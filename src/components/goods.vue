<template>
  <div class="goods">
      <div class="menu-wrapper" ref="menuWrapper">
        <ul>
          <li v-for="(item,index) in goods" class="menu-item" :class="{'current': currentIndex === index}" 
          	@click="selectMenu(index,$event)">
            <span class="text border-1px">
              <span v-show="item.type>0" class="icon" :class="classsMap[item.type]"></span>{{item.name}}
            </span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" ref="foodsWrapper">
        <ul>
          <li v-for="item in goods" class="food-list-hook">
            <h1 class="title">{{item.name}}</h1>
            <ul>
              <li v-for="food in item.foods" class="food-item border-1px">
                <div class="icon">
                  <img :src="food.icon" width="57" height="57" alt="">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}</span>
                    <span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">￥{{food.price}}</span><span v-show="food.oldPrice" class="old">￥{{food.oldPrice}}</span>
                  </div>
                  <div class="cartControl-wrapper">
                  <cartControl :food="food" @increment="incrementTotal"></cartControl>
                </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
     <div>
      <shopCart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice"
                :min-price="seller.minPrice" ref="shopCart"></shopCart>
      <food :food="selectedFood" ref="food"></food>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll';
import data from '../../data.json';
import shopCart from './shopCart.vue';
import cartControl from './cartControl.vue';
import food from './food.vue';
export default {
  name: 'goods',
   props: {
      seller: {
        type: Object
      }
    },
  data () {
    return {
      msg: 'this is goods date',
      goods: {},
      listHeight: [],
      scrollY: 0,
      selectedFood: {},
      currentIndex: 0
    }
  },
  created () {
    this.classsMap = ['decrease', 'descount', 'spacial', 'invoice', 'guarantee']
//  this.$http.get('')
//.then((response) => {
//  this.goods = response.data.goods
//  this.$nextTick(() => {
//    this._initScroller()
//    this._caculateHeight()
//  })
//})
//.catch(function (error) {
//  console.log(error)
//})
    this.goods = data.goods;
    this.$nextTick(() => {
        this._initScroll();
        this._caculateHeight();
      });
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  },
  mounted() {
//      this.$nextTick(() => {
//       this._initScroll();
//      });
    },
   computed: {
//  currentIndex() {
//      for (let i = 0; i < this.listHeight.length; i++) {
//        let height = this.listHeight[i];
//        let height2 = this.listHeight[i + 1];
//        if (!height2 || (this.scrolly >= height && this.scrolly < height2)) {
//          return i;
//        }
//      }
//      return 0;
//    },
    selectFoods() { //自动将所有的goods.food添加一个count属性,方便做数量运算
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
  },
  methods: {
    _initScroll () {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        probetype: 3
      })
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
        console.log('ddd')
      })
    },
    _caculateHeight () {
      let foodList = this.$refs.foodsWrapper.querySelectorAll('.food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    selectMenu (index, event) {
     // console.log(index)
      var foodList = this.$refs.foodsWrapper.querySelectorAll('.food-list-hook')
     // console.log(foodList)
      var el = foodList[index]
      //console.log(el)
      this.foodsScroll.scrollToElement(el, 300)
      this.currentIndex = index
    },
    selectFood(food, event){
            if (!event._constructed) {//忽略掉BScroll的事件
              return;
            }
            this.selectedFood = food; //写入当前选择的food
            this.$refs.food.show(); //显示当前选择的food的详情页
      },
     incrementTotal(target) {
        this.$refs.shopCart.drop(target);
      }
  },
    components: {
      shopCart,
      cartControl,
      food
    }
  
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" rel="stylesheet/stylus">
  @import '../common/stylus/mixin.styl'
ul{
	padding: 0px;
	text-align: center;
}  
li{
	list-style: none;
	border-bottom: .01px solid #646464;
}
  .goods
    display flex
    width 100%
    position absolute
    top 200px
    bottom 46px
    overflow hidden
    .menu-wrapper
      flex 0 0 100px
      /*width是为了兼容安卓设备*/
      width 80px
      background #f3f5f7
      font-size 0
      .menu-item
        display table
        height 54px
        width 100px
        line-height 14px
        &.current
          position relative
          margin-top -1px
          z-index 10
          background #fff
          font-weight 700
          .text
            border-none()
        .icon
          display inline-block
          width 12px
          height 12px
          margin-right 2px
          background-size 12px 12px
          background-repeat no-repeat
          &.decrease
              bg-img('../../resource/img/decrease_1')
          &.discount
              bg-img('../../resource/img/discount_1')
          &.guarantee
              bg-img('../../resource/img/guarantee_1')
          &.invoice
              bg-img('../../resource/img/invoice_1')
          &.special
              bg-img('../../resource/img/special_1')
        .text
          display table-cell
          vertical-align middle
          width 56px
          border-1px(rgba(7, 17, 27, 0.1))
          font-size 12px
    .foods-wrapper
      flex 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left: 2px solid #d9dde1
        font-size: 12px
        color rgb(147, 153, 159)
        background #f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-bottom 1px solid #e7e7e7
        position relative
        &:last-child
          border-none()
          margin-bottom: 0
          border-bottom 0
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1       
          .name
            margin: 2px 0 8px 0
            height: 14px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
          .desc, .extra
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
          .desc
            margin-bottom: 8px
            line-height: 12px
          .extra
            &.count
              margin-right: 12px
          .price
            font-weight: 700
            line-height: 24px
            .now
              font-size: 14px
              margin-right: 8px
              color: rgb(240, 20, 20)
            .old
              color: rgb(147, 153, 159)
              text-decoration: line-through
              font-size: 10
          .cartControl-wrapper
            position absolute
            right 20px
            bottom 12px 
</style>
