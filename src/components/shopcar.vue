<template>
  <div class="shopcar">
    <div class="header-title">
      <h3>购物车</h3>
      <div class="car-right">
        <img src="http://ohpveu9d8.bkt.clouddn.com/msg.png" alt="note">
        <div class="edit-status" @click="edit_mode">{{editStatus}}</div>
      </div>
    </div>
    <div class="car-list">
      <ul v-show="good_list.length !== 0">
        <li class="car-item" v-for="(item, index) in good_list">
          <div class="car-item-header">
            <div class="input-block">
              <label class="input-label"
                   :class="{active: item.is_selected}"
                   :for="'car-checkbox-'+index"
                   @click="select_one(index)">
              </label>
            </div>
            <div>{{item.seller}}></div>
          </div>
          <div class="car-item-content">
            <div class="input-block">
              <label class="input-label"
                   :class="{active: item.is_selected}"
                   :for="'car-checkbox-'+index"
                   @click="select_one(index)">
              </label>
            </div>

            <div class="car-img">
              <img :src="item.img" />
            </div>

            <div class="car-cont">
              <h3>{{item.title}}</h3>
              <div class="cat-desc" :class="{'cat-desc-edit': descEditActive}">
                <span v-for="spec in item.spec_item">{{spec}}</span>
              </div>

              <div class="car-price">
                ￥<span class="price">{{item.price}}</span>
              </div>

            </div>
            <div class="car-num">
              <span @click="reduce(index)">-</span>
              <span>{{item.num}}</span>
              <span @click="add(index)">+</span>
            </div>

            <div class="car-more" v-if="editMode === 0">
              <span>再逛逛></span>
            </div>
          </div>
        </li>
      </ul>
      <div class="no-data" v-show="good_list.length === 0">购物车空空如也~</div>
    </div>
    <div class="car-footer">
      <div class="foot-car">
        <label for="foot-check" class="input-label" :class="{active: selected_all}" @click="slect_all"></label>
        全选
      </div>
      <div class="total-cont" v-if="editMode === 0">
        <div>
          <div class="total-price">合计：￥{{totalPrice - 20 <= 20 ? totalPrice : totalPrice - 20}}</div>
          <div class="detail-price">
            <span>金额:￥{{totalPrice}}</span>
            <span>立减:￥20</span>
          </div>
        </div>
        <div class="btn-box">
          <button :style="{ background: availableColor }">立即下单</button>
        </div>
      </div>
      <div class="edit" v-if="editMode === 1">
        <button class="edit-delete" @click="delete_selected">删除</button>
        <button class="edit-collection">移入收藏</button>
      </div>
    </div>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        good_list: [
          {
            seller: '大胖子汽车服务联盟',
            title: '轮胎(zoux牌) 这个轮胎厉害喽 防滑 耐用 五年质保',
            img: 'http://ohpveu9d8.bkt.clouddn.com/tyre.jpg',
            num: 1,
            price: 499.00,
            spec_item: [
              '型号：91WF15',
              '全国免费安装'
            ],
            is_selected: false
          },
          {
            seller: '小胖子汽车服务联盟',
            title: '轮胎(zoux牌) 这个轮胎厉害喽 防滑 耐用 五年质保 全国免费安装 满599减100',
            img: 'http://ohpveu9d8.bkt.clouddn.com/tyre.jpg',
            num: 1,
            price: 259.00,
            spec_item: [
              '型号：91W455',
              '满599减100'
            ],
            is_selected: false
          },
          {
            seller: '邹的汽车服务联盟',
            title: '轮胎(zoux牌) 这个轮胎厉害喽 防滑 耐用 ',
            img: 'http://ohpveu9d8.bkt.clouddn.com/tyre.jpg',
            num: 2,
            price: 399.00,
            spec_item: [
              '型号：91WF15',
              '满599减100',
              '全国免费安装',
              '五年质保'
            ],
            is_selected: false
          },
          {
            seller: '圣诞老人汽车服务联盟',
            title: '轮胎(zoux牌) 这个轮胎厉害喽 防滑 耐用 五年质保 全国免费安装 满599减100',
            img: 'http://ohpveu9d8.bkt.clouddn.com/tyre.jpg',
            num: 1,
            price: 429.00,
            spec_item: [
              '型号：91WF15',
              '编不下去了了了了了了了'
            ],
            is_selected: false
          },
          {
            seller: '塘朗山汽车服务联盟',
            title: '轮胎(zoux牌) 这个轮胎厉害喽 防滑 耐用 五年质保 全国免费安装 满599减100',
            img: 'http://ohpveu9d8.bkt.clouddn.com/tyre.jpg',
            num: 1,
            price: 439.00,
            spec_item: [
              '型号：91WF15',
              '满599减100'
            ],
            is_selected: false
          },
          {
            seller: '黄焖鸡汽车服务联盟',
            title: '轮胎(zoux牌) 这个轮胎厉害喽 防滑 耐用 五年质保',
            img: 'http://ohpveu9d8.bkt.clouddn.com/tyre.jpg',
            num: 3,
            price: 259.00,
            spec_item: [
              '全国免费安装',
              '满599减100'
            ],
            is_selected: false
          }
        ],
        totalPrice: 0,
        selected_all: false,
        editMode: 0,
        editStatus: '编辑',
        descEditActive: false,
        availableColor: '#ccc'
      }
    },
    mounted: function () {
      this.getTotal()
    },
    watch: {
      // 侦听商品选中数据的变化
      'good_list': {
        handler: function (val, oldVal) {
          // console.log('val', val)
          return val
        },
        deep: true
      },
      // 监听商品总价，若为零下单按钮不可点
      totalPrice (val, oldVal) {
        if (oldVal === 0 && val !== 0) {
          this.availableColor = '#febc6c'
        } else if (val === 0) {
          this.availableColor = '#ccc'
        }
      }
    },
    methods: {
      // 切换编辑模式
      edit_mode () {
        if (this.editMode === 0) {
          this.editMode = 1
          this.editStatus = '完成'
          this.descEditActive = true
        } else {
          this.editMode = 0
          this.editStatus = '编辑'
          this.descEditActive = false
        }
      },
      // 计算选中商品金额
      getTotal () {
        this.totalPrice = 0
        for (let i = 0; i < this.good_list.length; i++) {
          let _d = this.good_list[i]
          if (_d.is_selected) {
            this.totalPrice += _d['price'] * _d['num']
          }
        }
      },
      // 删除选中商品
      delete_selected () {
        let arr = []
        for (let i = 0; i < this.good_list.length; i++) {
          let _d = this.good_list[i]
          if (!_d.is_selected) {
            arr.push(this.good_list[i])
          }
        }
        console.log(arr)
        this.good_list = arr
        this.totalPrice = 0
      },
      // 选中其中一件商品
      select_one (index) {
        if (this.good_list[index].is_selected === true) {
          this.good_list[index].is_selected = false
        } else {
          this.good_list[index].is_selected = true
        }
        this.getTotal()
      },
      // 选中全部
      slect_all () {
        if (this.selected_all) {
          for (let i = 0; i < this.good_list.length; i++) {
            this.good_list[i].is_selected = false
          }
          this.selected_all = false
        } else {
          for (let i = 0; i < this.good_list.length; i++) {
            this.good_list[i].is_selected = true
          }
          this.selected_all = true
        }
        this.getTotal()
      },
      // 减小商品购买数量
      reduce (index) {
        if (this.good_list[index].num <= 1) return
        this.good_list[index].num --
        this.getTotal()
      },
      // 增加商品购买数量
      add (index) {
        this.good_list[index].num ++
        this.getTotal()
      }
    }
  }
