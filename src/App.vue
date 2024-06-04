<script setup>
import { provide, ref, watch, computed } from 'vue'
import axios from 'axios'
import HeaderMain from './components/HeaderMain.vue'

import Drawer from './components/Drawer.vue'

/*Корзина (START)*/
const cart = ref([])
const isCreatingOrder = ref(false)

const drawerOpen = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const vatPrice = computed(() => Math.round(totalPrice.value * 5) / 100)

const cartIsEmpty = computed(() => cart.value.length === 0)

const cartButtonDisabled = computed(() => isCreatingOrder.value || cartIsEmpty.value)

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeToCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

const createOrder = async () => {
  try {
    isCreatingOrder.value = true
    const { data } = await axios.post('https://983795d1edc6edf5.mokky.dev/orders', {
      items: cart.value,
      totalPrice: totalPrice.value
    })

    cart.value = []

    return data
  } catch (error) {
    console.log(error)
  } finally {
    isCreatingOrder.value = false
  }
}

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true }
)

provide('cart', { cart, closeDrawer, openDrawer, addToCart, removeToCart })

/* Корзина (END) */
</script>

<template>
  <div>
    <Drawer
      v-if="drawerOpen"
      :total-price="totalPrice"
      :vat-price="vatPrice"
      @create-order="createOrder"
      :button-disabled="cartButtonDisabled"
    />
    <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14 mb-14">
      <HeaderMain :total-price="totalPrice" @open-drawer="openDrawer" />
      <div class="p-10"><router-view></router-view></div>
    </div>
  </div>
</template>

<style scoped>
/* Add your component-specific styles here */
</style>
