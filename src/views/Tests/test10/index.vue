<template>
  <div class="container">
    <div class="test">
      <div class="num">1</div>
      <div class="num">2</div>
      <div class="num">3</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    [
      // 第一层
      [
        [1, 0, 0, 0, 1, 0],
        [0, 0, 0, 0, 0, 0],
        [1, 0, 0, 0, 1, 0],
        [0, 0, 0, 0, 0, 0]
      ],
      // 第二层
      [
        [0, 0, 0, 0, 0, 0],
        [0, 1, 0, 1, 0, 0],
        [0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0]
      ]

    ]
    return {}
  },
  methods: {
    getdata() {
      const coverMap = [
        [1, 1, 0, 0, 1, 1],
        [1, 1, 1, 1, 1, 1],
        [1, 1, 1, 1, 1, 1],
        [1, 1, 0, 0, 1, 1]
      ]

      coverMap
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
  //   width: 100%;
  .test {
    margin: 10% auto;
    position: relative;
    width: 200px;
    .num {
      width: 100px;
      height: 100px;
      margin-bottom: 10px;
      font-size: 40px;
      text-align: center;
      line-height: 100px;
      background-color: orange;
    }
    div:nth-of-type(2) {
      position: absolute;
      left: 50px;
      background-color: rgb(255, 0, 0);
    }
    // div:nth-of-type(3) {
    //   // z-index: 1;
    // }
    // div:nth-of-type(2){
    //     position: relative;
    //   left: 100px;
    // }
    div:nth-of-type(3) {
      position: relative;
    }
  }
}
</style>
