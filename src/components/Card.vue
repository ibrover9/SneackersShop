<script setup>
import { inject } from 'vue'

const props = defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function
})
</script>

<template>
  <div
    class="relative bg-white border border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl"
  >
    <img
      :src="!isFavorite ? '/like-1.svg' : '/like-2.svg'"
      alt="Plus"
      class="absolute top-8 left-8"
      @click="onClickFavorite"
    />
    <img :src="imageUrl" alt="Sneaker" />
    <p class="mt-2">{{ title }}</p>

    <div class="flex justify-between">
      <div class="flex flex-col">
        <span class="text-slate-400">Цена:</span>
        <b>{{ price }} руб.</b>
      </div>
      <img :src="!isAdded ? '/plus.svg' : 'checked.svg'" alt="Plus" @click="onClickAdd" />
    </div>
  </div>
</template>
