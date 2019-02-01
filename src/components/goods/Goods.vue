<template>
    <div class="goods">
        <!-- 分类列表 -->
        <div class="menu-wrapper" ref="menuScroll">
          <ul>
            <!-- 专场 -->
            <li class="menu-item" :class="{'current': currentIndex === 0}" @click="selectMenu(0)">
              <p class="text">
                <img class="icon" :src="container.tag_icon" alt="" v-if="container.tag_icon">
                {{container.tag_name}}
              </p>
            </li>

            <li class="menu-item" v-for="(item, index) in goods" :key="index" :class="{'current': currentIndex === index+1}" @click="selectMenu(index + 1)">
              <p class="text">
                <img class="icon" :src="item.icon" alt="" v-if="item.icon">
                {{item.name}}
              </p>
            </li>
          </ul>
        </div>
        <!-- 商品列表 -->
        <div class="foods-wrapper" ref="foodScroll">
          <ul>
            <!-- 专场 -->
            <li class="container-list food-list-hook">
              <div v-for="(item, index) in container.operation_source_list" :key="index">
                <img :src="item.pic_url" alt="">
              </div>
            </li>
            <!-- 具体分类 -->
            <li v-for="(item, index) in goods" :key="index" class="food-list food-list-hook">
              <h3 class="title">{{item.name}}</h3>
              <!-- 具体的商品列表 -->
              <ul>
                <li class="food-item" v-for="(food, index) in item.spus" :key="index">
                  <div class="icon" :style="'background-image:url('+ food.picture +');'"></div>
                  <div class="content">
                    <h3 class="name">{{food.name}}</h3>
                    <p class="desc" v-if="food.description">{{food.description}}</p>
                    <div class="extra">
                      <span class="saled">{{food.month_saled_content}}</span>
                      <span class="praise">{{food.praise_content}}</span>
                    </div>
                    <img class="product" v-if="food.product_label_picture" :src="food.product_label_picture" alt="">
                    <p class=price>
                      <span class="text">${{food.min_price}}</span>
                      <span class="unit">/{{food.unit}}</span>
                    </p>
                  </div>
                </li>
              </ul>
            </li>
          </ul>
        </div>
    </div>
</template>

<script>
import BScroll from 'better-scroll'

export default {
  data () {
    return {
      container: {},
      goods: [],
      listHeight: [],
      menuScroll: {},
      foodScroll: {},
      scrollY : 0
    }
  },
  // 计算属性不能接收参数
  created () {
    fetch('/api/goods')
      .then(res => {
        return res.json()
      })
      .then(data => {
        this.container = data.data.container_operation_source
        this.goods = data.data.food_spu_tags

        this.$nextTick(() => {
          this.initScroll()

          // 计算分类的区间高度
          this.calculateHeight()
          //监听滚动的位置
          //根据滚动位置，确认下标，与左侧对应
          //通过下标，实现点击左侧滚动右侧
        })
      })
  },
  methods: {
    initScroll () {
      this.menuScroll = new BScroll(this.$refs.menuScroll,{click: true})
      this.foodScroll = new BScroll(this.$refs.foodScroll,{probeType: 3, click: true})

      //foodScroll监听事件
      this.foodScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    calculateHeight () {
      let foodList = this.$refs.foodScroll.getElementsByClassName('food-list-hook')

      let height = 0
      this.listHeight.push(height)

      for(let i = 0; i < foodList.length; i++){
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    selectMenu (index) {
      // console.log(index)
      let foodList = this.$refs.foodScroll.getElementsByClassName('food-list-hook')

      let element = foodList[index]

      //滚动到对应元素位置
      this.foodScroll.scrollToElement(element, 1000)
    }
  },
  computed: {
    currentIndex () {
      for(let i = 0; i < this.listHeight.length; i++){
        //获取商品区间的范围
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i+1]

        if (!height2 || (this.scrollY >= height1 && this.scrollY <= height2)) {
          return i
        }
      }
      return 0
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.goods{
  display: flex;
  position: absolute;
  top: 190px;
  bottom: 51px;
  overflow: hidden;
  width: 100%;
}

.goods .menu-wrapper{
  flex: 0 0 85px;
  background: #f4f4f4;
}

.goods .foods-wrapper{
  flex: 1;
  /* background: orange; */
}

.goods .menu-wrapper .menu-item{
  padding: 16px 23px 15px 10px;
  border-bottom: 1px solid #e4e4e4;
}

.goods .menu-wrapper .menu-item.current{
  background-color: #ffffff;
}

/* .goods .menu-wrapper .menu-item:last-child{
  border-bottom: 0;
} */

.goods .menu-wrapper .menu-item .text{
  font-size: 16px;
  color: #333333;
  line-height: 17px;
  vertical-align: middle;
  /* 超出... */
  -webkit-line-clamp: 2;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.goods .menu-wrapper .menu-item .text .icon{
  width: 15px;
  height: 15px;
  vertical-align: middle;
}

/* 专场样式 */
.goods .foods-wrapper .container-list{
  padding: 11px 11px 0 11px;
  border-bottom: 1px solid #e4e4e4;
}

.goods .foods-wrapper .container-list img{
  width: 100%;
  margin-bottom: 11px;
  border-radius: 5px;
}

/* 具体分类商品布局 */
.goods .foods-wrapper .food-list{
  padding: 10px;
  border-bottom: 1px solid #e4e4e4;
}

.goods .foods-wrapper .food-list .title{
  height: 13px;
  font-size: 13px;
  margin-left: 7px;
  margin-bottom: 12px;
  position: relative;
}

.goods .foods-wrapper .food-list .title:before{
  width: 2px;
  height: 13px;
  display: block;
  content: '';
  background-color: orange;
  position: absolute;
  top: 0;
  left: -7px;
}

.goods .foods-wrapper .food-list .food-item{
  display: flex;
  margin-bottom: 25px;
}

.goods .foods-wrapper .food-list .food-item .icon{
  flex: 0 0 63px;
  background-position: center;
  background-size: 120% 100%;
  background-repeat: no-repeat;
  margin-right: 11px;
  height: 75px;
}

.goods .foods-wrapper .food-list .food-item .content{
  flex: 1;
}

.goods .foods-wrapper .food-list .food-item .content .name{
  font-size: 16px;
  line-height: 21px;
  color: #333333;
  margin-bottom: 10px;
  padding-right: 27px;
}

.goods .foods-wrapper .food-list .food-item .content .desc{
  font-size: 10px;
  color: #bfbfbf;
  margin-bottom: 8px;
  line-height: 19px;

  -webkit-line-clamp: 1;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.goods .foods-wrapper .food-list .food-item .content .extra{
  font-size: 10px;
  color: #bfbfbf;
  margin-bottom: 7px;
}

.goods .foods-wrapper .food-list .food-item .content .extra .saled{
  margin-right: 14px;
}

.goods .foods-wrapper .food-list .food-item .content .product{
  height: 15px;
  margin-bottom: 6px;
}

.goods .foods-wrapper .food-list .food-item .content .price{
  font-size: 0;
}

.goods .foods-wrapper .food-list .food-item .content .price .text{
  font-size: 14px;
  color: #fb4e44;
}

.goods .foods-wrapper .food-list .food-item .content .price .unit{
  font-size: 12px;
  color: #bfbfbf;
}
</style>
