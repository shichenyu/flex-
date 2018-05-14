<template>
  <div class="header">
    <div class="header-content">
      <div class="header-logo">
        <img :src="seller.avatar" alt="" width="64" height="64"/>
      </div>
      <div class="header-info">
        <div class="name-wrapper">
          <span class="icon"></span><span class="name">{{seller.name}}</span>
        </div>
        <p class="desc">{{seller.description}}/{{seller.deliveryTime}}分钟送达</p>
        <div class="supports" v-if="seller.supports">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span><span
          class="description">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div class="supportnum" v-if="seller.supports" @click="showmore">
        <span class="num">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="header-bulletin" @click="showmore">
      <span class="icon"></span><span class="bulletin">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="moreinfo" v-show="this.show">
      <div class="more-wrapper">
        <div class="more">
          <div class="name">{{seller.name}}</div>
          <div class="star-wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div class="countinfo">
            <div class="title">
              <div class="line"></div>
              <div class="name">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li v-for="(support, index) in seller.supports" :key="index">
                <span class="icon" :class="classMap[seller.supports[index].type]"></span><span
                class="desc">{{seller.supports[index].description}}</span>
              </li>
            </ul>
          </div>
          <div class="sellerinfo">
            <div class="title">
              <div class="line"></div>
              <div class="name">商家公告</div>
              <div class="line"></div>
            </div>
            <p class="seller-bulletin">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <div class="close" @click="hidemore">
        <i class="icon-close"></i>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import star from 'components/star/star'

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        show: false
      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    },
    methods: {
      showmore () {
        this.show = true
      },
      hidemore () {
        this.show = false
      }
    },
    components: {
      star
    }
  }
</script>
<style lang="stylus" rel="sheetstylus">
  @import "../../common/stylus/mixin.styl"
  .header
    position: relative
    padding: 24px 12px 0 24px
    background: rgba(7, 17, 27, 0.5)
    .header-content
      display: flex
      width: 100%
      position: relative
      .header-logo
        display: inline-block
        margin-right: 16px
        img
          border-radius: 4px
      .header-info
        flex: 1
        display: inline-block
        .name-wrapper
          .icon
            display: inline-block
            vertical-align: top
            width: 30px
            height: 18px
            bg-image('brand')
            background-size: 30px 18px
            background-repeat: no-repeat
          .name
            display: inline-block
            line-height: 18px
            margin-left: 6px
            font-size: 16px
            color: rgb(255, 255, 255)
            font-weight: bold
        .desc
          line-height: 24px
          margin-top: 8px
          margin-bottom: 10px
          font-size: 12px
          color: rgb(255, 255, 255)
        .supports
          .icon
            display: inline-block
            vertical-align: top
            margin-right: 4px
            margin-bottom: 2px
            width: 12px
            height: 12px
            background-size: 12px 12px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_1')
            &.discount
              bg-image('discount_1')
            &.special
              bg-image('special_1')
            &.invoice
              bg-image('invoice_1')
            &.guarantee
              bg-image('guarantee_1')
          .description
            display: inline-block
            line-height: 12px
            font-size: 10px
            color: rgb(255, 255, 255)
            font-weight: 200
      .supportnum
        position: absolute
        right: 0px
        bottom: 0px
        padding: 7px 8px
        border-radius: 20px
        background-color: rgba(0, 0, 0, 0.2)
        .num
          display: inline-block
          line-height: 12px
          margin-right: 2px
          font-size: 10px
          color: rgb(255, 255, 255)
          font-weight: 200
        .icon-keyboard_arrow_right
          display: inline-block
          line-height: 12px
          color: #fff
          font-size: 10px
    .header-bulletin
      position: relative
      padding: 0 12px
      height: 28px
      line-height: 28px
      overflow: hidden
      white-space: nowrap
      text-overflow: ellipsis
      color: rgb(255, 255, 255)
      .icon
        display: inline-block
        width: 22px
        height: 12px
        background-size: 22px 12px
        background-repeat: no-repeat
        bg-image('bulletin')
      .bulletin
        line-height: 28px
        margin-left: 4px
        font-size: 10px
        font-weight: 200
      .icon-keyboard_arrow_right
        color: #fff
        position: absolute
        bottom: 3px
        right: 0px
    .moreinfo
      position: fixed
      top: 0
      left: 0
      overflow: auto
      z-index: 100
      width: 100%
      height: 100%
      background: rgba(7, 17, 27, 0.8)
      .more-wrapper
        width: 100%
        min-height: 100%
        .more
          padding: 64px 36px 32px
          .name
            line-height: 16px
            text-align: center
            font-size: 16px
            color: rgb(255, 255, 255)
            font-weight: 700
          .star-wrapper
            margin: 16px 0 28px 0
            text-align: center;
          .countinfo
            .title
              display: flex
              .line
                flex: 1
                position: relative
                top: -6px
                border-bottom: 1px solid #61676D
              .name
                line-height: 14px
                margin: 0 12px
                font-size: 14px
                color: rgb(255, 255, 255)
                font-weight: 700
            .supports
              margin: 24px 0 28px 0
              .icon
                display: inline-block
                vertical-align: top
                margin-right: 6px
                margin-bottom: 12px
                width: 16px
                height: 16px
                background-size: 16px 16px
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.special
                  bg-image('special_2')
                &.invoice
                  bg-image('invoice_2')
                &.guarantee
                  bg-image('guarantee_2')
              .desc
                display: inline-block
                line-height: 12px
                font-size: 12px
                color: rgb(255, 255, 255)
                font-weight: 200
          .sellerinfo
            .title
              display: flex
              .line
                flex: 1
                position: relative
                top: -6px
                border-bottom: 1px solid #61676D
              .name
                line-height: 14px
                margin: 0 12px
                font-size: 14px
                color: rgb(255, 255, 255)
                font-weight: 700
            .seller-bulletin
              padding: 0 12px
              line-height: 24px
              font-size: 12px
              color: rgb(255, 255, 255)
              font-weight: 200
      .close
        margin: -64px auto 0
        text-align: center
        width: 100%
        font-size: 32px
        color: rgba(255, 255, 255, 0.5)
</style>
