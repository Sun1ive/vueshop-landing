<template>
  <v-app light>
    <v-toolbar fixed class="light-blue lighten-3">
      <v-spacer></v-spacer>
      <v-toolbar-items>
        <v-btn flat class="px-2">BUTTON</v-btn>
        <v-btn flat class="px-2">BUTTON</v-btn>
        <v-btn flat class="px-2">BUTTON</v-btn>
      </v-toolbar-items>
      <v-spacer></v-spacer>
      <v-toolbar-items>
        <v-btn flat @click.stop="openCart"><v-icon class="mr-2">shopping_cart</v-icon><div class="mr-2">{{ checkLength }} </div> Корзина</v-btn>
      </v-toolbar-items>
    </v-toolbar>
    <v-container fluid grid-list-xl class="mt-5">
      <v-layout row wrap class="items">
        <v-flex xs12 md4 v-for="item in collection" :key="item.id">
          <v-card>
            <v-card-media :src="item.thumb" height="500"></v-card-media>
            <v-card-title primary-title>
              <div>
                <h5>{{ item.title }}</h5>
                <p class="text-xs-center">Цена: {{ item.price }} грн</p>
              </div>
            </v-card-title>
            <v-card-actions>
              <v-btn flat class="addBtn white--text" @click.stop="addToCart(item)">Добавить в корзину</v-btn>
            </v-card-actions>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
    <v-container fluid>
      <v-layout>
        <v-dialog v-model="showCart" class="cart" width="300">
          <v-layout column wrap justify-center>
            <v-card v-for="(item,i) in cart" :key="i" class="text-xs-center">
              <v-card-text>
                <div class="list">
                  Товар: {{ item.title }} <br>
                  Цена: {{ item.price }}
                </div>
                <v-btn class="ml-0" @click.stop="clear">Удалить товар</v-btn>
              </v-card-text>
            </v-card>
            <v-card-text class="text-xs-center bgc">
              Сумма: {{ summ }}
            </v-card-text>
            <v-card-actions>
              <v-btn @click.stop="makeOrder"><v-icon left>done</v-icon>Заказать</v-btn><v-btn @click.stop="showCart = false"><v-icon left>clear</v-icon>Закрыть</v-btn>
            </v-card-actions>
          </v-layout>
        </v-dialog>
      </v-layout>
    </v-container>
    <v-container>
      <v-dialog v-model="isVisibleForm" id="dialog" width="450">
        <v-form class="py-2 px-5 form" @submit.prevent="submitOrder">
          <v-text-field required type="text" v-model="finalOrder.name" label="Имя" name="name"></v-text-field>
          <v-text-field required type="text" v-model="finalOrder.phone" label="Телефон" name="name"></v-text-field>
          <v-text-field required type="email" v-model="finalOrder.email" label="Email" name="name"></v-text-field>
          <v-btn class="ml-0" type="submit">Заказать</v-btn>
          <v-btn @click="isVisibleForm = false">Закрыть</v-btn>
        </v-form>
      </v-dialog>
    </v-container>
  </v-app>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'

  export default {
    data () {
      return {
        collection: [
          { title: 'Платье 01', thumb: '/static/one1.png', url: '/static/one.png', price: 1234, id: 'xsWQ1vc' },
          { title: 'Платье 02', thumb: '/static/two1.png', url: '/static/two.png', price: 1274, id: 'xsxWQ1vc' },
          { title: 'Платье 03', thumb: '/static/three1.png', url: '/static/three.png', price: 2234, id: 'xs5WQ1vc' }
        ],
        cart: [],
        showCart: false,
        isVisibleForm: false,
        finalOrder: {
          name: '',
          phone: null,
          email: '',
          items: []
        },
        check: false
      }
    },
    methods: {
      addToCart (item) {
        let addedItem = {
          title: '',
          price: null
        }
        addedItem.title = item.title
        addedItem.price = item.price
        this.cart.push(addedItem)
        console.log(this.cart);
        console.log(this.cart.length);
      },
      openCart () {
        if (this.cart.length === 0) {
          alert('Ваша корзина пуста!')
        } else {
          this.showCart = true
        }
      },
      clear () {
        if (this.cart.length === 1) {
          this.showCart = false
          this.cart.splice(0, 1) 
        } else {
          this.cart.splice(0, 1)
        }
      },
      makeOrder () {
        this.showCart = false
        this.isVisibleForm = true
      },
      submitOrder () {
        this.finalOrder.items = this.cart
        axios.post('https://myvuewebapp.firebaseio.com/order.json', this.finalOrder)
          .then(r => console.log(r))
          .catch(e => console.log(e))
        alert('Спасибо за заказ!')
        this.isVisibleForm = false
      }
    },
    computed: {
      checkLength () {
        return this.cart.length
      },
      summ () {
        let summ = 0
        _.each(this.cart, item => {
          summ += item.price
        })
        return summ
      }
    }
  }
</script>

<style scoped>
.items {
  max-width: 1280px;
  margin: 0 auto !important;
}
.addBtn {
  background-color: #000 !important;
}
.card__actions {
  justify-content: center;
}
.dialog .dialog--active{
  min-width: 400px;
}
.card__title {
  justify-content: center;
}
.bgc {
  background-color: #fff;
}
</style>
