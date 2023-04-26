<template>
  <div class="container">
    羊了个羊
    <!-- <div class="intro" /> -->
    <div class="box">
      <div class="card-wrap" :style="cardWrapStyle">
        <div
          v-for="item in cardItemList"
          id="tag"
          :key="item.key"
          ref="item"
          class="card-item"
          :class="{ 'item-cover': item.cover }"
          :style="item.style"
          @click="clickCard(item)"
        >
          {{ item.content }}
        </div>
        <div
          v-for="item in penddingList"
          :key="item.key"
          class="card-item"
          :style="item.style"
        >
          {{ item.content }}
        </div>
        <div
          v-for="item in clearList"
          :key="item.key"
          class="card-item clear-item"
          :style="item.style"
        >
          {{ item.content }}
        </div>
        <div
          v-for="item in saveList"
          :key="item.key"
          class="card-item"
          :style="item.style"
          @click="clickSaveCard(item)"
        >
          {{ item.content }}
        </div>
        <p class="card-tips">
          剩余空位:{{ 7 - penddingList.length }}/7；已消除:{{ clearList.length }}/{{
            cardItemList.length + penddingList.length + saveList.length + clearList.length
          }}
        </p>
      </div>
      <div class="tools">
        道具：
        <el-button size="small" :disabled="!tools.save" @click="getThreeCard">
          取出三张卡片
        </el-button>
        <el-button size="small" :disabled="!tools.rand" @click="rand">随机</el-button>
        <el-button size="small" @click="initGame">再来一轮</el-button>
        <el-button size="small" @click="setting">游戏设置</el-button>
      </div>
    </div>
    <el-drawer title="游戏设置" :visible.sync="drawer" size="25%">
      <div style="padding: 20px" class="slid">
        <div class="demonstartion">横向最多卡片数</div>
        <div class="slide">
          <el-slider v-model="drawerOption.x" :min="2" :max="10" :marks="marks" />
        </div>
      </div>
      <div style="padding: 20px" class="slid">
        <div class="demonstartion">纵向最多卡片数</div>
        <div class="slide">
          <el-slider v-model="drawerOption.y" :min="2" :max="10" :marks="marks" />
        </div>
      </div>
      <div style="padding: 20px" class="slid">
        <div class="demonstartion">层数</div>
        <div class="slide">
          <el-slider v-model="drawerOption.z" :min="2" :max="10" :marks="marks" />
        </div>
      </div>
      <div style="padding: 20px" class="slid">
        <div class="demonstartion">使用卡片种类数</div>
        <div class="slide">
          <el-slider
            v-model="drawerOption.maxCardType"
            :min="3"
            :max="14"
            :marks="typeMarks"
          />
        </div>
      </div>

      <div class="footer">
        <el-button size="mini" @click="submit">确定</el-button>
        <el-button size="mini" @click="drawer = false">取消</el-button>
      </div>
    </el-drawer>
  </div>
</template>

<script>
class CardItem {
  static x = 20
  static y = 21
  static colorType = {
    1: { background: '#FFB7DD' },
    2: { background: '#FFCCCC' },
    3: { background: '#FFC8B4' },
    4: { background: '#FFDDAA' },
    5: { background: '#FFEE99' },
    6: { background: '#FFFFBB' },
    7: { background: '#EEFFBB' },
    8: { background: '#CCFF99' },
    9: { background: '#99FF99' },
    10: { background: '#BBFFEE' },
    11: { background: '#AAFFEE' },
    12: { background: '#99FFFF' },
    13: { background: '#CCEEFF' },
    14: { background: '#CCDDFF' }
  }
  static contentType = {
    1: '🥕',
    2: '✂️',
    3: '🥦',
    4: '🥛',
    5: '🌊',
    6: '🧤',
    7: '🧵',
    8: '🌱',
    9: '🔨',
    10: '🌽',
    11: '🌾',
    12: '🐑',
    13: '🪵',
    14: '🔥'
  }
  constructor({ x, y, z, key }) {
    this.x = x
    this.y = y
    this.z = z
    this.key = key
    // const offset = z * 0
    this.val = key
    this.style = {
      top: y * CardItem.y + 'px',
      left: x * CardItem.x + 'px',
      width: CardItem.x * 2 - 2 + 'px',
      height: CardItem.y * 2 - 8 + 'px'
    }
    // this.id = 'tagg'
  }

