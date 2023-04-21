<template>
  <div>我是爸爸
    <slot>hello</slot>
    {{ msg }}
    <Son :msg="msg" val="你好啊" name="'jddkkdkdll'" @eventone="one" @click.native="two()" @three="three">
      <!-- <Son ref="child" @fn="fn" /> -->
      <template v-slot:a="value">
        <h2>{{ message }}</h2>
        <h2>{{ value.value }}</h2>
      </template>
      <template slot="center">
        <div>html结构1</div>
        <!-- <el-button>click</el-button> -->
      </template></Son>

  </div>
</template>
<script>
import Son from '../son/index.vue'
export default {
  /**
     * 1.props传递数据
     *
     */
  components: { Son },
  provide() {
    return {
      ma: this.msg,
      somemethod: this.one
    }
  },
  data() {
    return {
      msg: '我是你爸爸',
      hobby: ['dkkdkk'],
      message: 'pppppppppppppppppppppppppppp'
    }
  },
  mounted() {
    // this.$refs.child.handleClick()
    console.log(this.$children, '000000000000000000000')
    // this.$root.foo = 2
    console.log(this.$root.foo)
  },
  methods: {
    fn(e) {
      console.log('收到了子组件传来的消息', e)
    },
    one() {
      console.log('调用了父组件中的eventone事件')
    },
    two() {
      console.log('调用了父组件中的eventtwo事件')
    },
    three() {}
  }

}
</script>

<style>

</style>
