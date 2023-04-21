<template>
  <div id="main" class="main" />
</template>

<script>
import * as echarts from 'echarts/core'
import { TooltipComponent } from 'echarts/components'
import { TreeChart } from 'echarts/charts'
import { CanvasRenderer } from 'echarts/renderers'
import msg from '@/json/data.json'
echarts.use([TooltipComponent, TreeChart, CanvasRenderer])
export default {
  data() {
    return {
      tooltipMessage: '',
      options: {
        tooltip: {
          trigger: 'item',
          triggerOn: 'mousemove',
          formatter: function(params) {
            // console.log(params.treeAncestors, 'params')
            // console.log(params.treeAncestors)
            // const dataArr = params.treeAncestors
            // console.log(ticket)
            // // const str = ''
            // // const message = dataArr.forEach(item =>return {item.name})
            // for (const item of dataArr) {
            //   if (item.name) {
            //     this.tooltipMessage = `${item.name}->`
            //   }
            // }
            // callback(ticket)
            console.log(params)
            return params.marker + params.data.name
            // console.log(message)
          }
          //   trigger: 'item',
          //   trigger: 'axis',
          //   triggerOn: 'mousemove',
          //   formatter: '{a}{b}{c}{d}'
          //   (params: Object|Array, ticket: string, callback: (ticket: string, html: string)) => string | HTMLElement | HTMLElement[]
        },
        selectMode: 'single',
        series: [
          {
            itemStyle: {
              //   color: '#556623'
            },
            labelLayout: {
              hideOverlap: true
            },
            type: 'tree',
            roam: true,
            data: [msg],
            top: '1%',
            left: '7%',
            bottom: '1%',
            right: '20%',
            symbolSize: 10,
            initialTreeDepth: 1,
            draggable: true,
            label: {
              position: 'left',
              verticalAlign: 'middle',
              align: 'right',
              fontSize: 9
              //   formatter: [
              //     '{a|这段文本采用样式a}',
              //     '{b|这段文本采用样式b}这段用默认样式{x|这段用样式x}'
              //   ].join('\n')
            },
            leaves: {
              label: {
                position: 'right',
                verticalAlign: 'middle',
                align: 'left'
              },
              select: {
                // ()=>{
                disabled: true
                //     console.log('oooooo')
                // }
                // function() {
                // }
              }
            },
            emphasis: {
              focus: 'none'
            },
            expandAndCollapse: true,
            animationDuration: 550,
            animationDurationUpdate: 750
          }
        ]
      }
    }
  },
  mounted() {
    this.draw()
  },
  methods: {
    draw() {
      var chartDom = document.getElementById('main')
      var myChart = echarts.init(chartDom)
      myChart.showLoading()
      this.getData()
      myChart.on('click', (data) => {
        console.log(data)
        // console.log('jdjfjfkkfkfkkfjfj')
      })
      myChart.hideLoading()
      myChart.setOption(this.options)
    },
    getData() {
      console.log(msg)
      msg.children.forEach(function(datum, index) {
        index % 2 === 0 && (datum.collapsed = true)
      })
    }
  }
}
</script>

<style>
.main {
  width: 50%;
  height: 500px;
}
</style>