  setValue(val) {
    this.val = val
    this.content = CardItem.contentType[val]
    Object.assign(this.style, CardItem.colorType[val])
  }
}
import audio from '/audio/click.mp3'
export default {
  data() {
    return {
      cardItemList: [],
      xUnit: 0,
      yUnit: 0,
      style: '',
      drawerOption: {
        x: 0,
        y: 0,
        z: 0,
        cardRandom: 0,
        maxCardType: 0
      },
      option: {
        x: 6,
        y: 4,
        z: 8,
        cardRandom: 0.3,
        maxCardType: 11
      },
      marks: {
        0: '2',
        10: '10'
      },
      typeMarks: {
        0: '3',
        14: '14'
      },
      drawer: false,
      cardMap: [],
      // cardItemList: [],
      penddingList: [],
      clearList: [],
      saveList: [],
      calcValueList: [],
      // xUnit: 0,
      // yUnit: 0,
      tools: {
        save: true,
        rand: true
      },
      timer: 0
    }
  },
  computed: {
    cardWrapStyle() {
      return {
        width: (this.xUnit + 2) * CardItem.x + 'px',
        height: (this.yUnit + 1) * CardItem.y + 'px'
      }
    },
    leftOffset() {
      const wrapWidth = (this.xUnit + 2) * CardItem.x
      return (wrapWidth - 7 * CardItem.x * 2) / 2
    }
  },
  mounted() {
    this.initGame()
  },
  methods: {
    setting() {
      this.drawer = true
      Object.assign(this.drawerOption, this.option)
    },
    initGame() {
      this.getMap(this.option)
      this.penddingList = []
      this.clearList = []
      this.saveList = []
      this.tools.save = true
      this.tools.rand = true
      this.setCardValue({ maxCardType: Number(this.option.maxCardType) })
      this.calcCover()
    },
    // x=6 y=4 z=8
    getMap({ x, y, z, cardRandom }) {
      const cardMap = this.initGameMap({ x, y, z })
      const cardItemList = []
      let key = 0
      console.log(cardMap)
      // const maxWidth = (x - 1) * 2
      // const maxHeight = (y - 1) * 2
      for (let k = 0; k < z; k++) {
        const shrinkSpeed = 3
        const shrink = Math.floor((z - k - 1) / shrinkSpeed)
        const shrinkX = Math.min(Math.floor(this.xUnit / 2) - 2, shrink)
        const shrinkY = Math.min(Math.floor(this.yUnit / 2) - 2, shrink)
        // const shrink = Math.floor((z - k - 1) / shrinkSpeed)
        // 行对称  从上到下
        for (let i = shrinkY; i < this.yUnit - 1 - shrinkY; i++) {
          // 列对称 从左到右
          for (let j = shrinkX; j < Math.ceil((this.xUnit - 1) / 2); j++) {
            let canSetCard = true

            // 左边
            if (j > 0 && cardMap[k][i][j - 1]) {
              //   console.log(canSetCard)
              canSetCard = false
              // 上边
            } else if (i > 0 && cardMap[k][i - 1][j]) {
              canSetCard = false
              // 左上
            } else if (i > 0 && j > 0 && cardMap[k][i - 1][j - 1]) {
              canSetCard = false
              // 右上
            } else if (i > 0 && cardMap[k][i - 1][j + 1]) {
              canSetCard = false
              // 正底
            } else if (k > 0 && cardMap[k - 1][i][j]) {
              canSetCard = false
            } else if (Math.random() >= cardRandom) {
              canSetCard = false
            }
            if (canSetCard) {
              key++
              const cardItem = new CardItem({ x: j, y: i, z: k, key })
              cardMap[k][i][j] = cardItem
              cardItemList.push(cardItem)
              const mirrorX = this.xUnit - 2 - j
              if (mirrorX > j) {
                key++
                const cardItem = new CardItem({
                  x: mirrorX,
                  y: i,
                  z: k,
                  key
                })
                cardMap[k][i][mirrorX] = cardItem
                cardItemList.push(cardItem)
              }
            }
          }
        }
      }
      this.cardItemList = cardItemList
      cardItemList.reverse()
      for (let i = 1; i <= key % 3; i++) {
        const clearItem = cardItemList.pop()
        cardMap[clearItem.z][clearItem.y][clearItem.x] = 0
      }
      cardItemList.reverse()
      this.cardMap = cardMap
      this.cardItemList = cardItemList
    },
    calcCover() {
      // 构建一个遮挡 map
      const coverMap = new Array(this.yUnit)
      for (let i = 0; i <= this.yUnit; i++) {
        coverMap[i] = new Array(this.xUnit).fill(false)
      }
      var io = new IntersectionObserver(
        (elements) => {
          elements.forEach((item) => {
            if (!item.isVisible) {
              item.target.classList.add('item-cover')
            } else {
              item.target.classList.remove('item-cover')
            }
          })
        },
        {
          threshold: [1.0],
          delay: 100,
          trackVisibility: true
        }
      )
      let elements = []
      setTimeout(() => {
        elements = this.$refs.item
        elements.forEach((item) => {
          io.observe(item)
        })
      }, 800)
    },
    initGameMap({ x, y, z }) {
      this.xUnit = x * 2
      this.yUnit = y * 2
      const cardMap = new Array(z)
      for (let k = 0; k < z; k++) {
        cardMap[k] = new Array(this.yUnit)
        for (let i = 0; i < this.yUnit; i++) {
          cardMap[k][i] = new Array(this.xUnit).fill(0)
        }
      }
      return cardMap
    },
    setCardValue({ maxCardType } = {}) {
      const valStack = new Array(maxCardType)
      this.calcValueList = new Array(maxCardType + 1).fill(0)
      this.cardItemList.forEach((item) => {
        const value = Math.ceil(Math.random() * maxCardType)
        if (valStack[value]) {
          valStack[value].push(item)
          if (valStack[value].length === 3) {
            valStack[value].forEach((item) => {
              item.setValue(value)
            })
            valStack[value] = null
          }
        } else {
          valStack[value] = [item]
        }
      })
      let count = 2
      valStack.forEach((list) => {
        list &&
          list.forEach((item) => {
            count++
            item.setValue(Math.floor(count / 3))
          })
      })
    },
    clickCard(item) {
      this.penddingList.push(item)
      const index = this.cardItemList.indexOf(item)
      this.cardItemList = this.cardItemList
        .slice(0, index)
        .concat(this.cardItemList.slice(index + 1))
      this.calcCover()
      this.calcValueList[item.val]++
      setTimeout(() => {
        console.log(this.leftOffset)
        item.style.top = '160%'
        item.style.left =
          this.leftOffset + (this.penddingList.length - 1) * CardItem.x * 2 + 'px'
      }, 0)
      setTimeout(() => {
        this.removeThree()
      }, 0)
    },
    clickSaveCard(item) {
      this.cardItemList.push(item)
      const index = this.saveList.indexOf(item)
      this.saveList = this.saveList.slice(0, index).concat(this.saveList.slice(index + 1))
      this.clickCard(item)
    },
    removeThree() {
      this.penddingList.some((item) => {
        if (this.calcValueList[item.val] === 3) {
          this.penddingList.forEach((newItem) => {
            if (newItem.val === item.val) {
              this.clearList.push(newItem)
            }
          })
          setTimeout(() => {
            this.clearList.forEach((item) => {
              item.style.left = this.leftOffset - 60 + 'px'
            })
          }, 200)
          this.penddingList = this.penddingList.filter(
            (newItem) => newItem.val !== item.val
          )
          this.penddingList.forEach((item, index) => {
            item.style.top = '160%'
            item.style.left = this.leftOffset + index * CardItem.x * 2 + 'px'
          })
          this.calcValueList[item.val] = 0
        }
      })
    },
    getThreeCard() {
      // 取出三张卡片
      if (!this.tools.save) {
        return
      }
      this.tools.save = false
      this.saveList = this.penddingList.slice(0, 3)
      this.saveList.forEach((item, index) => {
        item.style.top = '110%'
        item.style.left = this.leftOffset + index * CardItem.x * 2 + 'px'
        this.calcValueList[item.val]--
      })
      this.penddingList = this.penddingList.slice(3)
      this.penddingList.forEach((item, index) => {
        item.style.top = '160%'
        item.style.left = this.leftOffset + index * CardItem.x * 2 + 'px'
      })
    },
    rand() {
      // 在已生成卡片中随机打乱卡片顺序以及层数关系
      if (!this.tools.rand) {
        return
      }
      this.tools.rand = false
      const length = this.cardItemList.length
      this.cardItemList.forEach((item) => {
        const value = Math.floor(Math.random() * length)
        let t
        const newItem = this.cardItemList[value]
        t = item.style.left
        item.style.left = newItem.style.left
        newItem.style.left = t
        t = item.style.top
        item.style.top = newItem.style.top
        newItem.style.top = t
        t = item.x
        item.x = newItem.x
        newItem.x = t
        t = item.y
        item.y = newItem.y
        newItem.y = t
        t = item.z
        item.z = newItem.z
        newItem.z = t
      })
      this.cardItemList.sort((a, b) => a.z - b.z)
      this.calcCover()
    },
    submit() {
      console.log(this.option)
      Object.assign(this.option, this.drawerOption)
      this.initGame()
      this.drawer = false
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
  // display: flex;
  .box {
    position: relative;
    .card-wrap {
      position: relative;
      margin: 5% auto 0 auto;
      .card-item {
        font-size: 28px;
        text-align: center;
        position: absolute;
        border-radius: 2px;
        box-sizing: border-box;
        background: #ddd;
        opacity: 1;
        cursor: pointer;
        transition: all 0.3s;
        box-shadow: 0px 3px 0 0 #fff, 0 8px 0 0 #ddd, 0 8px 0 2px #333, 0 0 0 2px #333;
        // box-shadow: 0px 3px 0 0 #333, 0 8px 0 0 #333, 0 8px 0 2px #333, 0 0 0 2px #333;

        &:hover {
          transform: scale3d(1, 1, 1.2);
          z-index: 1;
        }
      }
      .item-cover {
        pointer-events: none;
        box-shadow: 0px 3px 0 0 #999, 0 8px 0 0 #666, 0 8px 0 2px #000, 0 0 0 2px #000;
      }

      .item-cover:after {
        border-radius: 2px;
        content: '';
        position: absolute;
        width: 100%;
        height: 100%;
        left: 0;
        top: 0;
        background: #000;
        opacity: 0.55;
      }

      .card-tips {
        white-space: nowrap;
        position: absolute;
        left: 50%;
        top: 140%;
        transform: translate(-50%, 0);
        pointer-events: none;
      }
    }
    .tools {
      position: absolute;
      // margin: 10% auto 0 auto;
      top: 200%;
      text-align: center;
      left: 0;
      width: 100%;
    }
  }
  .slid {
    display: flex;
    justify-content: center;
    align-items: center;
    .demonstartion {
      padding-right: 20px;
    }
    .slide {
      flex: 1;
    }
  }
  .footer {
    // margin: 40% auto 0 auto;
    margin-top: 20%;
    text-align: center;
  }
}
</style>
