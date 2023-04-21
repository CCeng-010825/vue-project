<template>
  <div>
    <el-table
      ref="dragTable"
      :data="list"
      border
      fit
      highlight-current-row
      row-key="date"
      style="width: 100%"
    >
      <el-table-column type="index" label="日期" />
      <el-table-column prop="date" label="日期" align="center" />
      <el-table-column prop="name" label="姓名" align="center" />
      <el-table-column prop="address" label="地址" align="center" />
    </el-table>
  </div>
</template>
<script>
import Sortable from 'sortablejs' // 引入拖拽js 这个是实现拖拽功能的核心，可以通過npm 安裝，文檔鏈接：http://www.sortablejs.com/options.html

export default {
  data() {
    return {
      newList: [],
      list: [
        {
          date: '1111111',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        },
        {
          date: '22222222222',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1517 弄'
        },
        {
          date: '333333333',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1519 弄'
        },
        {
          date: '44444444444',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          date: '555555555',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          date: '6666666666',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          date: '777777777777',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        },
        {
          date: '888888888',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        }
      ],
      sortable: null
    }
  },
  mounted() {
    console.log(this.$route.params.id, 'id')
    this.setSort()
    console.log('waht？？', this.$refs.dragTable.$el)
    this.newList = this.list.slice()
    console.log('ppppppppppppp')
    function promise1() {
      return new Promise((resolve) => {
        console.log('promise1 start')
        resolve()
      }).then(() => {
        console.log('promise1 end')
      })
    }
    function promise2() {
      return new Promise((resolve) => {
        console.log('promise2 start')
        resolve()
      }).then(() => {
        console.log('promise2 end')
      })
    }
    function promise3() {
      return new Promise((resolve) => {
        console.log('promise3 start')
        resolve()
      }).then(() => {
        console.log('promise3 end')
      })
    }
    function promise4() {
      return new Promise((resolve) => {
        console.log('promise4 start')
        resolve()
      }).then(() => {
        console.log('promise4 end')
      })
    }
    async function asyncFun() {
      console.log('async1 start')
      await promise2()
      console.log('async1 inner')
      await 2
      console.log('222')
      await promise3()
      console.log('async1 end')
    }
    setTimeout(() => {
      console.log('setTimeout start')
      promise1()
      console.log('setTimeout end')
    }, 0)
    asyncFun()
    promise4()
    console.log('script end')
  },
  methods: {
    setSort() {
      const el = this.$refs.dragTable.$el.querySelectorAll(
        '.el-table__body-wrapper > table > tbody'
      )[0]
      this.sortable = new Sortable(el, {
        ghostClass: 'blue-background-class', // Class name for the drop placeholder,// 抓取的元素的类名，简单来说就是抓取元素的时候给他加一个类名blue-background-class
        setData: function(dataTransfer) {
          console.log('dataTransfer', dataTransfer)
          dataTransfer.setData('Text', '')
        },

        onEnd: evt => { // console 一下 evt ，你就明白下面为什么这样写
        //   console.log(this.list.splice(evt.oldIndex, 1))
          const targetRow = this.list.splice(evt.oldIndex, 1)[0]
          console.log(targetRow)
          console.log(this.list)
          this.list.splice(evt.newIndex, 0, targetRow)
          console.log(evt)
          const tempIndex = this.newList.splice(evt.oldInx, 1)[0]
          console.log(tempIndex)
          this.newList.splice(evt.newIndex, 0, tempIndex)
          console.log(this.newList)
        }
      })
    }
  }
}
</script>
