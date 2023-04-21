<template>
  <div id="tree" :class="['tree-container']" />
</template>
<script>
import * as d3 from 'd3'
const curTranslate = {
  x: 0,
  y: 0
}
export default {
  data() {
    return {
      dataset: {
        cname: '主节点',
        children: [{
          cname: '子节点1',
          children: [{
            cname: '孙子点1'
          }, {
            cname: '孙子点2'
          }]
        }, {
          cname: '子节点2'
        }, {
          cname: '子节点3'
        }]
      },
      gsize: null
    }
  },
  mounted() {
    this.initTree()
  },
  methods: {
    initTree() {
      var timer
      const treeContainer = document.getElementById('tree')
      var width = treeContainer.clientWidth
      var height = treeContainer.clientHeight
      var preTranslate = [0, 0]
      var svg = d3
        .select('#tree')
        .append('svg')
        .attr('width', width + 'px')
        .attr('height', height + 'px')

      var treeGroup = svg
        .append('g')
        .attr('transition', '.2s')
      const zoom = d3
        .zoom() // 拖拽与缩放
        .scaleExtent([0.1, 10])
        .on('zoom', zoomed)
      svg.call(zoom)

      function zoomed(event) {
        const transofrm = d3.zoomTransform(this)
        treeGroup.attr('transform', transofrm.translate(curTranslate.x, curTranslate.y))
      }
      var hierarchyData = d3.hierarchy(this.dataset).sum(function(d) {
        return d.value
      })

      var tree = d3
        .tree()
        .nodeSize([width / 6, height / 6.5]) // 以node为参考缩放，size ()以容器大小缩放，node节点过多会叠加
        .separation(function(a, b) { // 定义邻居节点的距离
          return 1
        })
      var treeData = tree(hierarchyData) // 获取布 局数据
      var toolsWrap = svg.append('g').attr('id', 'toolsWrap').attr('style', 'display: none;position:relative')

      toolsWrap.append('rect').attr('width', 50).attr('height', 15).attr('fill', '#000').on('click', function() {
        toolsWrap.attr('transform', `translate(0, 0)`).attr('style', 'display: none;')
      })
      toolsWrap.append('rect').attr('width', 50).attr('height', 15).attr('fill', 'red').attr('x', 50)
      toolsWrap.append('rect').attr('width', 50).attr('height', 15).attr('fill', 'green').attr('x', 100)

      var nodes = treeData.descendants() // 节点数据
      var links = treeData.links() // 连线数据
      treeGroup.append('g')
        .selectAll('path') // 创建节点之间的连线
        .data(links)
        .enter()
        .append('path')
        .attr('d', function(d) {
          var start = { x: d.source.y, y: d.source.x }
          var end = { x: d.target.y, y: d.target.x }
          return `M${start.y},${start.x}L${start.y},${end.x - 30}L${end.y},${end.x - 30}L${end.y},${end.x}`
        })
        .attr('fill', 'none')
        .attr('stroke', '#999')
        .attr('stroke-width', 2)
        .attr('stroke-dasharray', function(d) {
          return this.getTotalLength()
        })
        .attr('stroke-dashoffset', function(d) {
          return this.getTotalLength()
        })
        .transition()
        .duration(1500)
        .attr('stroke-dashoffset', 0).delay(function(d) {
          return d.source.depth * 800
        })

      var gs = treeGroup.append('g') // 生成每个节点
        .selectAll('g')
        .data(nodes)
        .enter()
        .append('g')
        .attr('style', 'cursor: pointer;')
        .attr('transform', function(d) {
          var cx = d.x
          var cy = d.y
          return `translate(${cx},${cy})`
        })
        .on('click', function(e) {
          const point = this.getBoundingClientRect() // 被点击节点的dom信息
          const gsize = treeGroup.node().getBoundingClientRect() // 整个group的信息
          preTranslate = treeGroup.attr('transform').match(/translate\(.*\)/g)[0].replace('translate(', '').replace(')', '').split('.')

          d3.zoomTransform(this).x = (gsize.x + width / 2 - (point.x + point.width / 2) + gsize.width / 2)
          d3.zoomTransform(this).y = (gsize.y + height / 2 - point.y)
          curTranslate.x = 0
          curTranslate.y = 0

          const curScale = d3.zoomTransform(this).k
          treeGroup
            .transition()
            .attr('transform', `translate(${gsize.x + width / 2 - (point.x + point.width / 2) + gsize.width / 2}, ${gsize.y + height / 2 - point.y}) scale(${curScale})`) // 将被点击的节点居中显示
        })
        .on('mouseenter', function(d, level) {
          if (timer) {
            clearTimeout(timer)
          }
          const curScale = d3.zoomTransform(svg).k || 0
          toolsWrap.attr('transform', `translate(0,0) scale(${curScale})`).attr('style', 'display: block;')
          this.appendChild(toolsWrap.node())
          const bbox = this.getBBox() // bbox 不会因为放大而宽高变化
          console.log(bbox)
          toolsWrap.attr('transform', `translate(${bbox.x}, ${bbox.height - 4}) scale(${curScale})`).attr('style', 'display: block;')
        })
        .on('mousemove', function(d, v) {
          // let point = this.getBoundingClientRect();
        }).on('mouseleave', function() {
          timer = setTimeout(() => {
            toolsWrap.attr('transform', `translate(0, 0)`).attr('style', 'display: none;')
          }, 1000)
        })

      gs.append('circle') // 创建节点的圆点
        .attr('r', 4)
        .attr('fill', '#bae637')
        .attr('stroke', '#000')
        .attr('stroke-width', 1)

      gs.append('text') // 创建节点对应的文本内容
        .attr('y', 20)
        .attr('dy', 10)
        .attr('fill', function(d) {
          return 'orange'
        })
        .attr('font-size', 18)
        .attr('style', (d) => {
          return !d.data.mainNode ? `transform : scale(${0.8})` : ''
        })
        .text(function(d) {
          return d.data.cname
        }).attr('x', function(d) {
          const bbox = this.getBBox() // 居中对齐
          return -bbox.width / 2
        }).each(function(d) {
          d3.select(this.parentNode)

            .insert('rect')
            .attr('width', this.getBBox().width + 20)
            .attr('height', this.getBBox().height + 5)
            .attr('fill', d => {
              return '#000'
            })
            .attr('stroke', d => {
              return '#333'
            })
            .attr('x', function(d) {
              const bbox = this.getBBox() // 居中对齐
              return -bbox.width / 2
            })
            .attr('y', 9).each(function(d) {
              this.parentNode.insertBefore(this, this.parentNode.children[0])
            })
            .attr('style', !d.data.mainNode ? `transform: scale(${0.8})` : '')
        })
      const gsize = treeGroup.node().getBoundingClientRect()
      curTranslate.x = width / 2
      curTranslate.y = height / 2 - gsize.height / 2
      treeGroup.attr('transform', `translate(${curTranslate.x}, ${curTranslate.y})`)
    },
    update() { // 重绘
      d3.selectAll('svg').remove()
      this.initTree()
    }
  }
}
</script>
  <style scoped lang="scss" >
  .tree-container {
    position: relative;
    width: 800px;
    height: 600px;
    user-select: none;
    border: 1px solid;
    background: linear- gradient(to top, transparent 12px, #d9d9d9 12px),
      linear-gradient(to left, transparent 12px, #d9d9d9 13px);
    background-size: 13px 13px;
  }
  </style>
