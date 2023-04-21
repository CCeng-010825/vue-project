<template>
  <div>
    <el-button
      v-show="$store.state.user.buttons.indexOf('btn.Add1') != -1"
      type="primary"
    >添加按钮1</el-button>
    <el-button
      v-show="$store.state.user.buttons.indexOf('btn.Add2') != -1"
      type="primary"
    >添加按钮2</el-button>
    <div ref="canvas" class="drawing-board">
      <canvas />
    </div>
    <el-button @click="save">保存</el-button>
    <el-button @click="cancel">取消</el-button>
    <div class="father clearFix">
      <!-- <p>float测试</p> -->
      <!-- <span>几个字</span>
      <span class="p1">float</span>
      <span class="p1">float</span> -->
      <!-- <div class="normal" /> -->
      <div class="normal" />

      <!-- <div style="clear:both;" /> -->
      <span>ydeguuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuu
        bhjkkkkkjkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkk
      </span>
      <!-- <div class="normal1" /> -->
      <!-- <div class="p1">iii</div> -->
    <!-- <div style="clear:both;" />  -->
    <!-- <div class="p2">
        11111111111111111111很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字很长的文字
      </div> -->

    </div>
    <!-- <div class="father clearFix">
      <div class="left">
        左浮动
      </div>
      <div class="right">
        右浮动
      </div>
    </div>
    <div class="footer">
      底部块级元素
    </div> -->
  </div>
</template>

<script>
const client = {
  offsetX: 0, // 偏移量
  offsetY: 0,
  endX: 0, // 坐标
  endY: 0
}
const mobileStatus = /Mobile|Android|iPhone/i.test(navigator.userAgent)

let canvas, ctx
import Bus from '@/common/bus'

export default {
  data() {
    return {
      config: {
        width: 400,
        height: 200,
        lineWidth: 5,
        strokeStyle: 'red',
        lineCap: 'round',
        lineJoin: 'round'
      },
      updateSide: true
    }
  },
  // 侧边栏收起展开，pagex和pagey发生变化，通过检测数值变化，解决canvas画板外面产生路径的bug
  watch: {
    updateSide(value) {
      // console.log(value)
      // this.resetSize()
      // ctx.moveTo(client.endX - client.offsetX - 140, client.endY - client.offsetY)
      if (!value) {
        // ctx.moveTo(client.endX - client.offsetX - 140, client.endY - client.offsetY)
        // console.log('false')
      }
    }
    // 'toggleSidebar': {
    //   handler() {
    //     console.log('999')
    //   }
    // }
    // Bus.$on(()=>{
    //   console.log('{{{{}}}}')
    // })
  },
  created() {
    // const canvas = document.querySelector('canvas')
    // const ctx = canvas.getContext('2d')
    // console.log(canvas)
    // console.log(ctx)
    // this.initCanvas()
  },
  mounted() {
    this.initCanvas()
    Bus.$on('updateSide', () => {
      // console.log('{{{{}}}}')
      this.updateSide = !this.updateSide
    })
    console.log(0.1 + 0.2)
  },
  methods: {
    initCanvas() {
      canvas = document.querySelector('canvas')
      const { left, top } = this.$refs.canvas.getBoundingClientRect()
      console.log(left, top)
      canvas.width = this.config.width
      canvas.height = this.config.height
      client.offsetX = left
      client.offsetY = top
      canvas.style.border = '1px solid #000'
      ctx = canvas.getContext('2d')
      // console.log(ctx)
      ctx.fillStyle = 'transparent'
      ctx.fillRect(0, 0, this.config.width, this.config.height)
      window.addEventListener('mousedown', this.init)
      window.addEventListener('mouseup', this.closeDraw)
      window.addEventListener('resize', this.resetSize)
    },
    init(event) {
      // console.log(event)
      const { offsetX, offsetY, pageX, pageY } = mobileStatus
        ? event.changedTouches[0]
        : event
      // console.log(offsetX, 'offSetX')
      // console.log(offsetY, 'offSetY')
      // console.log(pageX, 'pageX')
      // console.log(pageY, 'pageY')
      // client.offsetX = offsetX
      // client.offsetY = offsetY
      client.endX = pageX
      client.endY = pageY
      ctx.beginPath()
      ctx.lineWidth = this.config.lineWidth
      ctx.strokeStyle = this.config.strokeStyle
      ctx.lineCap = this.config.lineCap
      ctx.lineJoin = this.config.lineJoin
      // 设置起始点位
      // ctx.moveTo(client.endX - client.offsetX, client.endY - client.offsetY)

      ctx.moveTo(
        this.updateSide === true
          ? client.endX - client.offsetX
          : client.endX - client.offsetX + 154,
        client.endY - client.offsetY
      )
      console.log(client.endX - client.offsetX, client.endY - client.offsetY)
      window.addEventListener('mousemove', this.draw)
    },
    draw(event) {
      const { pageX, pageY } = mobileStatus ? event.changedTouches[0] : event
      client.endX = pageX
      client.endY = pageY
      ctx.lineTo(
        this.updateSide === true
          ? client.endX - client.offsetX
          : client.endX - client.offsetX + 154,
        client.endY - client.offsetY
      )
      // ctx.lineTo(client.endX - client.offsetX, client.endY - client.offsetY)
      console.log(
        client.endX - client.offsetX,
        client.endY - client.offsetY,
        'end'
      )
      ctx.stroke()
      // Bus.$on(() => {
      //   console.log('{{{{}}}}')
      // })
    },
    closeDraw() {
      // 结束绘制
      ctx.closePath()
      // 移除鼠标移动或手势移动监听器
      window.removeEventListener('mousemove', this.draw)
    },
    cancel() {
      ctx.clearRect(0, 0, this.config.width, this.config.height)
    },
    resetSize(event) {
      // const { left, top } = document.querySelector('canvas').getBoundingClientRect()
      canvas = document.querySelector('.drawing-board')
      const { width, height, left, top } =
        this.$refs.canvas.getBoundingClientRect()
      // const { width, height, left, top } = $('.drawing-board').getBoundingClientRect()
      // console.log(width, 'width')
      // console.log(height, 'height')
      // // console.log(left, top)
      // console.log(left, top, 'ppp')
      // const dx = Math.abs(left - client.offsetX)
      // const dy = Math.abs(top - client.offsetY)
      this.config.width = width
      this.config.height = height
      client.offsetX = left
      client.offsetY = top
      // 重置canvas宽高 会导致 之前canvas的内容丢失，得先存一份，重置完宽高，再绘制上去
      // const canvasData = ctx.getImageData(0, 0, canvas.width, canvas.height)
      // 重置 canvas 宽高
      canvas.width = width
      canvas.height = height
      // 改变完宽高 重绘画布
      // ctx.putImageData(canvasData, dx, dy)
    },
    save() {
      // 将canvas上的内容转成blob流
      canvas.toBlob((blob) => {
        // 获取当前时间并转成字符串，用来当做文件名
        const date = Date.now().toString()
        // 创建一个 a 标签
        const a = document.createElement('a')
        // 设置 a 标签的下载文件名
        a.download = `${date}.png`
        // 设置 a 标签的跳转路径为 文件流地址
        a.href = URL.createObjectURL(blob)
        // 手动触发 a 标签的点击事件
        a.click()
        console.log(blob, 'blob')
        console.log(a, 'a')
        // 移除 a 标签
        a.remove()
      })
    }
  }
}
</script>

