<template>
   <!--Wishlist-->
  <div class="customerMain" style="flex-direction: column">

    <div class="customerFav_Top">
      <p class="customerFav-Title">Wish List</p>
      <p class="customerFav-subTitle">{{favProducts.length}} Items</p>
      <vue-element-loading :active="isDelay" color="#000000" background-color="#F0EEE9"/>
      <p v-if="!favProducts[0]" class="emptyCart">Oh, there is no item in wish list yet.</p>
    </div>
    <div class="customerFav-Main">
      <ul class="customerFav-Main-ul">
        <li class="customerFav-Main-li" 
        v-for="item in favProducts" 
        :key="item.id" >
          <div class="customerFav-Main-Left" :class="{'disabled': true,'deleteFavItem': deleteItem === item.id}">
            <img :src="item.imageUrl">
          </div>
          <div class="customerFav-Main-Right" :class="{'disabled': true,'deleteFavItem': deleteItem === item.id}">
            <div class="customerFav-Main-detail">
              <div class="customerFav-Main-category">{{item.category}}</div>
              <br>{{item.title}}
              <br>NT{{item.price|currency}}
              <div class="customerFav-Main-Btn">
                <div v-if="item.is_enabled" class="customerFav-Main-addtoCart" @click="addtoCart(item.id, 1)">
                  Add to cart
                  <i  v-if="isAddCart === item.id" class="fas fa-spinner fa-spin"></i>
                </div>
                <div v-else class="customerFav-Main-outOfStock">目前缺貨中</div>
              </div>
            </div>
            <img class="customerFav-Main-remove" :src="crossImg" @click="deleteFav(item)">
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import VueElementLoading from 'vue-element-loading';
export default {
  name: 'CustomerFavorite',
  components: {
    VueElementLoading
  },
  data(){
    return{
      crossImg: require("@/assets/img/other/cross_small_black.png"),
      addImg: require("@/assets/img/cart/add.png"),
      cutImg: require("@/assets/img/cart/sub.png"),
      favProducts: [],
      isAddCart: '',
      product: {},
      newFavProducts: [],
      isLoading: false,
      isDelay: false,
      isAdding: false,
      deleteItem: '',
    }
  },
  methods:{
    //刪除我的最愛項目
    deleteFav(item){
      const vm = this;
      let arrIndex = vm.favProducts.findIndex(function(value){
        return value.id === item.id;
      })
      vm.deleteItem = item.id;
      window.setTimeout(function(){
        vm.favProducts.splice(arrIndex,1);
        this.$bus.$emit('favToNav',vm.favProducts);
      }, 1000);
    },
    //加至購物車
    addtoCart(id, qty=1){
        const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart`;
        const vm = this;
        vm.isAddCart = id;
        const cart = {
	        product_id: id,
	        qty,
        };
        this.$http.post(api, { data: cart }).then((res) => {
            if(res.data.success){
              vm.isAddCart = '';
              this.$bus.$emit('update:cart');
            }
        });
    },
  },
  created(){
    const vm = this;
    this.isDelay = true;
    vm.$bus.$on('updateFavorite:fav', (item)=>{
      this.favProducts = item;
      this.isDelay = false;
    });
  }
}

</script>

