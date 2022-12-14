<template>
  <div class="home">
    
    <form class="input-container" @submit.prevent="search(1)">
      <input type="text" placeholder="Enter movie title..." v-model="searchKeyword">
      <button class="btn btn-primary">
        Search
      </button>
    </form>

    <div class="error">
      {{error}}
    </div>
    
    <LoadingState v-if="loading"></LoadingState>

    <div class="illustration" v-if="movieList.length === 0 && !loading">
      <img src="@/assets/illustation.png" alt="">
    </div>
    
    <div class="container">
      <div class="movie-list" v-if="movieList !== 0 && !loading">
        <div class="movie" v-for="movie in movieList" :key="movie.imdbID" @click="goToMovie(movie.imdbID)">
          <div class="image">
            <img :src="movie.Poster" alt="">
          </div>
          <div class="title">{{movie.Title}}</div>
          <div class="additional">
            <div class="type">{{movie.Type}}</div>
            <div class="mid"> &middot;</div>
            <div class="year">{{movie.Year}}</div>
          </div>
        </div>
      </div>

      <div class="pagination">
        <div :class="['page', { 'active': page === currentPage }]" v-for="page in totalPages" :key="page"
        @click="currentPage = page">
          {{page}}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import { mapState, mapActions } from 'vuex';
import LoadingState from '@/components/LoadingState.vue';


export default {
    name: "HomeView",
    data: () => ({
        searchKeyword: "",
        currentPage: 1,
    }),
    components: { LoadingState },
    computed: {
        ...mapState(["movieList", "loading", "error", "totalResults"]),
        totalPages() {
          return Math.ceil(this.totalResults / 10)
        }
    },
    methods: {
        ...mapActions(["fetchAllMovies", "clearList"]),
        search(page) {
          if (page) {
            this .currentPage = page;
          }
          this.$router.replace({ query: { s: this.searchKeyword}})
          const options = {
            searchPhrase: this.searchKeyword,
            page: this.currentPage
          }
          this.fetchAllMovies(options);
        },
        goToMovie(movieId) {
            this.$router.push(`/${movieId}`);
        }
    },
    mounted() {
        this.clearList();
        this.searchKeyword = this.$route.query.s
        if (this.searchKeyword) {
          this.search();
        }
    },

    watch: {
      currentPage() {
        this.search();
      }
    }
}
</script>

<style scoped>
  .error {
    text-align: center;
    margin-block: 1rem;
    color: #C53939;
    font-size: 24px;
  }
  
  .input-container{
    width: calc(100vw - 64px);
    max-width: 507px;
    height: 50px;
    margin-top: 72px;
    margin-inline: auto;
    padding: 6px;
    border-radius: 100px;
    display: flex;
    background: #313030;
  }

  .input-container:focus-within{
    box-shadow: 0 0 0 2px #C53939;
  }

  .input-container input {
    flex-basis: 100%;
    background: transparent;
    padding-inline: 20px 16px;
    border: none;
    color: white;
    font-size: 16px;
  }

  .input-container input:focus 
  .input-container input:focus-within, 
  .input-container input:focus-visible {
    outline: none;
    border: none;
  }

  .btn {
    padding: 12px 24px;
    font-size: 16px;
    border: none;
    border-radius: 100px;
    cursor: pointer;
  }

  .btn:hover{
    opacity: 0.7;
  }

  .btn-primary{
    background: #C53939;
    color: white;
  }

  .illustration{
    margin-top: 144px;
    width: 100%;
  }

  .illustration img {
    display: block;
    width: 90%;
    max-width: 715px;
    margin-inline: auto;
  }

  .movie-list{
    max-width: 1200px;
    margin-inline: auto;
    margin-top: 40px;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, auto));
    gap: 32px;
  }
  
  @media (min-width: 992px) {
    .movie-list{
      grid-template-columns: repeat(5, 1fr);
    }

  }

  .movie{
    width: 100%;
    transition: transform 300ms ease;
    cursor: pointer;
  }

  .movie:hover {
    transform: scale(1.05);
    
  }
  
  .movie .additional{
    display: flex;
    gap: 8px;
    opacity: 0.55;
  }

  .movie .type{
    text-transform: capitalize;
  }

  .movie .additional .mid{
    font-size: 40px;
    line-height: 20px;
  }

  .movie .title{
    font-weight: 700;
  }
  
  .movie .image img {
    width: 100%;
    height: 310px;
    object-fit: cover;
  }
  .pagination {
    display: flex;
    gap: 4px;
    width: 100%;
    overflow: auto;
    margin-block: 32px;
  }
  .page.active{
  background: #C53939;
  }
  .page{
    min-width: 40px;
    width: 40px;
    height: 40px;
    display: grid;
    place-content: center;
    background: rgba(255,255,255,0.1);
    cursor: pointer;
  }
  .page:not(.active):hover{
    background: rgba(255,255,255,0.2)
  }
</style>
