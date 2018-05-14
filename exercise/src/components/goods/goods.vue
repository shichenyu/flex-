<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item, index) in goods" class="menu-item" :key="index" :class="{'current':currentIndex===index}"
            @click="selectMenu(index,$event)">
          <span class="name">
            <span class="icon" v-if="item.type>0" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="(item,index) in goods" :key="index" class="food-list food-list-hook">
          <h1 class="title">{{ item.name }}</h1>
          <ul>
            <li v-for="(food,index) in item.foods" :key="index" class="food-item" @click="selectFood(food,$event)">
              <div class="icon">
                <img :src="food.icon" alt="" width="57" height="57">
              </div>
              <div class="content">
                <h2>{{food.name}}</h2>
                <span class="desc">{{food.description}}</span>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-if="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food" @increment="incrementTotal"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart ref="shopcart" :delivery-price="seller.deliveryPrice"
              :min-price="seller.minPrice"
              :select-foods="selectFoods"></shopcart>
    <food :food="selectedFood" ref="food"></food>
  </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import shopcart from 'components/shopcart/shopcart'
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import food from 'components/food/food'
  import Bus from '../../common/js/eventBus.js'

  const ERR_OK = 0
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        lineHeight: [],
        scrollY: 0,
        selectedFood: {}
      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      this.$http.get('/api/goods').then((res) => {
        res = res.body
        if (res.errno === ERR_OK) {
          this.goods = res.data
          console.log(this.goods)
          this.$nextTick(() => {
            this._initGoodsScroll()
            this._calculateHeight()
          })
        }
      })
      Bus.$on('cart.add', el => {
        this.$nextTick(() => {
          this.$refs.shopcart.drop(el)
        })
      })
    },
    computed: {
      currentIndex () {
        for (let i = 0; i < this.lineHeight.length; i++) {
          let height1 = this.lineHeight[i]
          let height2 = this.lineHeight[i + 1]
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i
          }
        }
        return 0
      },
      selectFoods () {
        let foods = []
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        })
        return foods
      }
    },
    methods: {
      _initGoodsScroll () {
        this.goodsScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        })
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3
        })
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      _calculateHeight () {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
        let height = 0
        this.lineHeight.push(height)
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i]
          height += item.clientHeight
          this.lineHeight.push(height)
        }
      },
      selectMenu (index, event) {
        if (!event._constructed) {
          return
        }
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
        let el = foodList[index]
        this.foodsScroll.scrollToElement(el, 300)
      },
      incrementTotal (target) {
        this.$refs.shopCart.drop(target)
      },
      selectFood (food, event) {
        if (!event._constructed) {
          return
        }
        this.selectedFood = food
        this.$refs.food.show()
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    }
  }
</script>
<style lang="stylus" rel="sheetstylus">
  @import "../../common/stylus/mixin.styl"
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    /*z-index :100*/
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        width: 56px
        height: 54px
        padding: 0 12px
        line-height: 14px
        font-size: 12px
        &.current
          margin-top: -1px
          background: #fff
          font-weight: 700
        .icon
          display: inline-block
          vertical-align: top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .name
          display: table-cell
          width: 56px
          vertical-align: middle
          font-size: 12px
          border-1px(rgba(7, 17, 27, 0.1))
    .foods-wrapper
      flex: 1
      .title
        padding-left 14px
        line-height: 26px
        height: 26px
        font-size: 12px
        color: rgb(147, 153, 159)
        background: #f3f5f7
        border-left: 2px solid #d9dde1
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-bottom: 0
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          h2
            margin: 2px 0px 8px 0px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
          .desc
            margin-bottom: 8px
            color: rgb(147, 153, 159)
            font-size: 10px
          .extra
            color: rgb(147, 153, 159)
            font-size: 10px
            .count
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
            right: 0px
            bottom: 12px
</style>
