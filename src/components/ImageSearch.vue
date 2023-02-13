<template>
  <div>
    <form class="flex flex-row flex-wrap text-left">
      <div class="w-full md:w-1/2 p-3">
        <label for="order">Order</label>
        <select id="order" v-model="order">
          <option value="RANDOM">Random</option>
          <option value="DESC">Descending</option>
          <option value="ASC">Ascending</option>
        </select>
      </div>

      <div class="w-full md:w-1/2 p-3">
        <label for="type">Type</label>
        <select id="type" v-model="type">
          <option value="jpg,png,gif">All</option>
          <option value="jpg,png">Static</option>
          <option value="gif">Animated</option>
        </select>
      </div>

      <div class="w-full md:w-1/2 p-3">
        <label for="category">Category</label>
        <select id="category" v-model="category">
          <option value="">None</option>
          <option v-for="category in categories" :key="category.id" :value="category.id">
            {{ category.name }}
          </option>
        </select>
      </div>

      <div class="w-full md:w-1/2 p-3">
        <label for="breed">Breed</label>
        <select id="breed" v-model="breed">
          <option value="">None</option>
          <option v-for="breed in breeds" :key="breed.id" :value="breed.id">
            {{ breed.name }}
          </option>
        </select>
      </div>
    </form>
    
    <div class="images px-3">
      <div v-for="(image, index) in imagesToDisplay.slice(0, perPage)" :key="index" class="image">
        <img :src="image.url" alt="Image">
        <button v-if="!isInFavourites(image)" @click="addToFavourites(image)">Add to Favourites</button>
        <button v-if="isInFavourites(image)" @click="removeFromFavourites(image)">Remove from Favourites</button>
      </div>
    </div>

    <p v-for="favourite in favourites" :key="favourite.id">{{ favourite.name }}</p>

    <form class="flex flex-row flex-wrap items-end text-left">
      <div class="w-full md:w-1/2 p-3">
        <label for="perPage">Per Page:</label>
        <select id="perPage" v-model="perPage">
          <option value="9">9</option>
          <option value="18">18</option>
          <option value="80">80</option>
        </select>
      </div>
      <div class="w-full md:w-1/2 p-3">
        <button class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-3 px-6 inline-flex items-center" @click="loadMore">
          <svg class="fill-current w-4 h-4 mr-2" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M13 8V2H7v6H2l8 8 8-8h-5zM0 18h20v2H0v-2z"/></svg>
          <span>More</span>
        </button>
      </div>
    </form>
  </div>
</template>

<script>
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("x-api-key", "live_IkIBAl3xNI5djAGRm2e2QtNVmWO8onHA538haEIY1SllogoUl5lR5onKlxoLYNMy");

var requestOptions = {
  method: 'GET',
  headers: myHeaders,
  redirect: 'follow'
};

export default {
  data() {
    return {
      categories: [],
      order: 'RANDOM',
      type: 'jpg,png,gif',
      category: '',
      breed: '',
      perPage: 9,
      currentPage: 1,
      images: [],
      imagesToDisplay: [],
      favourites: JSON.parse(localStorage.getItem('favourites')) || [],
    }
  },
  created () {
    this.fetchCategories()
    this.fetchBreeds()
  },
  computed: {
    hasMoreImages () {
      return this.imagesToDisplay.length < this.images.length
    }
  },
  watch: {
    order() {
      this.fetchImages();
    },
    type() {
      this.fetchImages();
    },
    selectedCategory() {
      this.fetchImages();
    },
    breed() {
      this.fetchImages();
    },
    perPage() {
      this.fetchImages();
    },
  },
  methods: {
    async fetchCategories() {
      const response = await fetch(`https://api.thedogapi.com/v1/categories`, requestOptions);
      const data = await response.json();
      this.categories = data;
    },
    async fetchBreeds() {
      const response = await fetch(`https://api.thedogapi.com/v1/breeds`, requestOptions);
      const data = await response.json();
      this.breeds = data;
    },
    async fetchImages() {
      const params = {
        order: this.order,
        mime_types: this.type,
        category_ids: this.category,
        breed_ids: this.breed,
        limit: this.perPage,
      };

      const response = await fetch(`https://api.thedogapi.com/v1/images/search?${new URLSearchParams(params)}`, requestOptions);
      const data = await response.json();
      this.images = data;
      this.updateImagesToDisplay()
    },
    updateImagesToDisplay () {
      const startIndex = (this.currentPage - 1) * this.perPage
      const endIndex = startIndex + this.perPage
      this.imagesToDisplay = this.images.slice(startIndex, endIndex)
    },
    loadMore () {
      this.currentPage++
      this.updateImagesToDisplay()
    },
    addToFavourites (image) {
      this.favourites.push(image)
      localStorage.setItem('favourites', JSON.stringify(this.favourites))
    },
    removeFromFavourites(favourite) {
      const index = this.favourites.findIndex(image => image.id === favourite.id)
      if (index !== -1) {
        this.favourites.splice(index, 1)
        localStorage.setItem('favourites', JSON.stringify(this.favourites))
      }
    },
    isInFavourites (image) {
      return this.favourites.some(i => i.id === image.id)
    }
  },
  mounted() {
    this.fetchImages();
  }
}
</script>