<template>
  <div class="seller" ref="seller">
    <div class="seller-wrapper">
      <div class="overview">
        <h1>{{seller.name}}</h1>
        <div class="desc">
          <star :size="24" :score="seller.score"></star>
          <span class="rank">({{seller.ratingCount}})</span>
          <span class="sellnum">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="sell-info">
          <li class="sell-text">
            <span class="title">起送价</span>
            <div class="content">
              <span class="price">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="sell-text">
            <span class="title">商家配送</span>
            <div class="content">
              <span class="price">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="sell-text">
            <span class="title">平均配送时间</span>
            <div class="content">
              <span class="price">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <!--<div class="favorite" @click="togglefavorite">-->
        <!--<span class="icon-favorite" :class="{'active':favorite}"></span>-->
        <!--<span class="text">{{ favoriteText }}</span>-->
        <!--</div>-->
      </div>
      <split></split>
      <div class="bulletin-wrapper">
        <h1>公告与活动</h1>
        <p class="bulletin-text">{{seller.bulletin}}</p>
        <ul v-if="seller.supports" class="supports">
          <li class="seller-item" v-for="(item, index) in seller.supports" :key="index">
            <span class="icon" :class="classMap[seller.supports[index].type]"></span>
            <span class="text">{{seller.supports[index].description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1>商家实景</h1>
        <div class="pics-wrapper" ref="picsWrapper">
          <ul class="pic-list" ref="picList">
            <li class="list-item" v-for="(pic,index) in seller.pics" :key="index">
              <img :src="pic" alt="">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="seller-info">
        <h1>商家信息</h1>
        <ul>
          <li class="info-list" v-for="(info,index) in seller.infos" :key="index">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import star from 'components/star/star'
  import split from 'components/split/split'

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {}
    },
    watch: {
      'seller' () {
        this.$nextTick(() => {
          this._initSellerScroll()
          this._initPicSroll()
        })
      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    },
    mounted () {
      this.$nextTick(() => {
        this._initSellerScroll()
        this._initPicSroll()
      })
    },
    methods: {
      togglefavorite () {
      },
      _initSellerScroll () {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        })
      },
      _initPicSroll () {
        if (this.seller.pics) {
          let picWidth = 120
          let margin = 6
          let width = (picWidth + margin) * this.seller.pics.length - margin
          this.$refs.picList.style.width = width + 'px'
          this.$nextTick(() => {
            this.picScroll = new BScroll(this.$refs.picsWrapper, {
              scrollX: true,
              eventPassthrough: 'vertical',
              click: true
            })
          })
        }
      }
    },
    components: {
      star,
      split
    }
  }
</script>
<style lang="stylus" rel="sheetstylus">
  @import "../../common/stylus/mixin.styl"
  .seller
    position: absolute
    top: 174px
    bottom: 0px
    left: 0px
    width: 100%
    overflow: hidden
    .seller-wrapper
      .overview
        padding: 18px 18px 0px 18px
        border-1px(rgba(7, 17, 27, 0.1))
        font-size: 0
        h1
          margin-bottom: 8px
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
        .desc
          display: inline-block
          width: 100%
          padding-bottom: 18px
          border-bottom: 1px solid rgba(7, 17, 27, 0.1)
          .star
            display: inline-block
            margin-right: 8px
          .rank
            display: inline-block
            margin-right: 12px
            line-height: 18px
            font-size: 10px
            color: rgb(77, 65, 93)
          .sellnum
            display: inline-block
            line-height: 18px
            font-size: 10px
            color: rgb(77, 65, 93)
        .sell-info
          display: flex
          padding: 18px 0px
          .sell-text
            flex: 1
            text-align: center
            border-right: 1px solid rgba(7, 17, 27, 0.1)
            &:last-child
              border: none
            .title
              display: inline-block
              margin-bottom: 4px
              line-height: 10px
              font-size: 10px
              color: rgb(147, 152, 159)
            .content
              font-size: 10px
              .price
                display: inline-block
                line-height: 24px
                font-size: 24px
                color: rgb(7, 17, 27)
        .favorite
          position: absolute
          width: 50px
          right: 11px
          top: 18px
          text-align: center
          font-size: 10px
          .icon-favorite
            display: inline-block
            margin-bottom: 4px
            line-height: 24px
            font-size: 24px
            color: #d4d6d9
            &.active
              color: rgb(240, 20, 20)
          .text
            display: inline-block
            line-height: 10px
            font-size: 10px
            color: rgb(77, 85, 93)
      .bulletin-wrapper
        padding: 18px
        h1
          margin-bottom: 8px
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
        .bulletin-text
          padding: 0px 12px 16px 12px
          line-height: 24px
          border-bottom: 1px solid rgba(7, 17, 27, 0.1)
          font-size: 12px
          color: rgb(240, 20, 20)
        .supports
          .seller-item
            padding: 16px 12px
            border-bottom: 1px solid rgba(7, 17, 27, 0.1)
            &:last-child
              border-bottom: 0
            .icon
              display: inline-block
              vertical-align: top
              width: 16px
              height: 16px
              margin-right: 6px
              background-size: 16px 16px
              background-repeat: no-repeat
              &.decrease
                bg-image('decrease_4')
              &.discount
                bg-image('discount_4')
              &.guarantee
                bg-image('guarantee_4')
              &.invoice
                bg-image('invoice_4')
              &.special
                bg-image('special_4')
            .text
              line-height: 16px
              font-size: 12px
              color: rgb(7, 17, 27)
      .pics
        padding: 18px 0px 18px 18px
        h1
          margin-bottom: 12px
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
        .pics-wrapper
          width: 100%
          overflow: hidden
          white-space: nowrap
          .pic-list
            font-size: 0
            .list-item
              display: inline-block
              margin-right: 6px
              width: 120px
              height: 90px
              img
                width: 120px
                height: 90px
      .seller-info
        padding: 18px 18px 0px 18px
        h1
          padding-bottom: 12px
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
          border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .info-list
          padding: 16px 12px
          line-height: 16px
          font-size: 12px
          color: rgb(7, 17, 27)
          border-bottom: 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border: 0
</style>
