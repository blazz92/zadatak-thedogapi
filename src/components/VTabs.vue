<template>
  <div class="flex justify-center bg-black">
    <nav class="flex" aria-label="Tabs">
    <a v-for="(tab, index) in tabs" :key="index" :class="{'border-white text-white': activeTab === index, 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300': activeTab !== index}" class="whitespace-nowrap py-4 px-3 border-b-2 font-medium text-sm cursor-pointer" @click="activeTab = index">
        {{ tab }}
    </a>
    </nav>
  </div>
  <component class="max-w-screen-md m-auto" :is="currentTabComponent"></component>
</template>

<script>
import ImageFavourites from './ImageFavourites.vue'
import ImageSearch from './ImageSearch.vue'

export default {
data() {
  return {
    tabs: ['Image/Search', 'Favourites'],
    activeTab: 0,
  }
},
computed: {
  currentTabComponent() {
    if (this.activeTab === 0) {
      return ImageSearch
    } else if (this.activeTab === 1) {
      return ImageFavourites
    } else {
      return null
    }
  }
}
}
</script>

<style>
form {
  margin: 30px 0;
}

label {
  font-size: 12px !important;
  line-height: 14px !important;
}

select {
  width: 100%;
  padding: 0.5rem 2.5rem 0.5rem 0 !important;
  border: 0;
  border-bottom: 1px solid #ccc;
  font-size: 16px !important;
}

.images {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  width: 100%;
  font-size: 0;
  gap: 6px;
}

.images .image {
  position: relative;
  flex: calc(33.33% - 6px);
}

@media screen and (max-width:767px) {
.images .image {
  flex: 100%;
}
}

.image img {
    width: 100%;
    aspect-ratio: 1/1;
    object-fit: cover;
}

.image button {
  position: absolute;
  right: 10px;
  bottom: 10px;
  left: 10px;
  background: #fff;
  border: none;
  font-size: 15px;
  line-height: 40px;
  opacity: 0;
}

.image:hover button {
  opacity: 1;
}
</style>
