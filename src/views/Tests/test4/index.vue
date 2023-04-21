<template>
  <div class="main" style="margin-top: 10px">
    <el-card>
      <el-row>
        <el-col :span="6">
          <el-col :span="16">
            <el-input
              v-model="condition"
              size="small"
              placeholder="搜索用户名"
            />
          </el-col>
          <el-col :span="2" :offset="2">
            <el-button
              type="primary"
              size="small"
              @click="searchList"
            >搜索</el-button>
          </el-col>
        </el-col>
      </el-row>
    </el-card>
    <div class="chartTooltip hidden">
      <p>
        <strong class="name" />
      </p>
    </div>
    <div id="tree" class="tree" />
  </div>
</template>

<script>
// import OyUserApi from '@/api/systemManage/oyUserApi'
import * as d3 from 'd3'
import msg from '@/json/data'
// import Axios from '@/interceptor/HttpInterceptor'
var diagonal = d3.svg.diagonal().projection(function(d) {
  return [d.y, d.x]
})
export default {
  name: 'D3',
  components: {},
  data() {
    return {
      condition: '',
      root: {
        name: '中国', children: [{ name: '江苏' }, { name: '贵州' }, { name: '上海' }]
      },
      tree: null,
      duration: 700,
      treeData: '',
      count: 0,
      zm: null,
      separation: () => 1
    }
  },
  created() {},
  mounted() {
    const that = this
    var width = document.getElementById('tree').offsetWidth
    var height = document.getElementById('tree').offsetHeight
    console.log(height, width, 'kkkk')
    that.height = height
    that.width = width
    var tree = d3.layout.tree().size([height, width])
    that.tree = tree
    this.getData()
  },

  methods: {
    searchList() {
      const that = this
      const params = {
        condition: that.condition,
        layer: 1
      }
      if (that.condition === '') {
        this.$message.error('请输入用户名！')
        return
      }
      const http = new OyUserApi()
      http.getMemberProfile(params).then((res) => {
        if (res.code === 200) {
          that.root = res.data
          that.update(that.root)
        } else {
          this.$message.error('用户不存在！')
        }
      })
    },
    getData() {
      const that = this
      const params = {
        id: 1,
        layer: 1
      }

      // const http = new OyUserApi()
      // http.getSubMemberProfile(params).then(res => {
      //   if (res.code === 200) {
      //     that.root = res.data
      //     that.update(that.root)
      //   }
      // })
      // console.log(0 / 8)
      // console.log(msg)
      // that.root = msg
      console.log(msg)
      // that.update(that.root)
      that.DrawTree()
    },
    DrawTree() {
      const that = this
      var svg = d3
        .select('body')
        .select('#tree')
        .append('svg')
        .attr('width', 1560)
        .attr('height', 800)
        .call(
          (that.zm = d3.behavior
            .zoom()
            .scaleExtent([0.1, 100])
            .on('zoom', (d) => {
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
        .append('g') // 给svg绑定zoom事件
        .attr('transform', 'translate(150, 0)')
      console.log(svg)
      that.zm.translate([150, 0])
      that.svg = svg
      // 根节点和位置
      console.log(that, 'oooooo')
      that.root.x0 = that.height / 2
      that.root.y0 = 0
      // (1) 折叠函数，递归调用,有子孙的就把children（显示）给_children（不显示）暂存，便于折叠，
      function collapse(d) {
        if (d.children) {
          d._children = d.children
          d._children.forEach(collapse)
          d.children = null
        }
      }
      // 折叠根节点的每个孩子
      that.root.children.forEach(collapse)
      // 折叠之后要重绘
      console.log(that.root, 'root')
      this.update(that.root)
    },
    // (2) 更新布局
    update(source) {
      const that = this
      console.log(that, 'that')
      console.log(that.root)
      // (2-1) 计算新树的布局
      var nodes = that.tree.nodes(that.root).reverse()
      console.log(nodes)
      var links = that.tree.links(nodes)
      // (2-2) 树的深度这里树d.y。树的宽度最大720，要分四层，所以每层就乘180
      nodes.forEach(function(d) {
        d.y = d.depth * 180
      })
      // (2-3) 数据连接，根据id绑定数据
      var node = that.svg.selectAll('g.node').data(nodes, function(d) {
        return d.id || (d.id = ++that.count)
      })
      var div = d3.select('.chartTooltip').style('opacity', 0)
      var timer = null
      var nodeEnter = node
        .enter()
        .append('g')
        .attr('class', 'node')
        .style('cursor', 'pointer')
        .attr('transform', function(d) {
          return 'translate(' + source.y0 + ',' + source.x0 + ')'
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
          var yPosition = transform.layerY + 40
          var xPosition = transform.layerX + 40
          // 将浮层位置设置为鼠标位置
          var chartTooltip = d3
            .select('.chartTooltip')
            .style('left', xPosition + 'px')
            .style('top', yPosition + 'px')
          // 更新浮层内容
          // chartTooltip
          //   .select('.name')
          //   .html('用户名：' + d.name + '<br>' + '手机号：' + d.telephone)
          // 移除浮层hidden样式，展示浮层
          chartTooltip.classed('hidden', false)
        })
        // 添加mouseover事件
        .on('mouseout', () => {
          // 添加浮层hidden样式，隐藏浮层
          timer = setTimeout(function() {
            d3.select('.chartTooltip')
              .style('opacity', 0)
              .style('display', 'none')
          }, 400)
        })
        .on('click', (d) => that.click(d))
      d3.select('.chartTooltip')
        .on('mouseover', function() {
          if (timer) clearTimeout(timer)
          d3.select('.chartTooltip')
            .transition()
            .duration(300)
            .style('opacity', 1)
            .style('display', 'block')
        })
        .on('mouseout', function() {
          timer = setTimeout(function() {
            d3.select('.chartTooltip')
              .style('opacity', 0)
              .style('display', 'none')
          }, 400)
        })
      // 原点
      nodeEnter
        .append('circle')
        .attr('r', 1e-6)
        .style('fill', function(d) {
          return d.hasChildren ? '#f00' : '#BEBEBE'
        })

      // 文字 1
      nodeEnter
        .append('text')
        .attr('x', function(d) {
          return d.children || d._children ? 10 : 10
        })
        .attr('dy', '.35em')
        .attr('text-anchor', function(d) {
          // return d.children || d._children ? "end" : "start";
          return 'start'
        })
        .text(function(d) {
          return d.name
        })
        .style('fill-opacity', 1)
        .style('font-size', '12px')
      // (2-5) 原有节点更新到新位置
      var nodeUpdate = node
        .transition()
        .duration(that.duration)
        .attr('transform', function(d) {
          return 'translate(' + d.y + ',' + d.x + ')'
        })
      nodeUpdate
        .select('circle')
        .attr('r', 4.5)
        .attr('r', function(d) {
          return d._children ? '5' : '5'
        })
        .style('fill', function(d) {
          return d.hasChildren ? '#f00' : '#BEBEBE'
        })
      nodeUpdate.select('text').style('fill-opacity', 1)

      // (2-6) 折叠节点的子节点收缩回来
      var nodeExit = node
        .exit()
        .transition()
        .duration(that.duration)
        .attr('transform', function(d) {
          return 'translate(' + source.y + ',' + source.x + ')'
        })
        .remove()
      nodeExit.select('circle').attr('r', 1e-6)
      nodeExit.select('text').style('fill-opacity', 1)

      // (2-7) 数据连接，根据目标节点的id绑定数据
      var link = that.svg.selectAll('path.link').data(links, function(d) {
        return d.target.id
      })

      // (2-8) 增加新连接
      link
        .enter()
        .insert('svg:path', 'g')
        .attr('class', 'link')
        .attr('fill', 'none')
        .attr('stroke', '#ccc')
        .attr('stroke-width', '2')
        .attr('d', function(d) {
          var o = { x: source.x0, y: source.y0 }
          return diagonal({ source: o, target: o })
        })
      // (2-9) 原有连接更新位置

      link.transition().duration(that.duration).attr('d', diagonal)
      // (2-10) 折叠的链接，收缩到源节点处
      link
        .exit()
        .transition()
        .duration(that.duration)
        .attr('d', function(d) {
          var o = {
            x: source.x,
            y: source.y
          }
          return diagonal({ source: o, target: o })
        })
        .remove()
      // 把旧位置存下来，用以过渡
      nodes.forEach(function(d) {
        d.x0 = d.x
        d.y0 = d.y
      })
    },
    // (3) 切换折叠与否
    async click(d) {
      const that = this
      if (d.children) {
        d._children = d.children
        d.children = null
      } else if (d._children && d._children.length !== 0) {
        d.children = d._children
        d._children = null
      } else if (d.hasChildren) {
        var mnodes = await that.getNode(d)
      }
      if (d.hasChildren) {
        that.update(d)
      }
      // 点击节点 以当前节点位置居中
      const a = that.zm.scale()
      that.svg.attr(
        'transform',
        'translate(' +
          (that.width / 3 - d.y0 * a) +
          ', ' +
          (that.height / 2 - d.x0 * a) +
          ') scale(' +
          a +
          ')'
      )
      that.zm
        .translate([that.width / 3 - d.y0 * a, that.height / 2 - d.x0 * a])
        .scale(a)
    },
    getNode(d) {
      const that = this
      // const http = new OyUserApi()
      // const params = {
      //   id: d.id,
      //   layer: 1
      // }
      // // #自定义的一个新的以同步方式从后台取数据的请求函数
      // http.getSubMemberProfile(params).then((res) => {
      //   if (res.code === 200) {
      //     // d.children = res.data.children
      //     // d._children = null
      //     // that.update(d)
      //   }
      // })
      d.children = msg.children
      d._children = null
      that.update(d)
    }
  }
}
</script>

<style scoped>
.main {
  width: 100%;
  height: 99%;
  overflow: auto;
}
.tree {
  width: 100%;
  height: 100%;
  overflow: auto;
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

#tree {
  height: 100%;
  margin: 0 auto;
  /* background: #E0E0E0; */
  box-sizing: border-box;
}

#tree svg {
  width: 100%;
  height: 100%;
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
