<template>
  <div>
    <form class="flex flex-row flex-wrap text-left">
      <div class="w-full md:w-1/2 p-3">
        <label for="order">Order</label>
        <select id="order" v-model="order">
          <option value="DESC">Desc</option>
          <option value="ASC">Asc</option>
        </select>
      </div>
    </form>
    
    <div>
      <div v-if="favourites.length === 0">No Favourites yet, just click on one of the images in Vote or Search to 'Fav-it'</div>
      <div v-else class="images px-3">
        <div v-for="favourite in favourites" :key="favourite.id" class="image">
          <img :src="favourite.url" :alt="favourite.id" />
          <button @click="removeFromFavourites(favourite)">Remove from Favourites</button>
        </div>
      </div>
    </div>

    <form class="flex flex-row flex-wrap text-left">
      <div class="w-full md:w-1/2 p-3"></div>
      <div class="w-full md:w-1/2 p-3">
        <label for="perPage">Per Page:</label>
        <select id="perPage" v-model="perPage">
          <option value="9">9</option>
          <option value="18">18</option>
          <option value="80">80</option>
        </select>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      order: 'DESC',
      perPage: 9,
      favourites: JSON.parse(localStorage.getItem('favourites')) || [],
    }
  },
  methods: {
    removeFromFavourites(favourite) {
      const index = this.favourites.findIndex(image => image.id === favourite.id)
      if (index !== -1) {
        this.favourites.splice(index, 1)
        localStorage.setItem('favourites', JSON.stringify(this.favourites))
      }
    }
  },
}
</script>