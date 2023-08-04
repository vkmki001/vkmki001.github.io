---
title: "分析埋点数据提升客户转化"
date: 2023-08-02T17:02:23+08:00
draft: false
---



### 我们需要留住用户
在竞争激烈的互联网时代，作为管理者或者网站运营人员，我们都渴望吸引更多的用户并将留存在我们的网站上成为忠实用户，越来越多的忠实用户才能保障公司的营收有一个稳定的增长。留存用户即提升用户的转化，然而，不可否认的是，我们常常困惑于如何有效地提升用户转化率。尽管投入了大量的时间和精力进行优化和营销，但结果却并不理想。

提升用户转化（或者说降低用户流失）有很多种方式，比如可以让我们的网站/APP的首页更加引人注目、优化页面的加载速度、设计对用户更加友好的网页结构……但是有时这套组合拳打下来收效甚微，不仅让我们疑惑：难道我们所做的内容都是徒劳的吗？

### 关注用户在哪里流失
其实，在做此类工作时往往忽略了一个重点，不仅仅要关注用户最终有没有变现，或者是否触达了网站的核心功能（分享、下单、付款、关注等）；更重要的是关注客户是**在哪个位置离开的**，找到这个位置便能解释一部分客户“不喜欢”我们网站的原因，比如：
- 有80%以上的客户在刚进入网站后就跳出了，则大概率是网站加载速度过慢导致的。
- 有一些用户在首页点击`领取优惠券`icon后跳出了，没有产生`消费优惠券`的动作，则我们为了转化这部分用户需要尽可能把`消费优惠券`排在`使用优惠券`近的位置。
- 有一些在网站留存时间很长的用户最终也没有下单，问题可能产生在下单页面隐藏的比较深，不利于用户发现，这种情况也需要考虑页面布局的优化
- ……

为了能发现类似上述的问题，我们需要借助用户的行为**埋点信息数据**，帮我们发掘用户流失的点位。

### 什么是埋点
埋点数据是在应用或网站中插入的代码片段，用于追踪和记录用户行为数据。当用户进行特定操作时，埋点代码会触发数据采集并发送相应的信息到后台服务器。这些数据可以提供有关用户行为、偏好和交互的详细信息，有助于进行用户行为分析和改进产品。

埋点数据对于分析用户行为具有以下价值：
- 理解用户行为模式：通过分析埋点数据，可以了解用户在应用或网站上的实际行为，如页面浏览、按钮点击、搜索等操作，帮助产品团队了解用户如何使用产品和与之互动；

- 优化产品设计：埋点数据可以揭示产品的瓶颈和问题所在。通过分析用户行为数据，产品团队可以发现用户的痛点和需求，进而优化产品设计，提升用户体验；

- 追踪转化率和流失率：通过埋点数据，可以跟踪用户在不同阶段的转化率和流失率。例如，可以分析用户在注册、付费、升级等关键步骤上的转化情况，找出影响转化的瓶颈，并采取相应措施提高转化率。

接下来我将介绍如何利用埋点数据搭建用户在网站中的行为路径，使用户跳转路径可以直观地呈现。

### 使用桑基图实现埋点整合
为了更直观的展现用户如何跳转与在哪里流失，可以先将埋点数据按照不同点位区分，再将数据以桑基图的方式呈现出来。

>桑基图（Sankey diagram）是一种信息可视化图表，通过连接线和节点展示了数据的流动过程和量的变化关系。利用它可以显示数据流向：桑基图通过直观、清晰的方式展示了数据的流向和传递路径。它可以帮助人们追踪和理解复杂系统中的数据流动，从而更好地理解整个过程的动态和变化，并且建立相关的数据与数据之间的关联关系。

如下展示了一个桑基图的示例，这个示例中直观的展示了男女熬夜原因的分布情况：



<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Awesome-pyecharts</title>
            <script type="text/javascript" src="https://assets.pyecharts.org/assets/echarts.min.js"></script>

