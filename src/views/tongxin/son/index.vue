<template>
  <div>我是儿子
    <!-- {{ msg }} -->
    <!-- {{ msg }}
    哈哈哈哈哈 -->
    <!-- <input :value="msg" @input="handleClick"> -->
    <slot>{{ msg }}</slot>
    <Grandson v-bind="$attrs" v-on="$listeners" />
    <slot name="a" :value="value" />
    <slot name="center">插槽默认内容...</slot>
  </div>
</template>

<script>
import Grandson from '../grandson/index.vue'
export default {
  components: { Grandson },
  //   model: {
  //     event: 'updateValue'
  //   },
  inject: ['ma', 'somemethod'],
  props: {
    msg: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      message: 'ppppp',
      value: '我是子组件数据 作用域插槽子传父 s-z-h'
    }
  },
  mounted() {
    console.log(this.$attrs, 'son')
    console.log(this.$listeners, 'son')
    this.$emit('eventone')
    this.$emit('eventtwo')
    console.log(this.$parent, 'son000000')
    console.log(this.ma)
    this.somemethod()
    // console.log(this.$root.foo)
    this.$root.foo = 2
  },
  methods: {
    handleClick(e) {
    //   console.log('')
    //   this.$emit('update:val', e.target.value)
    //   this.msg = '哈啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊'
    //   console.log('skkkkkkkkkkowpkokdoj')
      this.$emit('fn', '我是子组件')
    },
    handle() {
      console.log('handle')
    }
  }

}
</script>

<style>

</style>
