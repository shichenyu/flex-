<template>
  <transition name="move">
    <div class="food" v-show="showFlag" ref="food">
      <div class="food-content">
        <div class="image-wrapper">
          <img :src="food.image" alt="">
          <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1>{{food.name}}</h1>
          <div class="desc">
            <span class="sell-count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span>
            <span class="old" v-if="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food"></cartcontrol>
          </div>
          <div class="buy" v-show="!food.count||food.count===0" @click.stop.prevent="addFirst(food,$event)">加入购物车</div>

        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <div class="title">商品信息</div>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="ratings">
          <ratingselect :select-type="selectType" :only-content="onlyContent"
                        :desc="desc" :ratings="food.ratings" @selRatings="filterRatings"
                        @isContent="isContent"></ratingselect>
          <div class="rating-wrapper">
            <ul class="has-rating" v-show="food.ratings&&food.ratings.length">
              <li v-show="needShow(rating.rateType,rating.text)" v-for="(rating,index) in food.ratings" :key="index"
                  class="rating-item">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" alt="" width="12" height="12">
                </div>
                <div class="time">{{rating.rateTime | formatDate}}</div>
                <p class="text">
                  <span
                    :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span><span
                  class="txt">{{rating.text}}</span>
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings||!food.ratings.length">暂无评价</div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import Vue from 'vue'
  import split from 'components/split/split'
  import ratingselect from 'components/ratingselect/ratingselect'
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import { formatDate } from '../../common/js/data.js'
//  import Bus from '../../common/js/eventBus.js'

  const ALL = 2
  export default {
    props: {
      food: {
        type: Object
      },
      ratings: []
    },
    data () {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
//    created () {
//      Bus.$on('ratingtype.select', selectType => {
//        this.selectType = selectType
//        this.$nextTick(() => {
//          this.scroll.refresh()
//        })
//      })
//      Bus.$on('content.toggle', onlyContent => {
//        this.onlyContent = onlyContent
//        this.$nextTick(() => {
//          this.scroll.refresh()
//        })
//      })
//    },
    methods: {
      show () {
        this.showFlag = true
        this.selectType = ALL
        this.onlyContent = true
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      },
      hide () {
        this.showFlag = false
      },
      addFirst (food, event) {
        if (!event._constructed) {
          return
        }
        this.$emit('cart.add', event.target)
        Vue.set(this.food, 'count', 1)
      },
      filterRatings (type) {
        this.selectType = type
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      isContent (bl) {
        this.onlyContent = bl
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      needShow (type, text) {
        if (this.onlyContent && !text) {
          return false
        }
        if (this.selectType === ALL) {
          return true
        } else {
          return type === this.selectType
        }
      }
    },
    components: {
      split,
      ratingselect,
      cartcontrol
    },
    filters: {
      formatDate (time) {
        let date = new Date(time)
        return formatDate(date, 'yyyy-MM-dd hh:mm')
      }
    }
  }
</script>
<style lang="stylus" rel="sheetstylus">
  @import "../../common/stylus/mixin.styl"
  .food
    position: fixed
    left: 0px
    top: 0px
    bottom: 48px
    width: 100%
    background: #fff
    &.move-enter-active, &.move-leave-active
      transition: all .2s linear
    &.move-enter, &.move-leave-active
      transform: translate3d(100%, 0, 0)
    .image-wrapper
      position: relative
      top: 0px
      width: 100%
      height: 0
      padding-top: 100%
      img
        position: absolute
        left: 0px
        top: 0px
        width: 100%
        height: 100%
      .back
        position: fixed
        left: 0
        top: 10px
        .icon-arrow_lift
          display: block
          padding: 10px
          font-size: 20px
          color: #fff
    .content
      position: relative
      padding: 18px
      h1
        margin-bottom: 8px
        line-height: 14px
        font-size: 14px
        font-weight: 700
        color: rgb(7, 17, 27)
      .desc
        margin-bottom: 18px
        line-height: 10px
        font-size: 10px
        color: rgb(147, 153, 159)
        .sell-count
          margin-right: 12px
      .price
        .now
          line-height: 24px
          font-size: 10px
          font-weight: 700
          color: rgb(240, 20, 20)
        .old
          text-decoration: line-through
          line-height: 24px
          font-size: 10px
          font-weight: 700
          color: rgb(147, 153, 159)
      .cartcontrol-wrapper
        position: absolute
        right: 12px
        bottom: 12px
      .buy
        position: absolute
        right: 18px
        bottom: 18px
        height: 24px
        line-height: 24px
        padding: 0 12px
        border-radius: 12px
        background: rgb(0, 160, 220)
        font-size: 10px
        color: #ffffff
        z-index: 10
        box-sizing: border-box
    .info
      padding: 18px
      .title
        line-height: 14px
        margin-bottom: 6px
        font-size: 14px
        color: rgb(7, 17, 27)
      .text
        padding: 0 8px
        line-height: 24px
        font-size: 12px
        color: rgb(77, 85, 93)
    .rating-wrapper
      margin: 0px 18px
      .has-rating
        .rating-item
          position: relative
          padding: 16px 0px
          border-1px(rgba(7, 17, 27, 0.1))
          .user
            position: absolute
            top: 16px
            right: 0px
            font-size: 0
            .name
              display: inline-block
              vertical-align: top
              margin-right: 6px
              line-height: 12px
              font-size: 10px
              color: rgb(147, 153, 159)
            img
              display: inline-block
              vertical-align: top
              border-radius: 50%
          .time
            line-height: 12px
            font-size: 10px
            color: rgb(147, 153, 159)
          .text
            line-height: 16px
            font-size: 12px
            color: rgb(7, 17, 27)
            .icon-thumb_down, .icon-thumb_up
              display: inline-block
              vertical-align: top
              line-height: 16px
              margin-right: 4px
              font-size: 12px
            .icon-thumb_down
              color: rgb(7, 17, 27)
            .icon-thumb_up
              color: rgb(0, 160, 220)
      .no-rating
        font-size: 10px
        color: rgb(147, 153, 159)
</style>
