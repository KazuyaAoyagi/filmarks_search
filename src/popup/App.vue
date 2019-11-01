<template>
  <div class="container">
    <h2>Movie Score Search</h2>
    <div class="tab-container">
      <p v-on:click="selectTab('movies')" v-bind:class="{'selected':(searchType == 'movies')}">映画</p>
      <p v-on:click="selectTab('dramas')" v-bind:class="{'selected':(searchType == 'dramas')}">ドラマ</p>
    </div>
    <input type="text" v-model="searchtext" placeholder="検索したい作品名を入力してください" />

    <ul>
      <li v-for="item in result">
        {{ item.name }}
        {{ item.score }}
      </li>
      </ul>

  </div>
</template>

<script>
import axios from 'axios'
import $ from 'jquery'
export default {
  data() {
    return {
      url:"",
      searchtext: '',
      param:"{}",
      searchType:"movies",
      result:[],
    }
  },
  methods: {
    selectTab : function(type) {
      this.searchType = type
    }
  },
  computed: {
    
  },
  watch: {
    searchtext:function(val) {
       var vm = this
       let url = `https://filmarks.com/search/${this.searchType}?q=${val}`
       
      axios.get(url).then(function(response) {
        let parser = new DOMParser();
        let doc =parser.parseFromString(response.data, 
"text/html")
        
        let html = doc.getElementsByClassName("l-main")[0].getElementsByClassName("p-movie-cassette")
        let r = []
        $(html).each((index, 
element) => {
          let data = {
            name:element.getElementsByTagName("h3")[0].innerText,
            score:element.getElementsByClassName("c-rating__score")[0].innerText
          }
          r.push(data)
        })
        vm.result = r
      })
    }
  }
}
</script>

<style lang="scss">
.container {
  text-align: center;
}

ul {
    margin-top:20px;
    li {
      display: block;
      font-size: 12px;
      border-bottom: 1px solid #ccc
    }
  }

.tab-container {
  margin: 5px;
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
</style>
