<script>
  import axios from "axios";
  import './assets/rick_and_morty.css'
  import './assets/main.css'

  export default {
    data() {
      return {
        data_: {},
        info_: {},
        page: 1,
        filterName: "no",
        filterStatus: "no",
        filterGender: "no",
      }
    },
    mounted() {
       axios.get('https://rickandmortyapi.com/api/character')
             .then(res => {
               this.data_ = res.data;
               this.info_ = res.data.info;
             })
    },
    methods: {
      getNewPage (new_url){
        const parsedUrl = new URL(new_url)
        this.page = parsedUrl.searchParams.get("page");
        axios.get(new_url)
             .then(res => {
               this.data_ = res.data;
               this.info_ = res.data.info;
             })
      },
      getNewPageByFilters() {
        const fName = this.filterName === "no" ? "" : `name=${this.filterName}&`;
        const fStatus = this.filterStatus === "no" ? "" : `status=${this.filterStatus}&`;
        const fGender = this.filterGender === "no" ? "" : `gender=${this.filterGender}&`;
        let urlNow = `https://rickandmortyapi.com/api/character/?${fName}${fStatus}${fGender}`;
        this.page = 1;

        axios.get(urlNow.slice(0, -1))
             .then(res => {
               this.data_ = res.data;
               this.info_ = res.data.info;
             })
      }
    }
  }
</script>

<template>
  <header class="header">
    <nav>
      <div class="block left"><a href="#"><img src="@/assets/icon.png" alt="Image"></a></div>
      <span class="block">Docs</span>
      <span class="block">About</span>
      <div class="block support"><span>SUPPORT US</span></div>
    </nav>
    <div class="big_description"><span class="main_name">The Rick and Morty API</span></div>
  </header>
  <main>
    <div class="filter">
      <div class="select_container">
        <span class="title_name_select">Name</span>
        <select id="filter_by_name" v-model="filterName">
          <option value="no">---</option>
          <option value="rick">Rick</option>
          <option value="morty">Morty</option>
          <option value="beth">Beth</option>
          <option value="summer">Summer</option>
          <option value="jerry">Jerry</option>
        </select>
      </div>
      <div class="select_container">
        <span class="title_name_select">Status</span>
        <select id="filter_by_status" v-model="filterStatus">
          <option value="no">---</option>
          <option value="alive">Alive</option>
          <option value="dead">Dead</option>
          <option value="unknown">Unknown</option>
        </select>
      </div>
      <div class="select_container">
        <span class="title_name_select">Gender</span>
        <select id="filter_by_gender" v-model="filterGender">
          <option value="no">---</option>
          <option value="female">Female</option>
          <option value="male">Male</option>
          <option value="genderless">Genderless</option>
          <option value="unknown">Unknown</option>
        </select>
      </div>
      <button @click="getNewPageByFilters()">Accept filters</button>
    </div>
    <div class="card_container">
      <div v-for="item in data_.results" :key="item.id" class="card">
        <div class="image_div"><img v-bind:src="item.image" v-bind:alt="item.name"></div>
        <div class="info_div">
          <div class="block_info top">
            <span class="full_name">{{ item.name }}</span>
            <span class="short_info"><span :class="{
              'point red': item.status.toLowerCase() === 'dead',
              'point green': item.status.toLowerCase() === 'alive',
              'point grey': item.status.toLowerCase() === 'unknown',
            }"></span> {{ item.status }} - {{ item.species }}</span>
          </div>
          <div class="block_info middle">
            <span class="layout_text">Last known location:</span>
            <a v-bind:href="item.origin.url" class="url_text">{{ item.origin.name }}</a>
          </div>
          <div class="block_info bottom">
            <span class="layout_text">First seen in:</span>
            <a v-bind:href="item.location.url" class="url_text">{{ item.location.name }}</a>
          </div>
        </div>
      </div>
    </div>
  </main>
  <footer>
    <button v-bind:disabled="!info_.prev" :class="{'disabled': !info_.prev}" @click="getNewPage(info_.prev)">PREV PAGE</button>
    <span>CHARACTERS: {{ info_.count }}</span>
    <span>PAGES: {{ page }} / {{ info_.pages }}</span>
    <button v-bind:disabled="!info_.next" :class="{'disabled': !info_.next}" @click="getNewPage(info_.next)">NEXT PAGE</button>


  </footer>
</template>

<style scoped>



</style>
