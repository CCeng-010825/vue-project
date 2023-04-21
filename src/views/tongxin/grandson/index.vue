<template>
  <div>我是孙子</div>
</template>

<script>
export default {
  inject: ['ma', 'somemethod'],
  mounted() {
    console.log(this.$attrs, 'grandson')
    console.log(this.$listeners, 'grandson')
    this.$emit('eventone')
    this.$emit('eventtwo')
    console.log(this.$parent, 'grandson0000')
    console.log(this.ma)
    this.somemethod()
  }
}
</script>

<style>

</style>
