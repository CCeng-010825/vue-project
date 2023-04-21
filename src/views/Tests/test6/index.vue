<template>
  <div class="main" style="margin-top: 10px">
    <!-- <h2>d3测试</h2> -->
    <div class="chartTooltip hidden">
      <p>
        <strong class="name" />
      </p>
    </div>

    <div id="tree" class="tree" />
  </div>
</template>

<script>
import * as d3 from 'd3'
import msg from '@/json/data'
var diagonal = d3.svg.diagonal().projection((d) => {
  return [d.x, d.y]
})
export default {
  data() {
    return {
      // root: {
      //   name: '中国',
      //   children: [
      //     {
      //       name: '江苏',
      //       children: [{ name: 'iiii' }]
      //     },
      //     { name: '贵州' },
      //     { name: '上海' }
      //   ]
      // },
      root: msg,
      tree: null,
      duration: 750,
      treeData: '',
      width: '',
      height: '',
      count: 0,
      zm: null
      //   separation: () => 1
    }
  },
  created() {
    // this.drawTree()
  },
  mounted() {
    this.height = document.getElementById('tree').offsetHeight
    this.width = document.getElementById('tree').offsetWidth
    // console.log(height, width)
    var tree = d3.layout.tree().size([this.height, this.width])
    console.log(tree)
    this.tree = tree
    this.drawTree()
  },
  methods: {
    drawTree() {
      // var height = document.getElementById('tree').offsetHeight
      // var width = document.getElementById('tree').offsetWidth
      // console.log(height, width)
      const clientWidth = document.documentElement.clientWidth
      const clientHeight = document.documentElement.clientHeight
      var tree = d3.layout
        .tree()
        .size([this.width, this.height])
        .separation(() => {
          // 定义邻居节点的距离
          return 1
        })
      console.log(tree)
      this.tree = tree
      var svg = d3
        .select('body')
        .select('#tree')
        .append('svg')
        .attr('width', 1560 + 'px')
        .attr('height', 800 + 'px')
        .call(
          (this.zm = d3.behavior
            .zoom()
            .scaleExtent([0.1, 100])
            .on('zoom', (d) => {
              // console.log(d3.event, 'd3')
              svg.attr(
                'transform',
                'translate(' +
                  d3.event.translate +
                  ')' +
                  'scale(' +
                  d3.event.scale +
                  ')'
              )
            }))
        )
        .append('g').attr('id', 'dangan-elements') // 给svg绑定zoom事件
        .attr('transform', 'translate(0, 100)')
      var h = document.getElementById('dangan-elements').style
      // console.log(h, 'dfghjklsdfghjkdfghjk')
      // h.width = 800
      // h.height = 600
      // const widthScale = clientWidth / 1560
      // const heightScale = clientHeight / 800
      //       svg.attr(
      //         'transform',
      //         `
      //     translate(${(1560 * (widthScale - 1)) / 2}, ${
      //   (800 * (heightScale - 1)) / 2
      // })
      //     scale(0.5, 0.5)
      // `
      //       )
      this.zm.translate([150, 0])
      //   console.log(svg)
      this.svg = svg
      this.root.x0 = this.height / 2
      this.root.y0 = 0
      function collapse(d) {
        if (d.children) {
          d._children = d.children
          d._children.forEach(collapse)
          d.children = null
        }
      }
      // 折叠根节点的每个孩子
      this.root.children.forEach(collapse)
      // 折叠之后要重绘
      //   console.log(that.root, 'root')
      this.update(this.root)
    },
    update(source) {
      console.log(this.root)
      var nodes = this.tree.nodes(this.root).reverse()
      var links = this.tree.links(nodes)
      nodes.forEach((d) => {
        d.y = d.depth * 180 // 树的x,y倒置了，所以这里Y其实是横向的
        // console.log(d );  // 获取到 展示的所有 中国浙江等
      })
      console.log(nodes, 'ppppp')
      var node = this.svg.selectAll('g.node').data(nodes, (d) => {
        console.log(this.count, 'count')
        console.log(d, 'd.name')
        return d.id || (d.id = `${++this.count}-${d.name}-${d.x}`)
      })
      var timer = null

      var nodeEnter = node
        .enter()
        .append('g')
        .attr('class', 'node')
        .style('cursor', 'pointer')
        .attr('transform', (d) => {
          console.log(source.y0, source.x0, 'll;;;;;;')
          return 'translate(' + source.x0 + ',' + source.y0 + ')'
        })
        .on('mouseover', (d) => {
          if (timer) clearTimeout(timer)
          d3.select('.chartTooltip')
            .transition()
            .duration(300)
            .style('opacity', 1)
            .style('display', 'block')
          // 从d3.event获取鼠标的位置
          var transform = d3.event
          var yPosition = transform.offsetY - 20
          var xPosition = transform.offsetX - 20
          // 将浮层位置设置为鼠标位置
          var chartTooltip = d3
            .select('.chartTooltip')
            .style('left', xPosition + 'px')
            .style('top', yPosition + 'px')
          // 更新浮层内容
          chartTooltip.select('.name').text(d.name)
          // 移除浮层hidden样式，展示浮层
          chartTooltip.classed('hidden', false)
        })
        // 添加mouseover事件
        .on('mouseout', () => {
          // 添加浮层hidden样式，隐藏浮层
          timer = setTimeout(() => {
            d3.select('.chartTooltip')
              .style('opacity', 0)
              .style('display', 'none')
            // d3.select('.chartTooltip').style('opacity', 0)
          }, 400)
        })
        .on('click', (d) => {
          console.log(d, 'd的')
          this.click(d)
        })
      d3.select('.chartTooltip')
        .on('mouseover', () => {
          if (timer) clearTimeout(timer)
          d3.select('.chartTooltip')
            .transition()
            .duration(300)
            .style('opacity', 1)
            .style('display', 'block')
        })
        .on('mouseout', () => {
          timer = setTimeout(() => {
            d3.select('.chartTooltip')
              .style('opacity', 0)
              .style('display', 'none')
          }, 400)
        })
      nodeEnter
        .append('circle')
        .attr('r', 1e-6)
        .style('fill', (d) => {
          return d._children ? '#f00' : '#fff'
        })
      // 标签文字距离
      nodeEnter
        .append('text')
        .attr('x', (d) => {
          return d.children || d._children ? 50 : 30
        })
        .attr('dy', '.35em')
        .attr('text-anchor', (d) => {
          return d.children || d._children ? 'end' : 'start'
        })
        .text((d) => {
          return d.name
        })
        .style('fill-opacity', 1)
        .style('font-size', '12px')
      nodeEnter
        .append('text')
        .attr('x', (d) => {
          return d.children || d._children ? 20 : 0
        })
        .attr('y', (d) => {
          return d.children || d._children ? 25 : 20
        })
        .attr('dy', '.35em')
        .attr('text-anchor', (d) => {
          return d.children || d._children ? 'end' : 'start'
        })
        .text((d) => {
          return d.name
        })
        .style('fill-opacity', 1)
        .style('font-size', '12px')
      var nodeUpdate = node
        .transition()
        .duration(this.duration)
        .attr('transform', (d) => {
          console.log(d.y, d.x, 'kjj;;;pp')
          return 'translate(' + d.x + ',' + d.y + ')'
        })
      nodeUpdate
        .select('circle')
        .attr('r', 4.5)
        .attr('r', (d) => {
          return d._children ? '5' : '5'
        })
        .style('fill', (d) => {
          return d._children ? '#f00' : '#f90'
        })
      nodeUpdate.select('text').style('fill-opacity', 1)
      var nodeExit = node
        .exit()
        .transition()
        .duration(this.duration)
        .attr('transform', (d) => {
          return 'translate(' + source.x + ',' + source.y + ')'
        })
        .remove()
      nodeExit.select('circle').attr('r', 1e-6)
      nodeExit.select('text').style('fill-opacity', 1)
      var link = this.svg.selectAll('path.link').data(links, (d) => {
        console.log(d.target, 'ssddddd')
        return d.target.id
      })
      link
        .enter()
        .insert('svg:path', 'g')
        .attr('class', 'link')
        .attr('fill', 'none')
        .attr('stroke', '#ccc')
        .attr('stroke-width', '2')
        .attr('d', (d) => {
          console.log(source, 'source')
          console.log(d, 'd')
          var o = { x: source.x0, y: source.y0 }
          return diagonal({ source: o, target: o })
        })
      link.transition().duration(this.duration).attr('d', diagonal)
      // (2-10) 折叠的链接，收缩到源节点处
      link
        .exit()
        .transition()
        .duration(this.duration)
        .attr('d', (d) => {
          var o = {
            x: source.x,
            y: source.y
          }
          return diagonal({ source: o, target: o })
        })
        .remove()
      // 把旧位置存下来，用以过渡
      nodes.forEach((d) => {
        d.x0 = d.x
        d.y0 = d.y
      })
      console.log(nodes, 'ppppppppppppppppppppppppppp')
    },
    click(d) {
      var height = document.getElementById('tree').offsetHeight
      var width = document.getElementById('tree').offsetWidth
      if (d.children) {
        d._children = d.children
        d.children = null
      } else {
        d.children = d._children
        d._children = null
      }
      console.log(d, 'd的问题')
      this.update(d) // 重新渲染
      // const a = this.zm.scale()
      // this.svg.attr(
      //   'transform',
      //   'translate(' +
      //     (width / 3 - d.y0 * a) +
      //     ', ' +
      //     (height / 2 - d.x0 * a) +
      //     ') scale(' +
      //     a +
      //     ')'
      // )
      // this.zm.translate([width / 3 - d.y0 * a, height / 2 - d.x0 * a]).scale(a)
    }
  }
}
</script>