<style></style>
<!-- <template>
  <div style="width: 100%;height: 100%;">
    <div ref="canvas" class="drawing-board">
      <canvas id="canvas" />
    </div>
    <div class="tool-bar">
      <el-button size="small" type="primary" @click="reset()">清除</el-button>
      <el-button size="small" type="success" @click="save()">确定</el-button>
    </div>
  </div>
</template>
<script>
const $ = name => document.querySelector(name)
const mobileStatus = (/Mobile|Android|iPhone/i.test(navigator.userAgent)) // 判断是否为移动端
// 配置内容
const config = {
  width: 0, // 宽度
  height: 0, // 高度
  lineWidth: 5, // 线宽
  strokeStyle: 'red', // 线条颜色
  lineCap: 'round', // 设置线条两端圆角
  lineJoin: 'round' // 线条交汇处圆角
}
// 偏移量
const client = {
  offsetX: 0,
  offsetY: 0
}
let canvas, ctx
export default {
  mounted() {
    this.drawingBoardInit()
  },
  destroyed() {
    window.removeEventListener(mobileStatus ? 'touchstart' : 'mousedown', this.init)
    window.removeEventListener(mobileStatus ? 'touchend' : 'mouseup', this.cloaseDraw)
    window.removeEventListener('resize', this.resetSize)
  },
  methods: {
    drawingBoardInit() {
      // const { width, height, left, top } = $('.drawing-board').getBoundingClientRect()
      const { width, height, left, top } = this.$refs.canvas.getBoundingClientRect()

      console.log(left)
      console.log(top)
      config.width = width
      config.height = height
      client.offsetX = left
      client.offsetY = top
      // canvas 实例
      canvas = $('#canvas')
      // 设置宽高
      canvas.width = config.width
      canvas.height = config.height
      // 设置边框
      canvas.style.border = '1px solid #000'
      // 创建上下文
      ctx = canvas.getContext('2d')
      // 设置填充背景色
      ctx.fillStyle = 'transparent'
      // 绘制填充矩形
      ctx.fillRect(
        0, // x 轴起始绘制位置
        0, // y 轴起始绘制位置
        config.width, // 宽度
        config.height // 高度
      )
      // 创建鼠标/手势按下监听器
      window.addEventListener(mobileStatus ? 'touchstart' : 'mousedown', this.init)
      // 创建鼠标/手势 弹起/离开 监听器
      window.addEventListener(mobileStatus ? 'touchend' : 'mouseup', this.cloaseDraw)
      // 自适应画布
      window.addEventListener('resize', this.resetSize)
    },
    // 鼠标按下
    init(event) {
      // 获取偏移量及坐标
      const { clientX, clientY } = mobileStatus ? event.changedTouches[0] : event
      // 清除以上一次 beginPath 之后的所有路径，进行绘制
      ctx.beginPath()
      console.log(clientX)
      console.log(clientY)
      // 根据配置文件设置相应配置
      ctx.lineWidth = config.lineWidth
      ctx.strokeStyle = config.strokeStyle
      ctx.lineCap = config.lineCap
      ctx.lineJoin = config.lineJoin
      // 设置画线起始点位（减去 左边、上方的偏移量很关键）
      ctx.moveTo(clientX - client.offsetX, clientY - client.offsetY)
      // 监听 鼠标移动或手势移动
      window.addEventListener(mobileStatus ? 'touchmove' : 'mousemove', this.draw)
    },
    // 绘制
    draw(event) {
      // 获取当前坐标点位
      const { clientX, clientY } = mobileStatus ? event.changedTouches[0] : event
      // 根据坐标点位移动添加线条（减去 左边、上方的偏移量很关键）
      ctx.lineTo(clientX - client.offsetX, clientY - client.offsetY)
      // 绘制
      ctx.stroke()
    },
    // 结束绘制
    cloaseDraw() {
      // 结束绘制
      ctx.closePath()
      // 移除鼠标移动或手势移动监听器
      window.removeEventListener('mousemove', this.draw)
    },
    // 清除
    reset() {
      // 清空当前画布上的所有绘制内容
      ctx.clearRect(0, 0, config.width, config.height)
    },
    // 将画布内容保存为图片
    save() {
      // 将画布内容转为base64编码
      const imgBase64 = canvas.toDataURL('png')
      console.log(imgBase64, 'imgBase64-->
>') // base64编码 // 将canvas上的内容转成blob流 canvas.toBlob(blob => {
console.log(blob, 'blob-->>') // 文件二进制流 // 获取当前时间，用来当做文件名
const date = new Date().getTime() // 创建一个 a 标签 const link =
document.createElement('a') // 设置 a 标签的下载文件名 link.download =
`${date}.png` // 设置 a 标签的跳转路径为 文件流地址 link.href =
URL.createObjectURL(blob) // 手动触发 a 标签的点击事件 link.click() // 移除 a
标签 link.remove() }) }, // 浏览器分辨率改变 自适应重置 resetSize() { const {
width, height, left, top } = $('.drawing-board').getBoundingClientRect() const
dx = Math.abs(left - client.offsetX) const dy = Math.abs(top - client.offsetY)
// 浏览器分辨率改变 重新设置配置项 config.width = width config.height = height
client.offsetX = left client.offsetY = top // 重置canvas宽高 会导致
之前canvas的内容丢失，得先存一份，重置完宽高，再绘制上去 const canvasData =
ctx.getImageData(0, 0, canvas.width, canvas.height) // 重置 canvas 宽高
canvas.width = width canvas.height = height // 改变完宽高 重绘画布
ctx.putImageData(canvasData, dx, dy) } } }
<!-- </script>
<style scoped>
/* .drawing-board {
  width: 100%;
  height: calc(100% - 40px);
  border-bottom: 1px solid #ccc;
  box-sizing: border-box;
} */
.tool-bar {
  width: 100%;
  height: 40px;
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
</style> -->
<style scoped lang="scss">
/* .drawing-board {
  width: 100%;
  height: calc(100% - 40px);
  border-bottom: 1px solid #ccc;
  box-sizing: border-box;
} */
.tool-bar {
  width: 100%;
  height: 40px;
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
.father {
  // margin: 10px;
  // background: red;
  // width: 400px;
  // height: 400px;
  // display: inline-block;
  // position: absolute;
  border: 1px solid red;
  // float:right;
  // width: 100%;
  // overflow: hidden;
  .normal {
    background: rgb(49, 36, 170);
    float: left;
    width: 180px;
    height: 100px;
    margin-top: 50px;
    // margin:100px
  }
  // .normal1 {
  //   background: rgb(170, 36, 105);
  //   // float: left;
  //   width: 190px;
  //   height: 50px;
  // }
  .p1 {
    background: green;
    // float: right;
    // overflow: hidden;
    // clear: both;
    //  display: inline-block;
  // position: absolute;
    width: 500px;
    height: 300px;
  }
}
// .father {
//       margin-top:50px;
//       width: 200px;
//       padding: 10px;
//       background-color: #ffd664;
//       border: 1px solid #aaa;
//       // overflow: hidden;
//     }
//     .left {
//       height: 60px;
//       width: 160px;
//       background-color: #ffddee;
//         float: left;
//     }
//     .right {
//       height: 60px;
//       width: 60px;
//       background-color: #ffaaee;
//       float: right;
//     }
//     div {
//         padding: 15px 20px;
//         font-size: 14px;
//         color: #333;
//     }
//     .footer {
//       width: 200px;
//       height: 100px;
//       background-color: #eee;
//       margin-top: 5px;
//       clear: both;
//     }
//  .clearFix:after{
//   content:'';
//   display: block;
//   clear:both;
// }
// .clearFix:after{
//   content:'';
//   display: block;
//   clear: both;
// }
</style>
