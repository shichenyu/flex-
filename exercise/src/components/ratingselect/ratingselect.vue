<template>
  <div class="ratingselect-wrapper">
    <div class="ratingcontent">
      <div class="title">商品评价</div>
      <div class="ratingsbox">
        <span @click="select(2,$event)" class="block positive" :class="{'active':selectType===2}">{{ desc.all }}<span
          class="count">{{ratings.length}}</span></span>
        <span @click="select(0,$event)" class="block positive" :class="{'active':selectType===0}">{{ desc.positive
          }}<span
            class="count">{{positives.length}}</span></span>
        <span @click="select(1,$event)" class="block negative" :class="{'active':selectType===1}">{{ desc.negative
          }}<span
            class="count">{{negatives.length}}</span></span>
      </div>
      <div class="readonly">
        <span class="icon-check_circle" @click="toggleonly" :class="{'on':onlyContent}"></span>
        <span class="text">只看有内容的评价</span>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  //  import Bus from '../../common/js/eventbus.js'

  const ALL = 2
  const POSITIVE = 0
  const NEGATIVE = 1
  export default {
    props: {
      ratings: {
        type: Array,
        default () {
          return []
        }
      },
      desc: {
        type: Object,
        default () {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          }
        }
      }
    },
    data () {
      return {
//        selectType2: this.selectType,
//        onlyContent2: this.onlyContent
        selectType: {
          type: Number,
          default: ALL
        },
        onlyContent: {
          type: Boolean,
          default: false
        }
      }
    },
    computed: {
      positives () {
        return this.ratings.filter((item) => {
          return item.rateType === POSITIVE
        })
      },
      negatives () {
        return this.ratings.filter((item) => {
          return item.rateType === NEGATIVE
        })
      }
    },
    methods: {
      select (type, event) {
        if (!event._constructed) {
          return
        }
        this.selectType = type
//        Bus.$emit('ratingtype.select', type)
        this.$emit('sellRatings', this.selectType)
      },
      toggleonly (event) {
        if (!event._constructed) {
          return
        }
        this.onlyContent = !this.onlyContent
//        Bus.$emit('content.toggle', this.onlyContent2)
        this.$emit('isContent', this.onlyContent)
      }
    }
  }
</script>
<style lang="stylus" rel="sheetstylus">
  @import "../../common/stylus/mixin.styl"
  .ratingselect-wrapper
    .ratingcontent
      .title
        padding-top: 18px
        margin-left: 18px
        line-height: 14px
        font-size: 14px
        color: rgb(7, 17, 27)
      .ratingsbox
        padding: 18px 0px
        margin: 0px 18px
        border-1px(rgba(7, 17, 27, 0.1))
        color: rgb(77, 85, 93)
        .block
          padding: 8px 12px
          line-height: 16px
          border-radius: 1px
          font-size: 12px
          &.positive
            background: rgba(0, 160, 220, 0.2)
            &.active
              background: rgb(0, 160, 220)
              color: #fff
          &.negative
            background: rgba(77, 85, 93, 0.2)
            &.active
              background: rgb(77, 85, 93)
              color: #fff
      .readonly
        padding: 12px 18px
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .icon-check_circle
          display: inline-block
          vertical-align: top
          line-height: 24px
          font-size: 24px
          color: rgb(147, 159, 153)
          &.on
            color: #00c850
        .text
          display: inline-block
          vertical-align: top
          line-height: 24px
          font-size: 12px
          color: rgb(147, 159, 153)
</style>
