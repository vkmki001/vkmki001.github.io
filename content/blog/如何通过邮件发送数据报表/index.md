---
title: "如何通过邮件发送数据报表"
date: 2023-10-19T15:30:00+08:00
draft: false
tags: ["Python", "自动报表"]
---

### 背景

以前在工作中经常会做一些重复性的数据处理工作，这些工作难度不大但是比较耗时，便编写了一个自动取数处理、并最后通过邮件发送结果的脚本。每天可以定时收到数据报告而且轻易不会出错，对当时的工作效率提升还是蛮高的。   
当然，也可以通过这个方式设置一些重要的重复提醒，或者应用在其他依赖邮件交流的场景。   
代码不复杂，希望浏览后对您有帮助。


### 代码介绍
#### 数据清洗
因为数据存在远程服务器，这里用几条SQL把数据拉取过来，目的是为了获取注册数据和活跃数据。   
如果数据在本地，可以直接用pandas读取。

```python
#昨日注册 & 累计注册
sql_1 = '''
select "$dict_register_mall" as mall
       ,count(distinct case when from_unixtime(profile."register_date"/1000) >= date'{}'
                            then profile.xwho end) as register_ytd
       ,count(distinct profile.xwho) as register_acc
   from xxxx profile
  WHERE from_unixtime(profile."register_date"/1000) < (date '{}' + interval '1' day)
  group
  by 1
 '''

#昨日平台活跃人数 & APP活跃人数
#各项目
sql_2 = '''
select COALESCE(event."$dict_mall_id",'(无值)') as mall
      ,count(distinct event.distinct_id) as any_actv_user
      ,count(distinct case when event."$platform" in ('iOS','Android') then event.distinct_id end) app_actv_user
 FROM xxxx event
WHERE event.ds between cast(replace('{}','-','') as int)
  and cast(replace('{}','-','') as int)
               group
                  by 1
 '''

#整体
sql_2_2 = '''
select count(distinct event.distinct_id) as any_actv_user
      ,count(distinct case when event."$platform" in ('iOS','Android') then event.distinct_id end) app_actv_user
 FROM xxxx event
WHERE event.ds between cast(replace('{}','-','') as int)
  and cast(replace('{}','-','') as int)
  '''

 #历史每天注册量
sql_3 = '''
        select "$dict_register_mall" as mall
      ,date_trunc('day',from_unixtime(profile."register_date"/1000)) as day
      ,count(distinct profile.xwho) register_cnt
         FROM xxxx profile 
        where "$dict_register_mall" is not null
          and from_unixtime(profile."register_date"/1000) < (date '{}' + interval '1' day)
        group
           by 1,2
 '''

```


环境配置，后面会使用email.mime模块发送邮件。
```python
import csv
import time
import smtplib
import datetime
import json
import requests
import pandas as pd
import numpy as np

from apscheduler.schedulers.blocking import BlockingScheduler
from apscheduler.executors.pool import ProcessPoolExecutor
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.header import Header
```

