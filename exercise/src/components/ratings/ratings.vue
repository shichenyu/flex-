<template>
  <div class="ratings" ref="ratings">
    <div class="rating-content">
      <div class="overview">
        <div class="overview-left">
          <div class="score">{{seller.score}}</div>
          <div class="review">综合评分</div>
          <div class="more">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <div class="star">
              <star :size="36" :score="seller.score"></star>
            </div>
            <span class="scorenum">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <div class="star">
              <star :size="36" :score="seller.score"></star>
            </div>
            <span class="scorenum">{{seller.foodScore}}</span>
          </div>
          <div class="delivery">
            <span class="title">送达时间</span>
            <span class="time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <div class="rating-wrapper">
        <ratingselect :select-type="selectType" :only-content="onlyContent"
                      :desc="desc" :ratings="ratings" @sellRatings="filterRatings"
                      @isContent="isContent"></ratingselect>
      </div>
      <div class="ratings-text">
        <div class="rating-item" v-for="(rating,index) in ratings" :key="index"
             v-show="needShow(rating.rateType,rating.text)">
          <div class="avatar">
            <img :src="rating.avatar" alt="" width="28" height="28">
          </div>
          <div class="text-content">
            <h1>{{rating.name}}</h1>
            <div class="score">
              <div class="star">
                <star :size="24" :score="rating.score"></star>
              </div>
              <span class="time" v-show="rating.deliveryTime>0">{{rating.deliveryTime}}分钟送达</span>
            </div>
            <p class="text">{{rating.text}}</p>
            <div class="recommend">
              <span class="icon-thumb_up"></span>
              <span class="item" v-for="(item,index) in rating.recommend" :key="index">{{item}}</span>
            </div>
            <div class="reviewtime">{{rating.rateTime | formatDate}}</div>
            <!--{{rating.rateTime | formatDate}}-->
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import star from 'components/star/star'
  import split from 'components/split/split'
  import ratingselect from 'components/ratingselect/ratingselect'
  import { formatDate } from '../../common/js/data.js'

  const ERR_OK = 0
  const ALL = 2
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    },
    created () {
      this.$http.get('/api/ratings').then((res) => {
        res = res.body
        if (res.errno === ERR_OK) {
          this.ratings = res.data
//          console.log(this.ratings)
          this.$nextTick(() => {
            this._initRatingsScroll()
          })
        }
      })
    },
    methods: {
      _initRatingsScroll () {
        this.scroll = new BScroll(this.$refs.ratings, {
          click: true
        })
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
          return this.selectType === type
        }
      }
    },
    filters: {
      formatDate (time) {
        let date = new Date(time)
        return formatDate(date, 'yyyy-MM-dd hh:mm')
      }
    },
    components: {
      star,
      split,
      ratingselect
    }
  }
</script>
<style lang="stylus" rel="sheetstylus">
  @import "../../common/stylus/mixin.styl"
  .ratings
    position: absolute
    top: 174px
    bottom: 0px
    left: 0px
    width: 100%
    overflow: hidden
    .rating-content
      .overview
        display: flex
        padding: 18px 0px
        .overview-left
          flex: 0 0 137px
          width: 137px
          padding: 6px 0px
          text-align: center
          border-right: 1px solid rgba(7, 17, 27, 0.1)
          .score
            line-height: 28px
            margin-bottom: 6px
            font-size: 24px
            color: rgb(255, 153, 0)
          .review
            line-height: 12px
            margin-bottom: 8px
            font-size: 12px
            color: rgb(7, 17, 27)
          .more
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
        .overview-right
          flex: 1
          padding: 6px 0px 6px 24px
          .score-wrapper
            margin-bottom: 8px
            font-size: 0
            .title
              display: inline-block
              vertical-align: top
              margin-right: 12px
              line-height: 18px
              font-size: 12px
              color: rgb(7, 17, 27)
            .star
              display: inline-block
              margin-right: 12px
            .scorenum
              display: inline-block
              vertical-align: top
              line-height: 18px
              font-size: 12px
              color: rgb(255, 153, 0)
          .delivery
            .title
              display: inline-block
              margin-right: 12px
              line-height: 18px
              font-size: 12px
              color: rgb(7, 17, 27)
            .time
              line-height: 18px
              font-size: 12px
              color: rgb(147, 153, 159)
      .ratings-text
        padding: 0px 18px
        .rating-item
          display: flex
          padding: 18px 0px
          border-1px(rgba(7, 17, 27, 0.1))
          .avatar
            flex: 0 0 28px
            width: 28px
            margin-right: 12px
            img
              border-radius: 50%
          .text-content
            flex: 1
            position: relative
            h1
              margin-bottom: 4px
              line-height: 12px
              font-size: 10px
              color: rgb(7, 17, 27)
            .score
              margin-bottom: 6px
              .star
                display: inline-block
                margin-right: 6px
              .time
                line-height: 12px
                font-size: 10px
                font-weight: 200
                color: rgb(147, 153, 159)

            .text
              margin-bottom: 8px
              line-height: 18px
              font-size: 12px
              color: rgb(7, 17, 27)
            .recommend
              font-size 0
              .icon-thumb_up
                display: inline-block
                vertical-align: top
                margin-right: 8px
                line-height: 16px
                font-size: 12px
                color: rgb(0, 160, 220)
              .item
                display: inline-block
                vertical-align: top
                padding: 0px 6px
                margin-bottom: 4px
                line-height: 16px
                font-size: 9px
                color: rgb(147, 153, 159)
                border: 1px solid rgba(7, 17, 27, 0.1)
            .reviewtime
              position: absolute
              top: 0px
              right: 0px
              line-height: 12px
              font-size: 10px
              color: rgb(147, 153, 159)
</style>
