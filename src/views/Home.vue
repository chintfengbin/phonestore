<template>
  <div>
    <van-row>
      <van-col span="24">
        <van-tabs @click="onClick" sticky title-active-color="#E32DAB" color="#E32DAB" :line-width="100" :line-height="2">

          <van-tab v-for="index in categories.length" :title="categories[index-1].name" class="tab">

            <van-card v-for="(item,index) in phones"
                      :price="item.price"
                      :desc="item.desc"
                      :title="item.title"
                      :thumb="item.thumb"
            >
              <template #tags>
                <van-tag v-for="tag in item.tag" color="#f2826a" style="margin-left: 5px;">{{tag.name}}</van-tag>
              </template>
              <template #footer>
                <van-button round type="info" size="mini" @click="buy(index)">购买</van-button>
              </template>
            </van-card>

          </van-tab>
        </van-tabs>
      </van-col>
    </van-row>

    <van-sku
            v-model="show"
            :sku="sku"
            :goods="goods"
            :hide-stock="sku.hide_stock"
            @buy-clicked="onBuyClicked"
    >
      <template #sku-actions="props">
        <div class="van-sku-actions">

          <!-- 直接触发 sku 内部事件，通过内部事件执行 onBuyClicked 回调 -->
          <van-button
                  square
                  size="large"
                  type="danger"
                  @click="props.skuEventBus.$emit('sku:buy')"
          >
            买买买
          </van-button>
        </div>
      </template>
    </van-sku>

  </div>


</template>

<script>
  import {
    Toast,
    PullRefresh,
    Swipe,
    SwipeItem
  } from 'vant';
  export default {
    comments:{
      [PullRefresh.name]: PullRefresh,
      [Swipe.name]: Swipe,
      [SwipeItem.name]: SwipeItem
    },
    data() {
      return {
        categories: [
          {
            name:'魅焰红',
            type: 1
          },
          {
            name:'土豪金',
            type: 2
          },
          {
            name:'磨砂绿',
            type: 3
          },
          {
            name:'幻影黑',
            type: 4
          }
        ],
        phones: [
          {
            id: 1,
            title: "iphone8 plus",
            price: "5788",
            desc: "魅焰红",
            tag: [
              {
                name: "720p珍珠屏"
              },
              {
                name: "typec接口"
              }
            ],
            thumb:  "http://10.120.7.221:8080/train/znn/image20200909133535466.jpg"
          },

          {
            id: 2,
            title: "iphone7 plus",
            price: "5588",
            desc: "土豪金",
            tag: [
              {
                name: "720p珍珠屏"
              },
              {
                name: "typec接口"
              }
            ],
            thumb: "http://10.120.7.221:8080/train/znn/image20200909132839602.jpg"
          }
        ],
        show: true,
        sku: {
          tree: [
            {
              k: "规格",
              v: [
                {
                  id:1,
                  name:"32GB",
                  imgUrl: "http://10.120.7.221:8080/train/znn/image20200909133535466.jpg",
                  previewImgurl: "http://10.120.7.221:8080/train/znn/image20200909133535466.jpg"
                },
                {
                  id:2,
                  name:"64GB",
                  imgUrl: "http://10.120.7.221:8080/train/znn/image20200909133535466.jpg",
                  previewImgurl: "http://10.120.7.221:8080/train/znn/image20200909133535466.jpg"
                }
              ],
              k_s: "s1"
            }
          ],
          list: [
            {
              s1:1,
              price: 280000,
              stock_num: 5
            },
            {
              s1:2,
              price: 380000,
              stock_num: 10
            }
          ],
          price: "2800.00",
          stock_num: 15,
          nono_sku: false,
          hiden_stock: false
        },
        goods: {
          picture: "http://10.120.7.221:8080/train/znn/image20200909133535466.jpg"
        }
      }
    },
    created(){
      const _this = this
      axios.get('http://localhost:8181/phone/index').then(function (resp) {
        _this.phones = resp.data.data.phones
        _this.categories = resp.data.data.categories
      })
    },
    methods: {
      onClick(index) {
        // alert(index)
        const _this = this
        axios.get('http://localhost:8181/phone/findByCategoryType/'+this.categories[index].type).then(function (resp) {
          _this.phones = resp.data.data
        })
      },
      buy(index){
        this.show = true
        const _this = this
        axios.get('http://localhost:8181/phone/findSpecsByPhoneId/'+this.phones[index].id).then(function (resp) {
          _this.goods = resp.data.data.goods
          _this.sku = resp.data.data.sku
        })
      },
      onBuyClicked(item){
        this.$store.state.specsId = item.selectedSkuComb.s1
        this.$store.state.quantity = item.selectedNum
        this.$router.push('/addressList')
      }
    }
  }
</script>