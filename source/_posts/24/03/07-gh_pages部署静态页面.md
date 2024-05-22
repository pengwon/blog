---
title: GitHub Pages部署静态页面
date: 2024-03-07 11:24:53
categories:
tags:
    - github
---

# GitHub Pages部署静态页面

GitHub Pages是GitHub提供的静态页面托管服务，可以用来托管个人博客、项目文档等静态页面。GitHub Pages支持Jekyll，可以使用Jekyll构建博客，也可以使用其他静态页面生成器。现在GitHub Pages也在公测通过工作流部署静态页面，可以通过GitHub Actions自动部署静态页面，这样页面构建工具就不再受约束，可以使用任何静态页面生成器。GitHub Pages还支持自定义域名，可以绑定自己的域名。我的[个人博客](https://blog.boringhex.top/)目前是托管在腾讯云COS上，使用GitHub Actions自动部署，主要考虑是加快国内访问速度，现在都是在公众号上发布文章了，后期考虑把博客迁到GitHub Pages上。

今天先把被动收入组合的盈亏情况搞个页面出来，部署到GitHub Pages上，方便查看。

> 警告：GitHub Pages 站点可以在 Internet 上公开，即使该站点的存储库是私有的。 如果站点的存储库中有敏感数据，要在发布前删除数据。

如果不需要对站点的生成过程进行任何控制，则建议在将更改推送到特定分支时发布站点。 可以指定要用作发布源的分支和文件夹。 源分支可以是存储库中的任何分支，源文件夹可以是源分支上的存储库根目录 (/)，也可以是源分支上的 /docs 文件夹。 将更改推送到源分支时，源文件夹中的更改将发布到 GitHub Pages 站点。

如果想使用 Jekyll 以外的生成过程，或者不想使用专用分支来保存已编译的静态文件，则建议编写 GitHub Actions 工作流来发布站点。 GitHub 为常见的发布方案提供入门工作流，以帮助编写工作流。

本文的静态页面比较简单，就选择特定分支部署的方式，其实直接使用`main`分支也可以，但为了跟代码有所区分，还有实践习惯，就新建一个`gh-pages`分支。

## 创建分支

在GitHub上创建一个分支，分支名是`gh-pages`，然后将分支切换到`gh-pages`。创建`gh-pages`后，GitHub会自动创建一个页面部署工作流，我们需要在仓库设置中选择是使用仓库根目录，还是使用`/docs`目录。我使用了根目录来进行部署。

![仓库设置](https://imgs.boringhex.top/blog/20240307111200.png)

<!-- more -->

## 部署静态页面

每次向`gh-pages`分支推送更改时，GitHub Pages都会构建站点并发布站点。使用仓库根目录作为站点源文件夹时，GitHub Pages会使用`index.html`作为站点的主页。如果没有`index.html`，GitHub Pages会使用`README`文件。

我在`gh-pages`分支上新建了一个`index.html`文件，内容如下：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Portfolio Profit Curve</title>
    <meta charset="utf-8">
    <script src="https://cdn.jsdelivr.net/npm/echarts@5/dist/echarts.min.js"></script>
</head>

<body>
    <div id="profitCurve" style="width: 100%;height:600px;"></div>
    <script>
        fetch('my-investment/data/portfolio-1_change_records.json')
            .then(response => response.json())
            .then(data => {
                var chartDom = document.getElementById('profitCurve');
                var myChart = echarts.init(chartDom);
                var option = {
                    tooltip: {
                        trigger: 'axis',
                        formatter: function (params) {
                            var date = params[0].axisValue;
                            var funds = data.find(record => record.date === date).fund_detail;
                            var tooltipItems = params.map(param => {
                                var fund = funds.find(fund => fund.fund_code === param.seriesName);
                                if (!fund) {
                                    return `${param.seriesName}: ${parseFloat(param.value).toFixed(2)}`;
                                }
                                var cumulativePL = (parseFloat(param.value) - fund.cost).toFixed(2);
                                var color = cumulativePL >= 0 ? 'red' : 'green';
                                return `${param.seriesName}: ${parseFloat(param.value).toFixed(2)} (Cost: ${fund.cost}, Cumulative P&L: <span style="color: ${color};">${cumulativePL}</span>)`;
                            });
                            return `${date}<br/>${tooltipItems.join('<br/>')}`;
                        }
                    },
                    legend: {
                        data: ['Total Value'].concat(data[0].fund_detail.map(fund => fund.fund_code))
                    },
                    grid: {
                        left: '3%',
                        right: '4%',
                        bottom: '3%',
                        containLabel: true
                    },
                    toolbox: {
                        feature: {
                            saveAsImage: {},
                            dataZoom: {},
                            restore: {}
                        }
                    },
                    xAxis: {
                        type: 'category',
                        boundaryGap: false,
                        data: data.map(record => record.date)
                    },
                    yAxis: {
                        type: 'value'
                    },
                    series: [
                        {
                            name: 'Total Value',
                            type: 'line',
                            data: data.map(record => (record.balance + record.fund_value_total).toFixed(2)),
                            smooth: true
                        },
                        ...data[0].fund_detail.map((fund, index) => ({
                            name: fund.fund_code,
                            type: 'line',
                            data: data.map(record => record.fund_detail[index].value.toFixed(2)),
                            smooth: true
                        }))
                    ]
                };
                myChart.setOption(option);
            });
    </script>
</body>

</html>
```

这个HTML使用ECharts JavaScript库显示投资组合利润曲线。文档结构是标准的，包括一个`head`部分，其中包含页面的标题和链接到ECharts库的链接，以及包含页面主要内容的`body`部分。

`body`包含一个id为`profitCurve`的`div`，这是图表将被渲染的地方。`div`的样式设置为占据其父元素宽度的100%，并且高度为600px。

JavaScript代码从位于`my-investment/data/portfolio-1_change_records.json`的JSON文件中获取数据。然后使用这些数据初始化一个ECharts实例并配置图表。

`option`对象包含图表的配置。`tooltip`属性配置用户将鼠标悬停在图表上的数据点时将显示的信息。`legend`属性指定将在图表图例中显示的数据系列的名称。`grid`，`toolbox`，`xAxis`和`yAxis`属性配置图表的外观和功能。

`series`属性是一个数组，指定将在图表上显示的数据系列。第一个系列代表投资组合的总价值，其余的系列代表投资组合中各个基金的价值。每个系列的数据都是从获取的JSON数据中提取的。

最后，调用ECharts实例上的`setOption`方法来应用配置并渲染图表。

部署后效果如下：

![被动1号组合盈亏曲线](https://imgs.boringhex.top/blog/20240307113534.png)

[gif]

提示框显示的是对应日期的组合净值、各个基金的净值、成本和累计盈亏。

点击[原文链接](https://pengwon.github.io/my-investment/)可以访问到这个页面😀
