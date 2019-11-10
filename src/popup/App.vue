<template>
  <div class="container">
    <h2>Movie Score Search</h2>
    
    <div class="seach-form" :class="{'sticky': position > 35}">
      <input class="search-input" type="text" autofocus v-model="searchtext" placeholder="検索したい作品名を入力してください" />

      <div class="tab-container">
        <p v-on:click="selectTab('movies')" v-bind:class="{'selected':(searchType == 'movies')}">映画</p>
        <p v-on:click="selectTab('dramas')" v-bind:class="{'selected':(searchType == 'dramas')}">ドラマ</p>
      </div>
    </div>
    
    
    <ul>
        <li v-for="item in result">
          <div class="mr-1">
            <img :src="item.image" width="40" height="40"/>
          </div>
          <div>
            <p>{{ item.name }}</p>
            <p>{{ item.score }}</p>
          </div>
        </li>
      </ul>
      <vue-loading v-if="isLoading" type="spin" color="#333" :size="{ width: '50px', height: '50px' }"></vue-loading>
      
      <button v-if="result.length > 0" class="btn-search-more" v-on:click="searchMore()" v-bind:disabled="this.isLoading" >もっと見る</button>
    
  </div>
</template>

<script>
import axios from 'axios'
import $ from 'jquery'
import { VueLoading } from 'vue-loading-template'
export default {
  components: {
    VueLoading
  },
  data() {
    return {
      url:"",
      searchtext: '',
      param:"{}",
      searchType:"movies",
      result:[],
      isLoading:false,
      pageCount:1,
      position:0
    }
  },
  mounted() {
    document.onscroll = (e) => {
      this.position = document.documentElement.scrollTop || document.body.scrollTop;
    }
  },
  methods: {
    handleScroll() {
      this.position = document.documentElement.scrollTop || document.body.scrollTop;
    },
    selectTab : function(type) {
      
      if(this.searchType === type) {
        return
      }

      this.searchType = type
      this.result = []
      this.pageCount = 1
      if(this.searchtext !== '') {
        this.search()
      }
    },
    searchMore(){
      this.pageCount ++
      this.search()
    },
    search(){
      var vm = this
      let url = `https://filmarks.com/search/${this.searchType}?page=${this.pageCount}&q=${this.searchtext}`
      console.log(url);
      
       //vm.result = []
       vm.isLoading = true
      axios.get(url).then(function(response) {
        let parser = new DOMParser();
        let doc =parser.parseFromString(response.data, 
"text/html")
        
        let html = doc.getElementsByClassName("l-main")[0].getElementsByClassName("p-movie-cassette")
        let r = []
        $(html).each((index, 
element) => {
          //console.log($(element).find('.p-movie-cassette__other-info').text());
          
          let data = {
            name:element.getElementsByTagName("h3")[0].innerText ? element.getElementsByTagName("h3")[0].innerText:'',
            score:element.getElementsByClassName("c-rating__score")[0].innerText ? element.getElementsByClassName("c-rating__score")[0].innerText:'',
            image:$(element).find('.p-movie-cassette__jacket img')[0]['src'],
          }
          //console.log(data);
          
          vm.result.push(data)
        })
        
        vm.isLoading = false
      })
    }
  },
  computed: {
    
  },
  watch: {
    searchtext:function() {
      this.pageCount = 1
      this.result = []
      this.search()
    }
  }
}
</script>

<style lang="scss">
.container {
  text-align: center;
  padding:10px 10px 20px 10px;
}

.sticky{
  position: fixed;
  top:0;
  left:0;
  width: 100%;
  padding: 10px;
  background-color: #fff;
}
.search-input {
  margin:10px 0px;
  text-align: center;
  width:100%;
  height: 25px;
}
.btn-search-more {
  width: 100%;
  height: 30px;
  text-align: center;
  border-radius: 3px;
  border:none;
  background-color: #555345;
  color: #fff;
}
.btn-search-more:disabled {
  background-color: #676557;;
  color: #ccc;
}
ul {
    margin-top:20px;
    li {
      display: flex;
      font-size: 12px;
      padding: 5px;
      border-bottom: 1px solid #ccc;
      text-align: left;
      img {

      }
    }
  }

.tab-container {
  margin: 5px 0px;
  display: flex;
  justify-content: space-between;
  
  p {
    cursor: pointer;
    background: #000;
    font-size: 14px;
    color: #fff;
    display: block;
    padding: 5px;
    text-align: center;
    width: 49%;
    height: 30px;
    border-radius: 3px;
    border-right: solid 1px #ccc;
  }

  p.selected {
    background-color: #ffe100;
    color: #000;
    font-weight: bold
  }
  
}

.mr-1 {
  margin-right: 10px;
}
</style>
