<template>
  <view
      class="content"
      ref="content"
      :style="{backgroundImage:`url(${bgImgUrl})`}"
  >
    <view class="content-btn">
      <view class="content-text">
        <text>
          吃啥 :{{ text }}
        </text>
      </view>
      <view>
        <u-button
            type="primary"
            style='width: 180px'
            :text="btnText"
            color="linear-gradient(to bottom, rgb(246,202,92), rgb(243,173,78))"
            @click="handleStart">
        </u-button>
      </view>
    </view>
    <!-- 背景文字 -->
    <text
        class="bgText"
        v-for="(item,index) in contentText"
        :key="index"
        :style="{
          left: item.x,
          top: item.y,
          fontSize:item.size,
          transform:`scale(${item.scale}, ${item.scale})`,
          opacity:item.opacity
        }">
      {{ item.text }}
    </text>
  </view>
</template>

<script>
export default {
  data() {
    return {
      contentText: [],
      list: '麻辣烫 麻辣香锅 饺子 米线 炒饭 黄焖鸡 麦当劳 肯德基 螺蛳粉 炸鸡 寿司 粥 酸菜鱼 馄饨 酸辣粉 烧烤 凉皮 火锅 水饺 披萨 冒菜 拉面 汉堡 煎饼果子 煲仔饭 重庆小面 炸酱面 KFC 包子 黄焖鸡米饭 鸡公煲 烤冷面 兰州拉面 沙县 肠粉 西北风 泡面 小碗菜 面包 汉堡王 盖浇饭 面 米饭 沙县小吃 咖喱饭 炒面 烤肉拌饭 华莱士 石锅拌饭 猪脚饭 烩面 热干面 刀削面 油泼面 土豆粉 螺狮粉 凉皮儿 肉夹馍 羊肉汤 卤肉饭 烤肉饭 驴肉火烧 川菜 烤串 烤鸭 蟹黄包 生煎 炒年糕',
      timer: null,
      text: '', // 选中的菜品
      btnText: '开始',
      bgImgUrl: require('../../assets/images/bg.jpg'),
      clickStart:0
    }
  },
  onLoad() {
  },
  computed: {
    listFormat() {
      return this.list.replace(/ +/g, " ").replace(/^ | $/g, "").split(" ");
    }
  },
  methods: {
    // 点击开始和结束
    handleStart() {
      if(this.clickStart === 3){

      }
      if (this.btnText === '开始') {
        clearInterval(this.timer)
        this.createSpan()
        this.btnText = '确定'
        this.clickStart ++
      } else {
        clearInterval(this.timer)
        this.randomText()
        this.btnText = '开始'
      }
    },
    // 获取宽高度
    getScreen() {
      let width, height;
      uni.getSystemInfo({
        success: res => {
          width = res.windowWidth
          height = res.windowHeight
        }
      })
      return {
        width: width,
        height: height
      }
    },
    // 创建背景
    createSpan() {
      let {width, height} = this.getScreen();
      this.timer = setInterval(() => {
        let item = Math.ceil(Math.random() * this.listFormat.length)
        let food = this.listFormat[item - 1] // 得出随机一个
        this.text = food
        // 位置
        let rTop = Math.ceil(Math.random() * height),
            rLeft = Math.ceil(Math.random() * (width - 50)),
            rSize = Math.ceil(Math.random() * (37 - 14) + 14);
        this.contentText.push({
          text: food,
          opacity: 0,
          x: rLeft + "px",
          y: rTop + 'px',
          size: rSize + 'px',
          scale: Math.random() * 2,
        })
        // console.log(this.contentText);
        if (this.contentText.length > 0) {
          setTimeout(() => {
            this.contentText[this.contentText.length - 1].opacity = 1
            this.contentText.splice(0, 1);
          }, 400)
        }
      }, 60)
    },
    // 随机抽菜品
    randomText() {
      let index = Math.floor((Math.random() * this.listFormat.length));
      this.text = this.listFormat[index]
    }
  }
}
</script>

<style lang="scss">

.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  height: 100vh;
  width: 100vw;
}

.bgText {
  display: inline-block;
  position: absolute;
  color: #ccc;
  transition: opacity 0.3s;
}

.content-btn {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translateX(-50%) translateY(-50%);
  text-align: center;
  z-index: 20;

  view {
    margin-top: 10px;
  }

  .content-text {
    color: #333;
    font-weight: bold;
    font-size: 20px;
    white-space: nowrap;
  }
}

// 动画
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
