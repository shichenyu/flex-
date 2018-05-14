<template>
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
        <div class="content-left" :class="{'highlight':totalCount>0}">
          <div class="logo-wrapper">
            <div class="logo">
              <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
            </div>
            <div class="num" v-show="totalCount>0">{{totalCount}}</div>
          </div>
          <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
          <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
        </div>
        <div class="content-right" @click="pay">
          <p class="deliver" :class="payClass">{{payDesc}}</p>
        </div>
      </div>
      <div class="ball-contanier">
        <div v-for="(ball,index) in balls" :key="index">
          <transition name='drop' @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
            <div class="ball" v-show="ball.show" :css="false">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
      <transition name="fold">
        <div class="shopcart-list" v-show="fold">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li v-for="(food,index) in selectFoods" :key="index" class="food">
                <span class="name">{{food.name}}</span>
                <div class="price">￥{{food.price * food.count}}</div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" v-show="fold" @click="hideList"></div>
    </transition>
  </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import cartcontrol from 'components/cartcontrol/cartcontrol'

  export default {
    props: {
//        这里写的这些在父组件里面传了，要在这里写一下
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      },
//        如果type是Object、Array的话，default是个函数
      selectFoods: {
        type: Array,
        default () {
          return []
        }
      }
    },
    data () {
      return {
        fold: false,
        balls: [
          {show: false},
          {show: false},
          {show: false},
          {show: false},
          {show: false}
        ],
        ballDrops: []
      }
    },
    watch: {
      totalCount: function () {
        if (!this.totalCount) {
          this.fold = false
          return false
        }
      },
      fold: function (totalCount) {
        let show = this.fold
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.listContent, {
                click: true
              })
            } else {
              this.scroll.refresh()
            }
          })
        }
        return show
      }
    },
    computed: {
      totalPrice () {
        let price = 0
        this.selectFoods.forEach((food) => {
          price += food.price * food.count
        })
        return price
      },
      totalCount () {
        let count = 0
        this.selectFoods.forEach((food) => {
          count += food.count
        })
        return count
      },
      payDesc () {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}元起送`
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice
          return `差￥${diff}元起送`
        } else {
          return '去结算'
        }
      },
      payClass () {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough'
        } else {
          return 'enough'
        }
      }
//      listShow () {
//        if (!this.totalCount) {
//          // true是折叠状态，false是展开状态
//          this.fold = true
//          return false
//        }
//        let show = !this.fold
//        if (show) {
//          this.$nextTick(() => {
//            if (!this.scroll) {
//              this.scroll = new BScroll(this.$refs.listContent, {
//                click: true
//              })
//            } else {
//              this.scroll.refresh()
//            }
//          })
//        }
//        return show
//      }
    },
    methods: {
      toggleList () {
        if (!this.totalCount) {
          return
        }
        this.fold = true
      },
      pay () {
        if (this.totalPrice < this.minPrice) {
          return
        }
        window.alert(`支付${this.totalPrice}元`)
      },
      empty () {
        this.selectFoods.forEach((food) => {
          food.count = 0
        })
      },
      hideList () {
        this.fold = true
      },
      drop (el) {
//        console.log('el:')
//        console.log(el)
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i]
          if (!ball.show) {
            ball.show = true
            ball.el = el
            this.ballDrops.push(ball)
            return
          }
        }
      },
      beforeEnter (el) {
        let count = this.balls.length
        while (count--) {
          let ball = this.balls[count]
          if (ball.show) {
            // getBoundingClientRect 这个方法返回一个矩形对象，包含四个属性：left、top、right和bottom
            let rect = ball.el.getBoundingClientRect()
            let x = rect.left - 32
            let y = -(window.innerHeight - rect.top - 22)
            el.style.display = ''
            el.style.webkitTransform = `translate3d(0,${y}px,0)`
            el.style.transform = `translate3d(0,${y}px,0)`
            let inner = el.getElementsByClassName('inner-hook')[0]
            inner.style.webkitTransform = `translate3d(${x}px,0,0)`
            inner.style.transform = `translate3d(${x}px,0,0)`
          }
        }
      },
      enter (el) {
        /* 触发浏览器重绘 */
        /* eslint-disable no-unused-vars */
        let rf = el.offsetHeight
        /* 为了让下落的小球更自然做的优化 */
        this.$nextTick(() => {
          el.style.webkitTransform = 'translate3d(0,0,0)'
          el.style.transform = 'translate3d(0,0,0)'
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = 'translate3d(0,0,0)'
          inner.style.transform = 'translate3d(0,0,0)'
        })
      },
      afterEnter (el) {
        let ball = this.ballDrops.shift()
        if (ball) {
          ball.show = false
          el.style.display = 'none'
        }
      }
    },
    components: {
      cartcontrol
    }
  }
</script>
<style lang="stylus" rel="sheetstylus">
  @import "../../common/stylus/mixin.styl"
  .shopcart
    position: fixed
    bottom: 0px
    left: 0px
    width: 100%
    height: 48px
    z-index: 50
    .content
      display: flex
      background: #141d27
      .content-left
        flex: 1
        font-size: 0
        color: rgba(255, 255, 255, 0.4)
        .logo-wrapper
          display: inline-block
          position: relative
          top: -10px
          padding: 6px
          margin: 0 18px
          width: 56px
          height: 56px
          border-radius: 50%
          text-align: center
          box-sizing: border-box
          background: #141d27
          .logo
            width: 100%
            height: 100%
            box-sizing: border-box
            border-radius: 50%
            background: #2B343C
            &.highlight
              background: rgb(0, 160, 220)
            .icon-shopping_cart
              line-height: 44px
              font-size: 24px
              &.highlight
                color: #fff
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            line-height: 16px
            text-align: center
            border-radius: 16px
            font-size: 9px
            font-weight: 700
            color: #fff
            background: rgb(240, 20, 20)
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, .4)
        .price
          vertical-align: top
          display: inline-block
          margin-top: 12px
          line-height: 24px
          padding-right: 12px
          border-right 1px solid rgba(255, 255, 255, 0.1)
          font-size: 16px
          &.highlight
            color #fff
        .desc
          vertical-align: top
          display: inline-block
          line-height: 24px;
          margin: 12px 0 0 12px;
          font-size: 10px
          font-weight: 700
      .content-right
        flex: 0 0 105px
        width: 105px
        background: #2B333B
        .deliver
          text-align: center
          line-height: 48px
          font-size: 12px
          font-weight: 700
          background: #2b333b
          &.not-enough
            background: #2b333b
          &.enough
            background: #00b43c
            color: #fff
    .ball-contanier
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        /*color: #fff*/
        .inner
          display: inline-block
          width: 16px
          height: 16px
          border-radius: 50%
          background: rgb(0, 160, 220);
          &.drop-enter-active
            transition: all .4s cubic-bezier(0.49, -0.29, 0.75, .41)
            .inner
              transition: all .4s linear
    .shopcart-list
      position: absolute
      left: 0px
      top: 0px
      z-index: -1
      width: 100%
      transform: translate3d(0, -100%, 0);
      &.fold-enter-active, &.fold-leave-active
        transition: all .5s
      &.fold-enter, &.fold-leave-active
        transform: translate3d(0, 0, 0)
      .list-header
        padding: 0 18px
        height: 40px
        line-height: 40px
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        background #f3f5f7
        .title
          float: left
          font-size: 14px
          color: rgb(7, 17, 27)
        .empty
          float: right
          font-size: 12px
          color: rgb(0, 160, 220)
      .list-content
        padding: 0 18px
        max-height: 217px
        background: #fff
        overflow: hidden
        .food
          padding: 12px 0
          position: relative
          box-sizing: border-box
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            line-height: 24px
            font-size: 14px
            color: rgb(7, 17, 27)
          .price
            position: absolute
            right: 100px
            bottom: 12px
            line-height: 24px;
            font-weight: 700;
            font-size: 14px;
            color: rgb(240, 20, 20);
          .cartcontrol-wrapper
            position: absolute
            right: 6px
            bottom: 6px

  .list-mask
    position: fixed
    top: 0px
    left: 0px
    width: 100%
    height: 100%
    background: rgba(7, 17, 27, 0.6)
    backdrop-filter: blur(10px)
    &.fade-enter-active, .fade-leave-active
      transition: all .5s
    .fade-enter, .fade-leave-active
      opacity: 0
</style>
