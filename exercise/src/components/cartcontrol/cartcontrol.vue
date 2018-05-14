<template>
  <div class="cart-wrapper">
    <transition name="move">
      <div class="cart-desc" v-show="food.count>0" @click.stop.prevent="descart">
        <i class="inner icon-remove_circle_outline"></i>
      </div>
    </transition>
    <div class="cart-num" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addcart"></div>
  </div>
</template>
<script type="text/ecmascript-6">
  import Vue from 'vue'
  import Bus from '../../common/js/eventBus.js'
  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      descart (event) {
        if (!event._constructed) {
          return
        }
        if (this.food.count) {
          this.food.count--
        }
      },
      addcart (event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
//        this.$root.eventHub.$emit('cart.add', event.target) // 传输点击的目标元素
        Bus.$emit('cart.add', event.target)
      }
    }
  }
</script>
<style lang="stylus" rel="sheetstylus">
  .cart-wrapper
    font-size: 0
    .cart-desc
      display: inline-block
      vertical-align: top
      padding: 6px
      .inner
        display: inline-block
        line-height: 24px
        font-size: 24px
        color: rgb(0, 160, 220)
      &.move-enter-active, &.move-leave-active
        transition: all .5s
        transform: translate3d(0, 0, 0)
        .inner
          transition: all .5s
          transform: rotate(0deg)
      &.move-enter, &.move-leave-active
        opacity: 0
        transform: translate3d(24px, 0, 0)
        .inner
          transform: rotate(180deg)
    .cart-num
      display: inline-block
      vertical-align: top
      line-height: 24px
      font-size: 10px
      padding: 6px
    .cart-add
      display: inline-block
      vertical-align: top
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
</style>
