---
title: "如何给Highchart折线图添加阴影"
date: 2019-10-31T11:52:54+08:00
draft: false
toc: false
images:
tags: 
  - Highcharts
---
----

最近遇到一些使用Highcharts开发图表的需求，需要把图表设置成带shadow的。类似这样子的效果：

![](https://blog-pics.pek3b.qingstor.com/412803b3-c06b-4f7c-9a26-4011976f8dc1.png)

由于英文不太好，中文文档又不太齐全。害得我花了好半天的时间。配置在倒数第7行的代码

``` js
// 设置如下
{
  chart: {
    events: {
      // 加载页面的时候绘制一些按钮之类的东西
      load: function(event) {
        let x = event.target.chartWidth * 0.5;

        this.flashText = this.renderer
          .text(
            `<div align="right" ><div id="${item.key}" style="display:none;"><button  style="height:31px;width:84px;background:"#f7f7f7" class="btn green pull-right" data-toggle="modal"><font size="2"><b>detail info</b></font></button></div></div>`,
            x + 100,
            20,
            true
          )
          .attr({
            zIndex: 101
          })
          .add();
      },
      // 图表放大的时候
      selection: event => {
        if (event.xAxis) {
          let btn = document.getElementById(`${item.key}`);
          btn.style.display = "block";
          btn.removeEventListener("click", this.detailInfo);
          let min = Math.floor(event.xAxis[0].min);
          let max = Math.ceil(event.xAxis[0].max);
          this.startTime = this.getLogTimeWidthYearMonthDay[min];
          this.endTime = this.getLogTimeWidthYearMonthDay[max];
          btn.addEventListener("click", this.detailInfo);
        }
      }
    },
    zoomType: "x"
  },
  title: {
    text: item.title
  },
  subtitle: {
    text:
      document.ontouchstart === undefined
        ? "鼠标拖动可以进行缩放"
        : "手势操作进行缩放"
  },
  xAxis: {
    categories: this.getLogTime
  },
  tooltip: {
    dateTimeLabelFormats: {
      millisecond: "%H:%M:%S.%L",
      second: "%H:%M:%S",
      minute: "%H:%M",
      hour: "%H:%M",
      day: "%Y-%m-%d",
      week: "%m-%d",
      month: "%Y-%m",
      year: "%Y"
    }
  },
  yAxis: {
    title: {
      text: item.yAxisName
    }
  },
  legend: {
    enabled: false
  },
  plotOptions: {
    area: {
      fillColor: {
        linearGradient: {
          x1: 0,
          y1: 0,
          x2: 0,
          y2: 1
        },
        stops: [
          [0, Highcharts.getOptions().colors[0]],
          [
            1,
            Highcharts.Color(Highcharts.getOptions().colors[0])
              .setOpacity(0)
              .get("rgba")
          ]
        ]
      },
      marker: {
        radius: 1
      },
      lineWidth: 1,
      states: {
        hover: {
          lineWidth: 1
        }
      },
      threshold: null
    }
  },
  series: [
    {
      // 此处设置type等于area就可以应用plotOptions中area的属性了
      type: "area",
      data: this.chartData.map(it => {
        return it[item.key];
      })
    }
  ]
};
```