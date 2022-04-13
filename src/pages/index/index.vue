<template>
  <view
      class="content"
      ref="content"
      :style="{backgroundImage:`url(${bgImgUrl})`}"
  >
    <!-- 中间文字 -->
    <view class="content-btn">
      <view class="content-text">
        <text v-if="btnIsShow">
          吃啥 :{{ text }}
        </text>
      </view>
      <view>
        <u-button
            v-if="btnIsShow"
            type="primary"
            style='width: 180px'
            :text="btnText"
            color="linear-gradient(to bottom, rgb(246,202,92), rgb(243,173,78))"
            @click="handleStart">
        </u-button>
        <u-button
            v-else
            type="primary"
            style='width: 180px'
            text="我错了"
            color="linear-gradient(to bottom, rgb(246,202,92), rgb(243,173,78))"
            @click="handleError">
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
    <!-- 提示框 -->
    <u-toast ref="uToast"></u-toast>
    <!-- 弹框 -->
    <u-popup :show="menuShow" @close="menuClose" @open="menuOpen" closeable safeAreaInsetTop round="8">
      <u--textarea
          v-model="menuText"
          placeholder="自定义菜品名称,用空格隔开~"
          :disabled="menuDisabled"
          @input="handleTextarea"
      >
      </u--textarea>
      <u-scroll-list indicatorActiveColor="#ebba65">
        <view
            v-for="(item, index) in menuList"
            :key="index"
            class="scroll-box"
            @click="handleMenuList(item)"
        >
          <image class="scroll-image" :src="item.thumb"></image>
          <text class="scroll-text">{{ item.text }}</text>
        </view>
      </u-scroll-list>
    </u-popup>
    <!-- 菜单 -->
    <view class="menu-box" @click="menuShow = true">
      菜单
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      contentText: [],  // 循环遍历生成的背景文字
      list: '',  // 菜单列表
      timer: null,  // 计时器
      text: '', // 选中的菜品
      btnText: '开始',
      bgImgUrl: require('../../assets/images/bg.jpg'),
      clickStart: 0,    // 中间按钮点击了多少次
      btnIsShow: true,  // 是否显示中间按钮
      menuShow: false,  // 是否显示菜单
      menuList: [
        {
          thumb: require('../../assets/images/3.png'),
          text: '常用',
          list: '麻辣烫 麻辣香锅 饺子 米线 炒饭 黄焖鸡 麦当劳 肯德基 螺蛳粉 炸鸡 寿司 粥 酸菜鱼 馄饨 酸辣粉 烧烤 凉皮 火锅 水饺 披萨 冒菜 拉面 汉堡 煎饼果子 煲仔饭 重庆小面 炸酱面 KFC 包子 黄焖鸡米饭 鸡公煲 烤冷面 兰州拉面 沙县 肠粉 西北风 泡面 小碗菜 面包 汉堡王 盖浇饭 面 米饭 沙县小吃 咖喱饭 炒面 烤肉拌饭 华莱士 石锅拌饭 猪脚饭 烩面 热干面 刀削面 油泼面 土豆粉 螺狮粉 凉皮儿 肉夹馍 羊肉汤 卤肉饭 烤肉饭 驴肉火烧 川菜 烤串 烤鸭 蟹黄包 生煎 炒年糕',
        },
        {
          thumb: require('../../assets/images/2.png'),
          text: '甜点',
          list: '布丁 奶酪 冰淇凌 栗子蛋糕 处女冰淇淋 巧克力蛋糕 红豆沙 绿豆沙 杏仁糊 花生糊 核桃糊 百合糖水 莲子糖水 窝蛋奶 姜撞奶 双皮奶 银耳炖木瓜 芝麻汤圆'
        },
        {
          thumb: require('../../assets/images/1.png'),
          text: '自定义',
          list: ''
        },
      ],
      menuText: '',  // 菜单输入框文字
      menuDisabled: false // 是否禁用文本框
    }
  },
  onLoad() {
    try {
      // 读取自定义数据
      uni.getStorage({
        key: 'uniapp_storage_menu_list',
        success: (res) => {
          // console.log(res.data);
          // 更改数据
          if (res.data.length > 0) {
            this.menuList[2].list = res.data
            this.list = this.menuList[2].list
          } else {
            // 没有数据就取默认的
            this.list = this.menuList[0].list
            this.menuText = this.menuList[0].list
            this.menuDisabled = true
          }
        }
      });
    } catch (e) {
      // 没有自定义数据的情况下, 读取默认数据
      this.list = this.menuList[0].list
      this.menuText = this.menuList[0].list
      this.menuDisabled = true
    }
  },
  computed: {
    listFormat() {
      return this.list.replace(/ +/g, " ").replace(/^ | $/g, "").split(" ");
    },
  },
  methods: {
    // 处理文本域输入
    handleTextarea() {
      // 存储数据
      uni.setStorage({
        key: 'uniapp_storage_menu_list',
        data: this.menuText,
        success: () => {
          // console.log('success');
          // 更改数据
          this.menuList[2].list = this.menuText
          this.list = this.menuText
        }
      });
    },
    // 处理认错
    handleError() {
      this.btnIsShow = true
      this.clickStart = 0
    },
    // 处理菜单关闭
    menuClose() {
      this.menuShow = false
    },
    // 处理菜单打开
    menuOpen() {

    },
    // 菜单的某一条
    handleMenuList(item) {
      // 是否禁用
      this.menuDisabled = item.text !== '自定义'
      // 更换数据
      this.list = item.list
      this.menuText = item.list
    },
    // 点击开始和结束
    handleStart() {
      if (this.clickStart === 3 && this.btnText === '开始') {
        this.$refs.uToast.show({
          type: 'error',
          icon: false,
          title: '失败主题',
          message: "还选 ? 你别吃了 !",
          iconUrl: 'https://cdn.uviewui.com/uview/demo/toast/error.png'
        })
        this.btnIsShow = false
        return
      }

      if (this.btnText === '开始') {
        clearInterval(this.timer)
        this.createSpan()
        this.btnText = '确定'
        this.clickStart++
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

  .menu-box {
    width: 50px;
    height: 50px;
    position: fixed;
    bottom: 100px;
    right: 20px;
    z-index: 30;
    background: #EBBA65;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #fff;
    font-size: 14px;
  }

  .scroll-box {
    margin: 10px;
    text-align: center;
    display: flex;
    flex-direction: column;

    .scroll-image {
      width: 50px;
      height: 50px;
      border-radius: 8px;
    }

    .scroll-text {
      font-size: 14px;
      color: #82848a;
    }
  }

}

</style>
