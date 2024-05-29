<script>
  import axios from "axios";

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

body{
  background: #dcdcdc;
}

header.header{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 50vh;
}

main{
  background: #202329;
  width: 100%;
  min-height: 45vh;
}

nav{
  display: flex;
  flex-grow: 1;
  flex-direction: row;
  justify-content: flex-end;
  align-items: center;
  gap: 10px;
  width: 95%;
  margin: 0 auto;
  border-bottom: 1px solid #dcdcdc;
}

.block{
  margin: 1rem;
  font-weight: bold;
  font-size: 1rem;
  cursor: pointer;
}

.block.left{
  margin-right: auto;
  width: 50px;
  height: 50px;
}

.block.left img{
  width: 100%;
  height: 100%;
}

.block:hover{
  color: orange;
  transition: 0.2s;
}

div.block.support{
  border: 1px solid orange;
  padding: 5px 10px;
  font-size: 0.7rem;
  border-radius: 10px;
}

div.block.support span{
  font-weight: bold;
}

div.block.support:hover{
  color: white;
  background: orange;
}

div.big_description{
  flex-grow: 9;
  display: flex;
  align-items: center;
  text-align: center;
}

span.main_name {
  font-size: 6rem;
  font-weight: bold;
  color: rgb(32, 35, 41);
  margin: 20px 0;
}

div.filter{
  width: 100%;
  padding: 20px 0;
  color: white;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-around;
}

div.select_container{
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  width: 15%;
}

span.title_name_select{
  font-size: 0.7rem;
  color: gray;
  margin: 0;
}

div.filter button{
  background: rgba(0,0,0,0);
  border: 1px solid orange;
  color: orange;
  width: 15%;
  border-radius: 5px;
  padding: 5px;
  align-self: flex-end;
  cursor: pointer;
  transition: 0.2s;
}

div.filter button:hover{
  background-color: orange;
  color: white;
}

div.filter select{
  background: rgba(0,0,0,0);
  color: orange;
  border: none;
  border-bottom: 1px solid orange;
  padding: 0 5px 5px 5px;
  width: 100%;
  font-size: 1rem;
  cursor: pointer;
  transition: 0.2s;
}

div.filter select:hover{
  color: darkorange;
  border-color: darkorange;
}

div.card_container{
  display: flex;
  flex-wrap: wrap;
  width: 90%;
  justify-content: space-around;
  margin: auto;
  grid-template-columns: 50% 50%;
  padding-top: 50px;
}

div.card{
  background: #3c3e44;
  color: white;
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  border-radius: 5px;
  overflow: hidden;
  padding: 0;
  margin: 10px;
  width: 600px;
  height: 200px;
}

div.image_div{
  width: 33%;
  height: 100%;
}

div.image_div img{
  width: 100%;
  height: 100%;
}

div.info_div{
  width: auto;
  padding: 5px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  gap: 10px;
}

div.block_info{
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

span.full_name{
  font-size: 1.5rem;
  font-weight: bold;
  cursor: pointer;
}

span.full_name:hover{
  color: orange;
}

span.point{
  width: 10px;
  height: 10px;
  border-radius: 50%;
  display: inline-block;
}

span.point.grey{
  background: grey;
}

span.point.green{
  background: green;
}

span.point.red{
  background: red;
}

span.layout_text{
  color: rgb(158, 158, 158);
  font-size: 16px;
  font-weight: 500;
}

a.url_text{
  font-weight: normal;
  word-wrap: break-word;
  font-kerning: normal;
  font-size: 18px;
  cursor: pointer;
  color: white;
  text-decoration: none;
}

a.url_text:hover{
  color: orange;
}

footer{
  background: #14151b;
  padding: 50px 0;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 10px;
  min-height: 5vh;
}

footer span{
  color: white;
  cursor: pointer;
  transition: 0.2s;
}

footer span:hover{
  color: orange;
}

footer button{
  cursor: pointer;
  background: rgba(0,0,0,0);
  border: 1px solid orange;
  color: orange;
  transition: 0.2s;
}

footer button:hover{
  background: orange;
  color: white;
}

footer button.disabled{
  cursor: default;
  background: rgba(0,0,0,0);
  border: 1px solid gray;
  color: gray;
  transition: 0.2s;

}


@media only screen and (max-width: 768px) {
  span.main_name {
    font-size: 4rem;
    font-weight: bold;
    color: rgb(32, 35, 41);
    margin: 20px 0;
  }

  div.card{
    flex-direction: column;
    width: 80%;
    height: auto;
  }

  div.image_div{
    width: 100%;
    height: auto;
  }
}

</style>
