<script setup>
import axios from 'axios'
import { inject } from 'vue'
import CardList from '../components/CardList.vue'
import { reactive, watch, ref, onMounted } from 'vue'

const { cart, addToCart, removeToCart } = inject('cart')

const items = ref([])

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeToCart(item)
  }
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
}

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id
      }
      const { data } = await axios.post(`https://983795d1edc6edf5.mokky.dev/favorites`, obj)
      item.isFavorite = true
      item.favoriteId = data.id
    } else {
      await axios.delete(`https://983795d1edc6edf5.mokky.dev/favorites/${item.favoriteId}`)
      item.isFavorite = false
      item.favoriteId = null
    }
  } catch (error) {
    console(error)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://983795d1edc6edf5.mokky.dev/favorites?')
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id)
      if (!favorite) {
        return item
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
  } catch (error) {
    console.error(error)
  }
}

const fetchItems = async () => {
  try {
    const params = { sortBy: filters.sortBy }
    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
    const { data } = await axios.get('https://983795d1edc6edf5.mokky.dev/items?', {
      params
    })

    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (error) {
    console.error(error)
  } // Опци
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []
  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((CartItem) => CartItem.id === item.id)
  }))
})

watch(filters, fetchItems)

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})
</script>

<template>
  <div>
    <div class="flex justify-between items-center">
      <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
      <div class="flex gap-4">
        <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
          <option value="name">По названию</option>
          <option value="price">По цене (дешевые)</option>
          <option value="-price">По цене (дорогие)</option>
        </select>
        <div class="relative">
          <img class="absolute left-4 top-3" src="/search.svg" />
          <input
            @input="onChangeSearchInput"
            class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
            placeholder="Поиск..."
          />
        </div>
      </div>
    </div>
    <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
  </div>
</template>
