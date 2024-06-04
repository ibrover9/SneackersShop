<script setup>
import Card from './Card.vue'

defineProps({
  items: Array
})

const emit = defineEmits(['addToFavorite', 'addToCart'])

// const onClickFavorite = () => {
//   alert(111)
// }
</script>

<template>
  <div class="grid grid-cols-4 gap-5" v-auto-animate>
    <Card
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :image-url="item.imageUrl"
      :title="item.title"
      :price="item.price"
      :is-favorite="item.isFavorite"
      :onClickFavorite="() => emit('addToFavorite', item)"
      :onClickAdd="() => emit('addToCart', item)"
      :is-added="item.isAdded"
    />
  </div>
</template>