</head>
<body>
    <div id="dc34b65ff4f8476bb4ffb2dee9ff44d3" class="chart-container" style="width:900px; height:500px;"></div>
    <script>
        var chart_dc34b65ff4f8476bb4ffb2dee9ff44d3 = echarts.init(
            document.getElementById('dc34b65ff4f8476bb4ffb2dee9ff44d3'), 'white', {renderer: 'canvas'});
        var option_dc34b65ff4f8476bb4ffb2dee9ff44d3 = {
    "animation": true,
    "animationThreshold": 2000,
    "animationDuration": 1000,
    "animationEasing": "cubicOut",
    "animationDelay": 0,
    "animationDurationUpdate": 300,
    "animationEasingUpdate": "cubicOut",
    "animationDelayUpdate": 0,
    "color": [
        "#c23531",
        "#2f4554",
        "#61a0a8",
        "#d48265",
        "#749f83",
        "#ca8622",
        "#bda29a",
        "#6e7074",
        "#546570",
        "#c4ccd3",
        "#f05b72",
        "#ef5b9c",
        "#f47920",
        "#905a3d",
        "#fab27b",
        "#2a5caa",
        "#444693",
        "#726930",
        "#b2d235",
        "#6d8346",
        "#ac6767",
        "#1d953f",
        "#6950a1",
        "#918597"
    ],
    "series": [
        {
            "type": "sankey",
            "data": [
                {
                    "name": "\u7537"
                },
                {
                    "name": "\u5973"
                },
                {
                    "name": "\u6253\u6e38\u620f"
                },
                {
                    "name": "\u52a0\u73ed"
                },
                {
                    "name": "\u770b\u5267"
                }
            ],
            "links": [
                {
                    "source": "\u7537",
                    "target": "\u6253\u6e38\u620f",
                    "value": 57
                },
                {
                    "source": "\u7537",
                    "target": "\u52a0\u73ed",
                    "value": 13
                },
                {
                    "source": "\u7537",
                    "target": "\u770b\u5267",
                    "value": 30
                },
                {
                    "source": "\u5973",
                    "target": "\u6253\u6e38\u620f",
                    "value": 33
                },
                {
                    "source": "\u5973",
                    "target": "\u52a0\u73ed",
                    "value": 5
                },
                {
                    "source": "\u5973",
                    "target": "\u770b\u5267",
                    "value": 62
                }
            ],
            "left": "5%",
            "top": "5%",
            "right": "20%",
            "bottom": "5%",
            "nodeWidth": 20,
            "nodeGap": 30,
            "nodeAlign": "justify",
            "layoutIteration": 32,
            "orient": "horizontal",
            "draggable": true,
            "focusNodeAdjacency": false,
            "label": {
                "show": true,
                "position": "right",
                "margin": 8
            },
            "lineStyle": {
                "show": true,
                "width": 1,
                "opacity": 0.3,
                "curveness": 0.5,
                "type": "solid",
                "color": "source"
            }
        }
    ],
    "legend": [
        {
            "data": [
                ""
            ],
            "selected": {
                "": true
            },
            "show": true,
            "padding": 5,
            "itemGap": 10,
            "itemWidth": 25,
            "itemHeight": 14
        }
    ],
    "tooltip": {
        "show": true,
        "trigger": "item",
        "triggerOn": "mousemove|click",
        "axisPointer": {
            "type": "line"
        },
        "showContent": true,
        "alwaysShowContent": false,
        "showDelay": 0,
        "hideDelay": 100,
        "textStyle": {
            "fontSize": 14
        },
        "borderWidth": 0,
        "padding": 5
    },
    "title": [
        {
            "text": "\u71ac\u591c\u539f\u56e0\u6851\u57fa\u56fe",
            "padding": 5,
            "itemGap": 10
        }
    ]
};
        chart_dc34b65ff4f8476bb4ffb2dee9ff44d3.setOption(option_dc34b65ff4f8476bb4ffb2dee9ff44d3);
    </script>
</body>
</html>

---

桑基图更有价值的一点是使用埋点数据，展示不同点位之间的客户流转，比如可以看到有多少客户从A点位到B点位，为了做到这点，我们需要通过以下内容达成：
1. 数据预处理：收集到的埋点数据可能需要进行清洗和预处理，以便将其转换成适用于桑基图的格式。处理过程中可能需要对节点和连线进行标识和命名。

2. 数据转换与整理：将处理好的埋点数据转换成桑基图所需的数据结构。通常，数据结构应包括节点的名称、链接关系以及数据流量等信息。

