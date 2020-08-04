
# Vue-Fortune-Wheel

组件名称: Wheel of Fortune, 命运之轮, 幸运转盘

应用场景: 抽奖

## Install
```
yarn add vue-fortune-wheel
```
or
```
npm install vue-fortune-wheel
```

## Usage

```html
<template>
  <div>
    <!-- type: image -->
    <FortuneWheel
      style="width: 500px"
      type="image"
      :prizes="prizes"
      :angleBase="-10"
      @rotateStart="onImageRotateStart"
      @rotateEnd="onRotateEnd"
    >
      <img slot="wheel" src="@/assets/wheel.png" />
      <img slot="button" src="@/assets/button.png" />
    </FortuneWheel>

    <!-- type: canvas -->
    <FortuneWheel
      style="width: 500px"
      borderColor="#584b43"
      :borderWidth="6"
      :prizes="prizes"
      :verify="cavansVerify"
      @rotateStart="onCanvasRotateStart"
      @rotateEnd="onRotateEnd"
    />
  </div>
</template>
```

```js
import FortuneWheel from 'vue-fortune-wheel'
import 'vue-fortune-wheel/lib/vue-fortune-wheel.css'

export default {
  components: {
    FortuneWheel
  },
  data() {
    return {
      cavansVerify: true, // canvas 模式的转盘是否开启验证
      prizes: [
        {
          id: 1, //* 每个奖品唯一的 id, 大于 0 的整数
          name: 'Blue', // 奖品名, type 为 canvas 时的显示值 (type 为 image 时无需此参数)
          value: 'Blue\' value', //* 奖品值, 旋转结束后的返回值
          bgColor: '#45ace9', // 背景色 (type 为 image 时无需此参数)
          color: '#ffffff', // 字体色 (type 为 image 时无需此参数)
          probability: 30 //* 概率，最多保留 4 位小数 （所有奖品的概率和必须为 100）
        },
        {
          id: 2,
          name: 'Red',
          value: 'Red\' value',
          bgColor: '#dd3832',
          color: '#ffffff',
          probability: 40
        },
        {
          id: 3,
          name: 'Yellow',
          value: 'Yellow\' value',
          bgColor: '#fef151',
          color: '#ffffff',
          probability: 30
        }
      ]
    }
  },
  methods: {
    onImageRotateStart() {
      console.log('onRotateStart')
    },
    onCanvasRotateStart(rotate) {
      if (this.canvasVerify) { // 开启验证则先验证
        const verified = true // true: 测试通过验证, false: 测试未通过验证
        this.DoServiceVerify(verified, 2000).then((verifiedRes) => {
          rotate() // 传 ture 则开始旋转, 不传默认 false 不旋转
          if (verifiedRes) {
            console.log('验证通过, 开始旋转')
            rotate() // 调用回调, 开始旋转
            this.canvasVerify = false // 关闭验证模式
          } else {
            alert('未通过验证')
          }
        })
        return
      }
      console.log('onCanvasRotateStart')
    },
    onRotateEnd(prize) {
      alert(prize.value)
    },
    // 模拟请求后端接口, verified: 是否通过验证, duration: 延迟时间
    DoServiceVerify(verified, duration) {
      return new Promise((resove) => {
        setTimeout(() => {
          resove(verified)
        }, duration)
      })
    }
  }
}
```

## Demo (App.vue)

### online
https://xiaolin1995.github.io/vue-fortune-wheel/demo/

## FortuneWheel Events
| 事件名称 | 说明 | 回调参数 |
| ------ | ------ | ------ |
| rotateStart | 点击转盘按钮时触发 | 开启 verify 时, 会有回调, 调用回调函数才开始旋转 |
| rotateEnd | 转盘动画结束时触发 | 奖品的整个 Object |

## FortuneWheel Attributes
| 参数 | 说明 | 类型 | 默认值 |
| ------ | ------ | ------ | ----- |
| type | 转盘的类型 (canvas, image) | String | canvas |
| disabled | 是否禁用 (禁用后, 点击按钮不会旋转) | Boolean | false |
| verify | 是否开始验证 | Boolean | false |
| radius | 圆的半径 (type: canvas) | Number | 250 |
| textRadius | 文本距圆心的距离 (type: canvas) | Number | 190 |
| textLength | 奖品一行几个字符, 超出换行 (最多两行) | Number | 6 |
| lineHeight | 文本行高 (type: canvas) | Number | 20 |
| borderWidth | 圆的外边框 (type: canvas) | Number | 0 |
| borderColor | 外边框的色值 (type: canvas) | String | transparent |
| btnText | 按钮文本 (type: canvas) | String | GO |
| btnWidth | 按钮的宽 (px) | Number | 140 |
| fontSize | 奖品字号 (px) | Number | 34 |
| duration | 完成一次旋转的时间 (单位 ms) | Number | 6000 |
| timingFun | 旋转过渡的 css 时间函数 | String | cubic-bezier(0.36, 0.95, 0.64, 1) |
| angleBase | 旋转圈数 (angleBase * 360 为一次旋转的总角度, 为负数时可反向旋转) | Number | 10 |
| prizeId | 指定 id, 会每次旋转到此 id 的奖品 (为 0 时, 则根据每个奖品自身的概率, 可在旋转中改变值, 以此完成各种骚操作 ) | Number | 0 |
| prizes | 奖品列表 (结构参考 Usage) | Array | / |