<style scoped>
 .body {
        width: 100%;
        height: 100%;
        margin: 0;
        box-sizing: border-box;
        overflow: hidden;
      }
.main {
  width: 100%;
  height: 100%;
  /* width: 800px;
  height: 600px; */
  /* overflow: auto; */
}

.tree {
  width: 100%;
  height: 100%;
  /* overflow: auto; */
  /* width: 800px;
  height: 600px; */
}
.node {
  cursor: pointer;
}

.node circle {
  fill: none;
  stroke: #fff;
  stroke-width: 1.5px;
}

.node text {
  font: 10px sans-serif;
}

.link {
  fill: none;
  stroke: #ccc;
  stroke-width: 1.5px;
}

.link {
  fill: none;
  stroke: #ccc;
  stroke-width: 1.5px;
}
#dangan-elements{
  width:200px !important;
  height: 600px !important;
}

#tree {
  height: 100%;
  margin: 0 auto;
  /* background: #E0E0E0; */
  box-sizing: border-box;
  /* color:#f90 */
}

#tree svg {
  width: 100%;
  height: 100%;
  /* width: 800px;
  height: 600px; */
}
.chartTooltip {
  position: absolute;
  width: 200px;
  height: auto;
  padding: 10px;
  box-sizing: border-box;
  background-color: white;
  border-radius: 5px;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.4);
}

.chartTooltip.hidden {
  display: none;
}
.chartTooltip p {
  margin: 0;
  font-size: 14px;
  line-height: 20px;
  word-wrap: break-word;
}
</style>