3. 使用可视化工具绘制桑基图：选择合适的数据可视化工具，如Python的Matplotlib、JavaScript的D3.js等，使用转换后的数据，绘制桑基图。

4. 设置样式和交互效果：根据需要，自定义桑基图的样式和外观，并添加交互效果以增强用户体验。

### 数据解读

回到我们最开始的目标`提升用户转化`，现在需要对生成的桑基图进行分析和解读，从中获取洞察和结论。最终将结果分享给相关团队或利益相关者，帮助他们理解数据流程与关系，并支持数据驱动的决策。

下面这个案例是我之前帮助一家商业地产的APP绘制的用户转化路径，当时其实已经在和一家做客户线上运营产品的供应商合作，但是这家供应商的产品最多只能分析二到三层用户的路径转化，支撑不了更多层次的分析；所以自己是采用了先调取他们的埋点数据、再自己用Python实现桑基图绘制的方式。

---
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Awesome-pyecharts</title>
            <script type="text/javascript" src="https://assets.pyecharts.org/assets/echarts.min.js"></script>

</head>
<body>
    <div id="1b5b864101cb442983391cbd5f62b8f3" class="chart-container" style="width:900px; height:500px;"></div>
    <script>
        var chart_1b5b864101cb442983391cbd5f62b8f3 = echarts.init(
            document.getElementById('1b5b864101cb442983391cbd5f62b8f3'), 'white', {renderer: 'canvas'});
        var option_1b5b864101cb442983391cbd5f62b8f3 = {
    "animation": true,
    "animationThreshold": 2000,
    "animationDuration": 1000,
    "animationEasing": "cubicOut",
    "animationDelay": 0,
    "animationDurationUpdate": 300,
    "animationEasingUpdate": "cubicOut",
    "animationDelayUpdate": 0,
    "color": [
        "#c23531",
        "#2f4554",
        "#61a0a8",
        "#d48265",
        "#749f83",
        "#ca8622",
        "#bda29a",
        "#6e7074",
        "#546570",
        "#c4ccd3",
        "#f05b72",
        "#ef5b9c",
        "#f47920",
        "#905a3d",
        "#fab27b",
        "#2a5caa",
        "#444693",
        "#726930",
        "#b2d235",
        "#6d8346",
        "#ac6767",
        "#1d953f",
        "#6950a1",
        "#918597"
    ],
    "series": [
        {
            "type": "sankey",
            "data": [
                {
                    "name": "\u9996\u9875\u5f39\u7a97"
                },
                {
                    "name": "\u9996\u9875\u4e8c\u697c\u5165\u53e3"
                },
                {
                    "name": "\u9996\u9875banner"
                },
                {
                    "name": "\u9876\u90e8icon"
                },
                {
                    "name": "\u9650\u65f6\u79d2\u6740"
                },
                {
                    "name": "\u7cbe\u9009\u597d\u7269\u63a8\u8350"
                },
                {
                    "name": "\u70ed\u95e8\u5151\u6362\u597d\u793c"
                },
                {
                    "name": "\u670d\u52a1\u7a97icon"
                },
                {
                    "name": "\u5f00\u5c4f"
                },
                {
                    "name": "2_\u626b\u7801"
                },
                {
                    "name": "2_\u83b7\u53d6\u79ef\u5206"
                },
                {
                    "name": "2_\u4e13\u9898\u6d4f\u89c8"
                },
                {
                    "name": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875"
                },
                {
                    "name": "2_\u6d88\u8017\u79ef\u5206"
                },
                {
                    "name": "2_push\u63a8\u9001"
                },
                {
                    "name": "2_push点击"
                },
                {
                    "name": "2_\u9886\u53d6\u4f18\u60e0\u5238"
                },
                {
                    "name": "2_\u767b\u5f55"
                },
                {
                    "name": "3_\u8df3\u51fa"
                },
                {
                    "name": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb"
                },
                {
                    "name": "3_push\u63a8\u9001"
                },
                {
                    "name": "3_\u83b7\u53d6\u79ef\u5206"
                },
                {
                    "name": "3_push点击"
                },
                {
                    "name": "3_\u9886\u53d6\u4f18\u60e0\u5238"
                },
                {
                    "name": "3_\u63d0\u4ea4\u8ba2\u5355"
                },
                {
                    "name": "3_\u767b\u5f55"
                },
                {
                    "name": "4_\u8df3\u51fa"
                },
                {
                    "name": "4_\u83b7\u53d6\u79ef\u5206"
                },
                {
                    "name": "4_\u4e13\u9898\u6d4f\u89c8"
                },
                {
                    "name": "4_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875"
                },
                {
                    "name": "4_\u6d88\u8017\u79ef\u5206"
                },
                {
                    "name": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb"
                },
                {
                    "name": "4_push\u63a8\u9001"
                },
                {
                    "name": "4_push点击"
                },
                {
                    "name": "4_\u626b\u7801"
                },
                {
                    "name": "4_\u9886\u53d6\u4f18\u60e0\u5238"
                },
                {
                    "name": "4_\u767b\u5f55"
                },
                {
                    "name": "4_\u652f\u4ed8\u8ba2\u5355"
                },
                {
                    "name": "4_\u6838\u9500\u4f18\u60e0\u5238"
                },
                {
                    "name": "5_\u8df3\u51fa"
                },
                {
                    "name": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb"
                },
                {
                    "name": "5_push\u63a8\u9001"
                },
                {
                    "name": "5_\u6d88\u8017\u79ef\u5206"
                },
                {
                    "name": "5_push点击"
                },
                {
                    "name": "5_\u9886\u53d6\u4f18\u60e0\u5238"
                },
                {
                    "name": "5_\u83b7\u53d6\u79ef\u5206"
                },
                {
                    "name": "5_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875"
                },
                {
                    "name": "5_\u4e13\u9898\u6d4f\u89c8"
                }
            ],
            "links": [
                {
                    "source": "\u5f00\u5c4f",
                    "target": "2_\u4e13\u9898\u6d4f\u89c8",
                    "value": 4
                },
                {
                    "source": "\u670d\u52a1\u7a97icon",
                    "target": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "value": 181
                },
                {
                    "source": "\u670d\u52a1\u7a97icon",
                    "target": "2_\u83b7\u53d6\u79ef\u5206",
                    "value": 14
                },
                {
                    "source": "\u670d\u52a1\u7a97icon",
                    "target": "2_\u9886\u53d6\u4f18\u60e0\u5238",
                    "value": 30
                },
                {
                    "source": "\u70ed\u95e8\u5151\u6362\u597d\u793c",
                    "target": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "value": 43
                },
                {
                    "source": "\u7cbe\u9009\u597d\u7269\u63a8\u8350",
                    "target": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "value": 44
                },
                {
                    "source": "\u9650\u65f6\u79d2\u6740",
                    "target": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "value": 15
                },
                {
                    "source": "\u9876\u90e8icon",
                    "target": "2_push点击",
                    "value": 13
                },
                {
                    "source": "\u9876\u90e8icon",
                    "target": "2_push\u63a8\u9001",
                    "value": 141
                },
                {
                    "source": "\u9876\u90e8icon",
                    "target": "2_\u626b\u7801",
                    "value": 12
                },
                {
                    "source": "\u9876\u90e8icon",
                    "target": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "value": 9
                },
                {
                    "source": "\u9876\u90e8icon",
                    "target": "2_\u6d88\u8017\u79ef\u5206",
                    "value": 37
                },
                {
                    "source": "\u9876\u90e8icon",
                    "target": "2_\u767b\u5f55",
                    "value": 21
                },
                {
                    "source": "\u9876\u90e8icon",
                    "target": "2_\u83b7\u53d6\u79ef\u5206",
                    "value": 637
                },
                {
                    "source": "\u9876\u90e8icon",
                    "target": "2_\u9886\u53d6\u4f18\u60e0\u5238",
                    "value": 5
                },
                {
                    "source": "\u9996\u9875banner",
                    "target": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "value": 13
                },
                {
                    "source": "\u9996\u9875\u4e8c\u697c\u5165\u53e3",
                    "target": "2_\u4e13\u9898\u6d4f\u89c8",
                    "value": 14
                },
                {
                    "source": "\u9996\u9875\u5f39\u7a97",
                    "target": "2_\u626b\u7801",
                    "value": 6
                },
                {
                    "source": "\u9996\u9875\u5f39\u7a97",
                    "target": "2_\u83b7\u53d6\u79ef\u5206",
                    "value": 14
                },
                {
                    "source": "2_\u4e13\u9898\u6d4f\u89c8",
                    "target": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 10
                },
                {
                    "source": "2_\u4e13\u9898\u6d4f\u89c8",
                    "target": "3_\u8df3\u51fa",
                    "value": 8
                },
                {
                    "source": "2_push点击",
                    "target": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 13
                },
                {
                    "source": "2_push\u63a8\u9001",
                    "target": "3_push点击",
                    "value": 16
                },
                {
                    "source": "2_push\u63a8\u9001",
                    "target": "3_\u83b7\u53d6\u79ef\u5206",
                    "value": 7
                },
                {
                    "source": "2_push\u63a8\u9001",
                    "target": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 106
                },
                {
                    "source": "2_push\u63a8\u9001",
                    "target": "3_\u8df3\u51fa",
                    "value": 12
                },
                {
                    "source": "2_\u626b\u7801",
                    "target": "3_\u83b7\u53d6\u79ef\u5206",
                    "value": 4
                },
                {
                    "source": "2_\u626b\u7801",
                    "target": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 8
                },
                {
                    "source": "2_\u626b\u7801",
                    "target": "3_\u8df3\u51fa",
                    "value": 6
                },
                {
                    "source": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "target": "3_\u63d0\u4ea4\u8ba2\u5355",
                    "value": 16
                },
                {
                    "source": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "target": "3_\u767b\u5f55",
                    "value": 4
                },
                {
                    "source": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "target": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 240
                },
                {
                    "source": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "target": "3_\u8df3\u51fa",
                    "value": 22
                },
                {
                    "source": "2_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "target": "3_\u9886\u53d6\u4f18\u60e0\u5238",
                    "value": 23
                },
                {
                    "source": "2_\u6d88\u8017\u79ef\u5206",
                    "target": "3_push\u63a8\u9001",
                    "value": 6
                },
                {
                    "source": "2_\u6d88\u8017\u79ef\u5206",
                    "target": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 20
                },
                {
                    "source": "2_\u6d88\u8017\u79ef\u5206",
                    "target": "3_\u8df3\u51fa",
                    "value": 11
                },
                {
                    "source": "2_\u767b\u5f55",
                    "target": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 13
                },
                {
                    "source": "2_\u767b\u5f55",
                    "target": "3_\u8df3\u51fa",
                    "value": 8
                },
                {
                    "source": "2_\u83b7\u53d6\u79ef\u5206",
                    "target": "3_push点击",
                    "value": 4
                },
                {
                    "source": "2_\u83b7\u53d6\u79ef\u5206",
                    "target": "3_push\u63a8\u9001",
                    "value": 110
                },
                {
                    "source": "2_\u83b7\u53d6\u79ef\u5206",
                    "target": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 499
                },
                {
                    "source": "2_\u83b7\u53d6\u79ef\u5206",
                    "target": "3_\u8df3\u51fa",
                    "value": 46
                },
                {
                    "source": "2_\u83b7\u53d6\u79ef\u5206",
                    "target": "3_\u9886\u53d6\u4f18\u60e0\u5238",
                    "value": 6
                },
                {
                    "source": "2_\u9886\u53d6\u4f18\u60e0\u5238",
                    "target": "3_\u83b7\u53d6\u79ef\u5206",
                    "value": 4
                },
                {
                    "source": "2_\u9886\u53d6\u4f18\u60e0\u5238",
                    "target": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 23
                },
                {
                    "source": "2_\u9886\u53d6\u4f18\u60e0\u5238",
                    "target": "3_\u8df3\u51fa",
                    "value": 8
                },
                {
                    "source": "3_push点击",
                    "target": "4_push\u63a8\u9001",
                    "value": 8
                },
                {
                    "source": "3_push点击",
                    "target": "4_\u83b7\u53d6\u79ef\u5206",
                    "value": 4
                },
                {
                    "source": "3_push点击",
                    "target": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 8
                },
                {
                    "source": "3_push\u63a8\u9001",
                    "target": "4_push点击",
                    "value": 6
                },
                {
                    "source": "3_push\u63a8\u9001",
                    "target": "4_\u626b\u7801",
                    "value": 5
                },
                {
                    "source": "3_push\u63a8\u9001",
                    "target": "4_\u83b7\u53d6\u79ef\u5206",
                    "value": 7
                },
                {
                    "source": "3_push\u63a8\u9001",
                    "target": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 90
                },
                {
                    "source": "3_push\u63a8\u9001",
                    "target": "4_\u8df3\u51fa",
                    "value": 8
                },
                {
                    "source": "3_\u63d0\u4ea4\u8ba2\u5355",
                    "target": "4_\u652f\u4ed8\u8ba2\u5355",
                    "value": 10
                },
                {
                    "source": "3_\u63d0\u4ea4\u8ba2\u5355",
                    "target": "4_\u6d88\u8017\u79ef\u5206",
                    "value": 6
                },
                {
                    "source": "3_\u767b\u5f55",
                    "target": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 4
                },
                {
                    "source": "3_\u83b7\u53d6\u79ef\u5206",
                    "target": "4_push\u63a8\u9001",
                    "value": 7
                },
                {
                    "source": "3_\u83b7\u53d6\u79ef\u5206",
                    "target": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 8
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_\u4e13\u9898\u6d4f\u89c8",
                    "value": 11
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_push点击",
                    "value": 14
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_push\u63a8\u9001",
                    "value": 85
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_\u626b\u7801",
                    "value": 17
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "value": 236
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_\u6d88\u8017\u79ef\u5206",
                    "value": 53
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_\u767b\u5f55",
                    "value": 26
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_\u83b7\u53d6\u79ef\u5206",
                    "value": 377
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_\u8df3\u51fa",
                    "value": 98
                },
                {
                    "source": "3_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "4_\u9886\u53d6\u4f18\u60e0\u5238",
                    "value": 15
                },
                {
                    "source": "3_\u8df3\u51fa",
                    "target": "4_\u8df3\u51fa",
                    "value": 121
                },
                {
                    "source": "3_\u9886\u53d6\u4f18\u60e0\u5238",
                    "target": "4_\u6838\u9500\u4f18\u60e0\u5238",
                    "value": 13
                },
                {
                    "source": "3_\u9886\u53d6\u4f18\u60e0\u5238",
                    "target": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 16
                },
                {
                    "source": "4_\u4e13\u9898\u6d4f\u89c8",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 11
                },
                {
                    "source": "4_push点击",
                    "target": "5_push\u63a8\u9001",
                    "value": 6
                },
                {
                    "source": "4_push点击",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 14
                },
                {
                    "source": "4_push\u63a8\u9001",
                    "target": "5_push点击",
                    "value": 16
                },
                {
                    "source": "4_push\u63a8\u9001",
                    "target": "5_\u83b7\u53d6\u79ef\u5206",
                    "value": 4
                },
                {
                    "source": "4_push\u63a8\u9001",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 75
                },
                {
                    "source": "4_push\u63a8\u9001",
                    "target": "5_\u8df3\u51fa",
                    "value": 5
                },
                {
                    "source": "4_\u626b\u7801",
                    "target": "5_\u83b7\u53d6\u79ef\u5206",
                    "value": 13
                },
                {
                    "source": "4_\u626b\u7801",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 9
                },
                {
                    "source": "4_\u652f\u4ed8\u8ba2\u5355",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 10
                },
                {
                    "source": "4_\u6838\u9500\u4f18\u60e0\u5238",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 13
                },
                {
                    "source": "4_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "target": "5_\u83b7\u53d6\u79ef\u5206",
                    "value": 12
                },
                {
                    "source": "4_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 204
                },
                {
                    "source": "4_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "target": "5_\u8df3\u51fa",
                    "value": 11
                },
                {
                    "source": "4_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "target": "5_\u9886\u53d6\u4f18\u60e0\u5238",
                    "value": 9
                },
                {
                    "source": "4_\u6d88\u8017\u79ef\u5206",
                    "target": "5_\u83b7\u53d6\u79ef\u5206",
                    "value": 5
                },
                {
                    "source": "4_\u6d88\u8017\u79ef\u5206",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 46
                },
                {
                    "source": "4_\u6d88\u8017\u79ef\u5206",
                    "target": "5_\u8df3\u51fa",
                    "value": 8
                },
                {
                    "source": "4_\u767b\u5f55",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 22
                },
                {
                    "source": "4_\u767b\u5f55",
                    "target": "5_\u8df3\u51fa",
                    "value": 4
                },
                {
                    "source": "4_\u83b7\u53d6\u79ef\u5206",
                    "target": "5_push点击",
                    "value": 9
                },
                {
                    "source": "4_\u83b7\u53d6\u79ef\u5206",
                    "target": "5_push\u63a8\u9001",
                    "value": 36
                },
                {
                    "source": "4_\u83b7\u53d6\u79ef\u5206",
                    "target": "5_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "value": 7
                },
                {
                    "source": "4_\u83b7\u53d6\u79ef\u5206",
                    "target": "5_\u6d88\u8017\u79ef\u5206",
                    "value": 4
                },
                {
                    "source": "4_\u83b7\u53d6\u79ef\u5206",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 314
                },
                {
                    "source": "4_\u83b7\u53d6\u79ef\u5206",
                    "target": "5_\u8df3\u51fa",
                    "value": 18
                },
                {
                    "source": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "5_\u4e13\u9898\u6d4f\u89c8",
                    "value": 6
                },
                {
                    "source": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "5_push\u63a8\u9001",
                    "value": 28
                },
                {
                    "source": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "5_\u6d4f\u89c8\u5546\u54c1\u8be6\u60c5\u9875",
                    "value": 12
                },
                {
                    "source": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "5_\u6d88\u8017\u79ef\u5206",
                    "value": 19
                },
                {
                    "source": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "5_\u83b7\u53d6\u79ef\u5206",
                    "value": 43
                },
                {
                    "source": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "5_\u8df3\u51fa",
                    "value": 13
                },
                {
                    "source": "4_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "target": "5_\u9886\u53d6\u4f18\u60e0\u5238",
                    "value": 5
                },
                {
                    "source": "4_\u8df3\u51fa",
                    "target": "5_\u8df3\u51fa",
                    "value": 227
                },
                {
                    "source": "4_\u9886\u53d6\u4f18\u60e0\u5238",
                    "target": "5_\u8d44\u6e90\u4f4d\u70b9\u51fb",
                    "value": 15
                }
            ],
            "left": "5%",
            "top": "5%",
            "right": "20%",
            "bottom": "5%",
            "nodeWidth": 20,
            "nodeGap": 15,
            "nodeAlign": "justify",
            "layoutIteration": 32,
            "orient": "horizontal",
            "draggable": true,
            "focusNodeAdjacency": false,
            "label": {
                "show": true,
                "position": "right",
                "margin": 8
            },
            "lineStyle": {
                "show": true,
                "width": 1,
                "opacity": 0.3,
                "curveness": 0.5,
                "type": "solid",
                "color": "source"
            }
        }
    ],
    "legend": [
        {
            "data": [
                ""
            ],
            "selected": {
                "": true
            },
            "show": true,
            "padding": 5,
            "itemGap": 10,
            "itemWidth": 25,
            "itemHeight": 14
        }
    ],
    "tooltip": {
        "show": true,
        "trigger": "item",
        "triggerOn": "mousemove|click",
        "axisPointer": {
            "type": "line"
        },
        "showContent": true,
        "alwaysShowContent": false,
        "showDelay": 0,
        "hideDelay": 100,
        "textStyle": {
            "fontSize": 14
        },
        "borderWidth": 0,
        "padding": 5
    },
    "title": [
        {
            "padding": 5,
            "itemGap": 10
        }
    ]
};
        chart_1b5b864101cb442983391cbd5f62b8f3.setOption(option_1b5b864101cb442983391cbd5f62b8f3);
    </script>
</body>
</html>

---
从如上这个桑基图中可以在第一列点位中点击`顶部icon`的用户占据了绝大部分，在第二列点位中是`获取积分`，给我们的第一点启发就是在这两个点位附近设置更多、更已吸引客户下单的内容；
其次我们想让更多的客户在`获取积分`后，尽可能快的去`消耗积分`，但是图中展示的这种比例的客户并不多，给我们的第二点启发则是需要缩短`获取积分`与`消耗积分`之间的路径，增强转化的机会。

这种分析需要对分析人员先对网站的每个点位的功能、分布有所了解，清楚每个埋点动作带来的价值差异，才能合理地基于数据提出改善方向建议。

后面将分享使用Python搭建桑基图的代码示例。