</script>

<style>
  .input-label{
    position: absolute;
    width: .33333rem;
    height: .33333rem;
    top: 50%;
    margin-top: -.16666rem;
    left: .26666rem;
    background: #fff;
    border: 1px solid #ccc;
    border-radius: 50%;
    cursor: pointer;
  }
  .input-label:after{
    position: absolute;
    width: .33333rem;
    height: .33333rem;
    top: 50%;
    margin-top: -.16666rem;
    left: .26666rem;
    opacity: 0;
    content: '';
    background: transparent;
    border-top: none;
    border-right: none;
    -webkit-transform: rotate(-45deg);
    -moz-transform: rotate(-45deg);
    -o-transform: rotate(-45deg);
    -ms-transform: rotate(-45deg);
    transform: rotate(-45deg);
  }
  .input-label.active{
    border: none;
    width: .34666rem;
    height: .34666rem;
    background: url(http://ohpveu9d8.bkt.clouddn.com/select.png) no-repeat center/100%;
  }
  .car-checkbox{
    display: none;
  }
</style>

<style scoped lang="less">
  .header-title{
    position: fixed;
    top: 0;
    left: 0;
    z-index: 98;
    width: 100%;
    height: 1.17333rem;
    line-height: 1.17333rem;
    background: #fff;
    border-bottom: 1px solid #eee;
    h3{
      font-weight: normal;
      text-align: center;
      font-size: 0.5rem;
    }
    .car-right{
      position: absolute;
      right: .26666rem;
      top: 50%;
      height: .4rem;
      width: 2rem;
      margin-top: -0.2rem;
      z-index: 99;
      display: flex;
      flex-direction: row-reverse;
      align-items: center;
      .edit-status{
        font-size: .4rem;
        color: #aaa;
        margin-right:.26666rem;
        cursor: pointer;
      }
      img{
        height: .5rem;
        width: .5rem;
      }
    }
  }
  .car-list{
    background: #f8f8f8;
    margin-top: 1.17333rem;
    margin-bottom: 1.33333rem;
    .car-item{
      background: #fff;
      border-bottom: 1px solid #eee;
      border-top: 1px solid #eee;
      margin-top: .24rem;
      .car-item-header{
        height: 1.17333rem;
        line-height: 1.17333rem;
        background: #fbfbfb;
        font-size: .33333rem;
        padding-left: .90666rem;
        display: flex;
        position: relative;
      }
      .car-item-content{
        display: flex;
        height: 1.8666rem;
        padding: .4rem .2666rem;
        position: relative;
        .car-img{
          height: 1.86666rem;
          width: 1.86666rem;
          margin-left: .54rem;
          border: 1px solid #ccc;
          border-radius: 20%;
          overflow: hidden;
          img{
            width: 100%;
          }
        }
        .car-cont{
          height: 1.86666rem;
          margin-left: .37rem;
          position: relative;
          h3{
            font-weight: normal;
            line-height: .4rem;
            font-size: .29333rem;
            width: 4.4rem;
            background: #fdfdfd;
            display: -webkit-box;
            -webkit-box-orient: vertical;
            -webkit-line-clamp: 2;
            overflow: hidden;
          }
          .cat-desc{
            font-size: .24rem;
            height: .37333rem;
            line-height: .37333rem;
            width: 4.6733rem;
            padding-right: .4rem;
            overflow: hidden;
            text-overflow:ellipsis;
            white-space: nowrap;
          }
          .cat-desc-edit{
            background: #f8f8f8 url(http://ohpveu9d8.bkt.clouddn.com/arrow-right.png) no-repeat right/.32rem 50%;
          }
          .car-price{
            position: absolute;
            bottom: 0;
            left: 0;
            height: .53333rem;
            line-height: .53333rem;
            font-size: .21333rem;
            color: #f98d30;
            .price{
              font-size: .34rem;
            }
          }
        }
        .car-more{
          flex-shrink: 1;
          flex-grow: 1;
          text-align: right;
          span{
            color: #febc6c;
            font-size: .29333rem;
          }
        }
        .car-num{
          position: absolute;
          bottom: .4rem;
          right: .26666rem;
          span{
            display: inline-block;
            height: .53333rem;
            line-height: .53333rem;
            float: left;
            text-align: center;
            border: 1px solid #f9f9f9;
            font-size: .24rem;
          }
          span:nth-child(1){
            width: .613333rem;
            border-radius: 0 0 20% 20%;
          }
          span:nth-child(2){
            width: .93333rem;
            border-left: none;
            border-right: none;
          }
          span:nth-child(3){
            width: .613333rem;
            border-radius: 20% 20% 0 0;
          }
        }
      }
    }
    .no-data{
      text-align: center;
      line-height: .8rem;
      color: #ccc;
      background: #fff;
    }
  }
  .car-footer{
    height: 1.33333rem;
    background: #fff;
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    z-index: 99;
    overflow: hidden;
    border-top: 1px solid #999;
    box-shadow: 0px -6px 12px -6px #ddd inset;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .foot-car{
      width: .90666rem;
      height: 1.33333rem;
      line-height: 1.33333rem;
      padding-left: .93333rem;
      font-size: .29333rem;
      position: relative;
    }
    .total-cont{
      height: 1.33333rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      flex-grow: 1;
      flex-shrink: 1;
      // background: red;
      .total-price{
        font-size: .4rem;
        line-height: .5rem;
      }
      .detail-price{
        font-size: .24rem;
        line-height: .3rem;
      }
      .btn-box{
        button{
          height: 1.33333rem;
          width: 2.8rem;
          // background: #febc6c;
          border: none;
          color: #fff;
          font-size: .37333rem;
        }   
      }
    }
    .edit{
      flex-grow: 1;
      flex-shrink: 1;
      display: flex;
      align-items: center;
      flex-direction: row-reverse;
      height: 1.33333rem;
      button{
        height: .72rem;
        width: 1.89333rem;
        border-radius: 4px;
        background: none;
        margin-right: .266666rem;
      }
      .edit-collection{
        border: 1px solid #999;
        color: #999;
      }
      .edit-delete{
        border: 1px solid #febc6c;
        color: #febc6c;
      }
    }
  }
</style>