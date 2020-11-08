<template>
  <div>
      <div v-if="show" class=" cart-div">
        <div class="d-flex p-4">
          <div class="close" @click="show = false">x</div>
          <h4>Your Cart</h4>
        </div>
        <div class="container">
          <ApolloQuery :query="require('../graphql/product.gql')" :variables="{ currency }">
          <template v-slot="{ result: {  data } }">
            <!-- Result -->
            <div v-if="data" class="result apollo">
              <select @change="selectCurr" v-model="selected">
                <option v-for="item in data.currency" :key="item.id">{{item}}</option>
              </select>
            </div>
          </template>
        </ApolloQuery>
          
          <div class="card mt-4">
            <div v-for="items in cartItems.carts" :key="items.product.id" >
              <div class="p-4 d-flex">
                <p>{{items.product.title}}</p>
                <img :src="items.product.image_url" width="50" alt="">
                <div class="close" @click="deleteCart(items)">x</div>
              </div>
              <div class="d-flex p-4">
                <div class="number">
                  <span class="minus" @click="dec(items)">-</span>
                  <span class="pl-2 pr-2">{{items.quantity}}</span>
                  <span class="plus" @click="inc(items)">+</span>
                </div>
                <p>${{items.product.price * items.quantity}}</p>
              </div>
            </div>
            <div v-if="cartItems.carts.length == 0" class="text-center p-4">
              <h4>Your Cart is Empty</h4>
            </div>
          </div>
          <div class="mt-5">
            <hr>
            <div class="d-flex p-4">
              <h5>Subtotal</h5>
              <p>${{subTotal}}</p>
            </div>
            <div class="container pb-4">
              <button type="button" class="btn btn-block add">PROCEED TO CHECKOUT</button>
            </div>
          </div>
        </div>
      </div>
    <div class="container-fluid mt-5 d-flex">
      <div>
        <h2>All products</h2>
        <p>360° look at Lumin</p>
      </div>
      <div>
        <button @click="show = true" type="button" class="btn add">Carts</button>
      </div>
    </div>
      <div class="wrapper">
        <div class="container text-center">
          <ApolloQuery :query="require('../graphql/product.gql')" :variables="{ currency }">
            <template v-slot="{result: { data }}">
              <!-- Result -->
              <div v-if="data" class="result apollo">
                  <div class="row">
                    <div class="col-lg-4 col-md-6 p-5" v-for="product in data.products" :key="product.id">
                        <img :src="product.image_url" width="100" height="100" :alt="product.image_url">
                        <p class="mt-5">{{product.title}}</p>
                        <p>${{product.price}}</p>
                        <button type="button" class="btn add" @click="addToCart(product)">Add to Cart</button>
                    </div>
                  </div>
              </div>
              <!-- No result -->
              <div v-else class="p-5 no-result apollo">No result yet</div>
            </template>
          </ApolloQuery>
        </div>
        <div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Products',
  data:()=>({
    show: false,
    cartItems:{
      carts:[]
    },
    selected: 'USD',
    currency: ''
  }),
  computed : {
      subTotal () {
        let total = 0;
        this.cartItems.carts.forEach((item)=> {
          total += item.quantity * item.product.price
        })
        return total;
      }
    },
  methods: {
    addToCart(products) {
      this.show = true
      var cartItem = this.getCartItem(products);
        if (cartItem != null) {
          cartItem.quantity++;
        } else {
          this.cartItems.carts.push({
            product: products,
            quantity: 1
          });
        }
    },
    inc(qty) {
      qty.quantity++
    },
    dec(qty) {
      qty.quantity--
      if (qty.quantity == 0) {
        this.deleteCart(qty);
      }
    },
    getCartItem: function(product) {
      for (var i = 0; i < this.cartItems.carts.length; i++) {
        if (this.cartItems.carts[i].product.id === product.id) {
          return this.cartItems.carts[i];
        }
      }
      return null;
    },
    deleteCart (cart) {
        this.cartItems.carts.splice(this.cartItems.carts.indexOf(cart),  1);
    },
    selectCurr(product) {
      product.price = this.currency
    },
    
  },
  
}
</script>

<style scoped>
.wrapper {
  background: #E2E6E3;
  /* height: 100%; */
}
.add {
  border-radius: 0px !important;
  background: #525D4F;
  color: #fff;
  padding: 7px 20px;
}
.d-flex {
  display: flex;
  justify-content: space-between;
}
.cart-div {
  position: fixed;
  overflow-x: hidden;
  right: 0;
  top: 0;
  z-index: 1;
  background: #F2F3F0;
  height: 100%;
  width: 500px;
  margin-bottom: 10px;
}
.close, span {
  cursor: pointer;
}
.minus, .plus{
  width:20px;
  background:#f2f2f2;
  border-radius:4px;
  border:1px solid #ddd;
  display: inline-block;
  vertical-align: middle;
  text-align: center;
}
input{
  height:20px;
  width: 50px;
  text-align: center;
  font-size: 16px;
  border:1px solid #ddd;
  border-radius:4px;
  display: inline-block;
  vertical-align: middle;
}
</style>