{{< alert "code" >}}
[email.mime](https://docs.python.org/3/library/email.mime.html) 文档.
{{< /alert >}}



创建两个函数对象，`get_result()`用来建立与数据存储服务器的链接（根据服务器文档设置），`query()`用来结合上面的sql传递取数请求。
```python
def get_result(data):
    """API请求"""
    data = json.dumps(data,ensure_ascii=False).encode()
    headers = { "Content-Type": "application/json",
              "token":"xxxx",
              "appKey":"xxxx" }
    url = "xxxx"
    
    result = requests.post(url,data=data,headers=headers)
    return result


def query(sql):
    """传入SQL"""
    data = {"sql": sql,"format": "json"}
    result = get_result(data)
    return result.content
```

创建一个数据清洗的函数`data_extract()`, 将`query()`返回的结果处理成目标格式。
```python
def data_extract():
    """API调用&生成数据"""
    start_time = time.time()
    today = datetime.datetime.today() 
   
    yesterday = today - datetime.timedelta(days=1)
    year = datetime.date(today.year, 1, 1)
    mall = pd.read_csv('mall_list.csv', encoding='gbk')

    #创建今年1月1号到昨天的日期列表
    day_list = pd.DataFrame(pd.date_range(year,yesterday,freq='d'), columns = ['day'])
    day_list['day'] = day_list.day.apply(lambda x : x.strftime("%Y-%m-%d"))
    day_list['key'] = 1

    #昨日注册 & 累计注册
    sql_register = sql_1.format(yesterday.strftime("%Y-%m-%d"), yesterday.strftime("%Y-%m-%d"))
    query_register  = query(sql_register)
    data_register = json.loads('['+query_register.decode().replace('}\n{', '},{')+']')
    df_register = pd.DataFrame(data_register)
    mall2 =  mall.merge(df_register, how = 'left', on = 'mall')
    print('昨日注册 & 累计注册汇总完毕。')

    #昨日平台活跃人数 & APP活跃人数
    #各项目
    sql_act_ytd = sql_2.format(yesterday.strftime("%Y-%m-%d"), yesterday.strftime("%Y-%m-%d"))
    query_act_ytd  = query(sql_act_ytd)
    data_act_ytd = json.loads('['+query_act_ytd.decode().replace('}\n{', '},{')+']')
    df_act_ytd = pd.DataFrame(data_act_ytd)
    mall3 =  mall2.merge(df_act_ytd, how = 'left', on = 'mall')
    #整体
    sql_act_ytd_all = sql_2_2.format(yesterday.strftime("%Y-%m-%d"), yesterday.strftime("%Y-%m-%d"))
    query_act_ytd_all  = query(sql_act_ytd_all)
    data_act_ytd_all = json.loads('['+query_act_ytd_all.decode().replace('}\n{', '},{')+']')
    df_act_ytd_all = pd.DataFrame(data_act_ytd_all)
    #昨日活跃率
    mall3['any_actv_rate_ytd'] = mall3.any_actv_user / mall3.register_acc
    mall3['app_actv_rate_ytd'] = mall3.app_actv_user / mall3.register_acc
    print('昨日平台活跃人数 & APP活跃人数汇总完毕。')

    #昨日历史累计注册
    sql_register_hstry = sql_3.format(yesterday.strftime("%Y-%m-%d"))
    query_register_hstry  = query(sql_register_hstry)
    data_register_hstry = json.loads('['+query_register_hstry.decode().replace('}\n{', '},{')+']')
    df_register_hstry = pd.DataFrame(data_register_hstry)

    df_register_hstry = pd.DataFrame(df_register_hstry.groupby('mall').register_cnt.sum()).reset_index()
    df_register_hstry['day_y'] = yesterday.strftime("%Y-%m-%d")

    #拼接历史注册数据
    df_register_byday = pd.read_csv('cumulative_register_by_day_update.csv', encoding = 'gbk')
    #清除昨天之后的数据
    df_register_byday = df_register_byday[df_register_byday.day_y < yesterday.strftime("%Y-%m-%d")]
    #拼接昨日累计注册数据
    df_register_byday = pd.concat([df_register_hstry,df_register_byday])
    #校验是否有缺失活跃数据
    if not df_register_byday[df_register_byday.day_y >= year.strftime("%Y-%m-%d")].day_y.nunique() == day_list.day.nunique():
        print('各项目累计注册数据缺失！')
    else:
        print('各项目累计注册数据已通过校验。')
        df_register_byday.to_csv('cumulative_register_by_day_update.csv', encoding = 'gbk', index = False)

    #当年平均活跃率统计 各项目
    #首先读取历史活跃数据
    df_act_byday_parent = pd.read_csv('act_hstry_update.csv', encoding = 'gbk')
    df_act_byday_parent.drop('Unnamed: 0', axis = 1, inplace = True)
    #清除昨天之后的数据
    df_act_byday_parent = df_act_byday_parent[df_act_byday_parent.day < yesterday.strftime("%Y-%m-%d")]
    #拼接昨日活跃数据
    df_act_cmb = pd.concat([df_act_ytd,df_act_byday_parent])
    df_act_cmb.loc[df_act_cmb.day.isnull(), 'day'] = yesterday.strftime("%Y-%m-%d")
    #校验是否有缺失活跃数据
    if not df_act_cmb[df_act_cmb.day >= year.strftime("%Y-%m-%d")].day.nunique() == day_list.day.nunique():
        print('各项目每日活跃数据缺失！')
    else:
        print('各项目每日活跃数据已通过校验。')
        df_act_cmb.to_csv('act_hstry_update.csv', encoding = 'gbk')
    df_act_cmb = df_act_cmb.merge(df_register_byday, how = 'left', left_on = ['mall','day'], right_on = ['mall','day_y'])
    df_act_cmb.drop('day_y', axis = 1, inplace = True)
    df_act_cmb['any_actv_rate'] = df_act_cmb.any_actv_user / df_act_cmb.register_cnt
    df_act_cmb['app_actv_rate'] = df_act_cmb.app_actv_user / df_act_cmb.register_cnt
    #删除上线之前的数据
    df_act_cmb = mall.merge(df_act_cmb, how = 'left', on = 'mall')
    df_act_cmb = df_act_cmb[df_act_cmb.day>= df_act_cmb.publish_date_2]

    actv_rate1 = pd.DataFrame(df_act_cmb.groupby('mall').any_actv_rate.mean()).reset_index()
    actv_rate2 = pd.DataFrame(df_act_cmb.groupby('mall').app_actv_rate.mean()).reset_index()
    actv_rate1_avg_year = actv_rate1.merge(actv_rate2, how = 'left', on = 'mall')

    mall4 =  mall3.merge(actv_rate1_avg_year, how = 'left', on = 'mall')    

    #当年平均活跃率统计 整体
    #首先读取历史活跃数据
    df_act_byday_all_parent = pd.read_csv('all_act_hstry_update.csv', encoding = 'gbk')
    df_act_byday_all_parent.drop('Unnamed: 0', axis = 1, inplace = True)
    #清除昨天之后的数据
    df_act_byday_all_parent = df_act_byday_all_parent[df_act_byday_all_parent.day < yesterday.strftime("%Y-%m-%d")]
    #拼接昨日活跃数据
    df_act_cmb_all = pd.concat([df_act_ytd_all,df_act_byday_all_parent])
    df_act_cmb_all.loc[df_act_cmb_all.day.isnull(), 'day'] = yesterday.strftime("%Y-%m-%d")
    #校验是否有缺失活跃数据
    if not df_act_cmb_all[df_act_cmb_all.day >= year.strftime("%Y-%m-%d")].day.nunique() == day_list.day.nunique():
        print('整体每日活跃数据缺失！')
    else:
        print('整体每日活跃数据已通过校验。')
        df_act_cmb_all.to_csv('all_act_hstry_update.csv', encoding = 'gbk')
    df_register_byday_all = pd.DataFrame(df_register_byday.groupby('day_y').register_cnt.sum()).reset_index()
    df_act_cmb_all = df_act_cmb_all.merge(df_register_byday_all, how = 'left', left_on = ['day'], right_on = ['day_y'])
    df_act_cmb_all.drop('day_y', axis = 1, inplace = True)
    df_act_cmb_all['any_actv_rate'] = df_act_cmb_all.any_actv_user / df_act_cmb_all.register_cnt
    df_act_cmb_all['app_actv_rate'] = df_act_cmb_all.app_actv_user / df_act_cmb_all.register_cnt

    #构建 “合计” 行
    df_heji = pd.DataFrame(
                            [
                                [mall4['rank'].max()+1,
                                '合计',
                                ' ',
                                mall4.register_ytd.sum(),
                                df_act_cmb_all[df_act_cmb_all.day == yesterday.strftime("%Y-%m-%d")].any_actv_rate[0],
                                df_act_cmb_all[df_act_cmb_all.day == yesterday.strftime("%Y-%m-%d")].app_actv_rate[0],
                                df_register_byday[df_register_byday.day_y == yesterday.strftime("%Y-%m-%d")].register_cnt.sum(),
                                df_act_cmb_all[df_act_cmb_all.day >= year.strftime("%Y-%m-%d")].any_actv_rate.mean(),
                                df_act_cmb_all[df_act_cmb_all.day >= year.strftime("%Y-%m-%d")].app_actv_rate.mean()
                                ]
                            ],
                            columns = ['rank','mall','publish_date','register_ytd','any_actv_rate_ytd','app_actv_rate_ytd',
                                    'register_acc','any_actv_rate','app_actv_rate'
                                    ]
                        )

    #结果数据
    mall5 = pd.concat([mall4[['rank','mall','publish_date','register_ytd','any_actv_rate_ytd','app_actv_rate_ytd',
                                    'register_acc','any_actv_rate','app_actv_rate'
                                    ]],df_heji], ignore_index = True)

    mall5['any_actv_rate_ytd'] = mall5['any_actv_rate_ytd'].apply(lambda x: format(x, '.2%')) 
    mall5['app_actv_rate_ytd'] = mall5['app_actv_rate_ytd'].apply(lambda x: format(x, '.2%'))
    mall5['any_actv_rate'] = mall5['any_actv_rate'].apply(lambda x: format(x, '.2%'))
    mall5['app_actv_rate'] = mall5['app_actv_rate'].apply(lambda x: format(x, '.2%'))


    mall5.rename(columns = {'rank':'序号','mall':'项目','publish_date':'上线日期'
                            ,'register_ytd':'{}会员注册'.format(yesterday.strftime("%Y-%m-%d"))
                            ,'any_actv_rate_ytd':'{}平台日活跃率'.format(yesterday.strftime("%Y-%m-%d"))
                            ,'app_actv_rate_ytd':'{}APP日活跃率'.format(yesterday.strftime("%Y-%m-%d"))
                            ,'register_acc':'历史累计会员注册'
                            ,'any_actv_rate':'年平均日活跃率'
                            ,'app_actv_rate':'年APP日活跃率'}, inplace = True)
    end_time = time.time()
    print("耗时: {:.2f}秒".format(end_time - start_time))

    return mall5, yesterday
```

将处理完的结果转换成excel并保存到目标路径。
```python
def create_excel_file():
    """调取生成数据文件"""
    mall5, yesterday = data_extract()
    mall5.to_excel("./active_rate_record/{}_运营日报数据.xlsx".format(yesterday.strftime("%Y%m%d")), index = False)
    return yesterday
```

#### 部署邮件任务
添加邮件发送函数`send_email()`.
```python
def send_email(csv_file, from_day):
    """
    获取文件，发送邮件
    :param csv_file: 文件路径+文件名
    :param from_day: 日期参数，用于填充邮件内容
    :return:
    """
    if not csv_file:
        return
    else:

        subject = from_day.strftime("%Y-%m-%d") + '运营日报数据' #邮件主题
        #邮件正文
        main_content = "您好：" + "\n" + \                      
                       "\n" + \
                       "      本邮附件为" + from_day.strftime("%Y-%m-%d") + "运营日报数据。" + "\n" + \
                       "      请使用网页版Coremail查看。\n" + \
                       "\n" + \
                       "\n" + \
                       "\n" + \
                       "诚挚问候" + '\n' +\
                       "-----------------------" + "\n" \

        mail_host = "xxxx"  # 设置服务器
        mail_user = "xxxx@xxxx"  # 用户名
        mail_pass = "xxxx"  # 密码

        sender = 'jitaiyi@crland.com.cn'

        receivers = [
            # 收件人邮箱地址，可多个
            'xxxx@xxxx'
        ]

        message = MIMEMultipart()
        message['From'] = "xxxx@xxxx" #发件人邮箱
        message['Subject'] = Header(subject, 'utf-8')

        message.attach(MIMEText(main_content, 'plain', 'utf-8'))

        att1 = MIMEText(open(csv_file, 'rb').read(), 'base64', 'utf-8')
        att1["Content-Type"] = 'application/octet-stream'

        att1.add_header('Content-Disposition', 'attachment', filename=('gbk', '', subject+'.xlsx'))
        message.attach(att1)

        try:
            smtpObj = smtplib.SMTP()
            smtpObj.connect(mail_host, xxxx)  # xxxx 为 SMTP 端口号
            smtpObj.login(mail_user, mail_pass)
            smtpObj.sendmail(sender, receivers, message.as_string())
            print(subject + " ---> 日报邮件发送成功")
        except Exception as e:
            print("Error:%s " % e + subject + " ---> 日报邮件发送失败")
```

通过`email_job()`函数将结果文件传入邮件发送函数。
```python
def email_job():
    """
    组装执行步骤，形成邮件发送任务
    :return:
    """
    
    try:
        yesterday = create_excel_file()
        excel_file = "{}_运营日报数据.xlsx".format(yesterday.strftime("%Y%m%d"))
        send_email(excel_file, yesterday)
    except Exception as e:
        print("send email error! %s, %s" % (e, datetime.datetime.now()))
```

通过`job_manager()`设计任务执行频率与时间。
```python
def job_manager():
    """每日发送定时数据"""
    scheduler = BlockingScheduler()
    executors = {
        'default': {'type': 'threadpool', 'max_workers': 10},
        'processpool': ProcessPoolExecutor(max_workers=10)
    }
    job_defaults = {
        'coalesce': False,
        'max_instances': 10
    }
    scheduler.configure(executors=executors, job_defaults=job_defaults)

    # 加载数据
    basic_available_stats = [
        {'function': email_job, 'enable': True, 'params': {}},
    ]

    # 设定任务
    for rec in basic_available_stats:
        if not rec.get('enable'):
            continue
        scheduler.add_job(rec.get('function'), kwargs=rec.get('params'),
                          trigger='cron', hour=13, minute=0, max_instances=10, misfire_grace_time=14400)

    # 任务执行
    scheduler.start()
```
脚本执行。
```python
if __name__ == '__main__':
    
    create_excel_file()

    yesterday = today - datetime.timedelta(days=1)
    file= "{}_一点万象运营日报数据.xlsx".format(yesterday.strftime("%Y%m%d"))
    send_email(file, yesterday)

    # 执行定时任务
    job_manager()
```
        
    



