<template>
  <div id="app">
    <header id="index">
      <img class = "loupe" src ="../static/loupe.svg" >
    </header>
    <main>
      <div class="name" v-if="idoldata[tmp_idol]" v-text="idoldata[tmp_idol].name"></div>
      <div class ="card_image">
        <img class="img_displayed" v-if="getImage" v-bind:src = "getImage">
        <div class= "buttons">
          <img class="card" src="../static/card.svg" v-on:click="state=0">
          <img class="fullsize" src="../static/fullsize.svg" v-on:click="state=1">
          <img class="standalone" src="../static/standalone.svg" v-on:click="state=2">
          <img class="petit" src="../static/petit.svg" v-on:click="state=3">  
        </div>           
      </div>
      <div class ="variable" v-if="idoldata[tmp_idol]" >
        <img class="icon" v-for="(img, index) in idoldata[tmp_idol].imgs" v-bind:key="img.id" v-bind:src="img.icon" v-on:click="tmp_img = index">
      </div>
      <div class = "unit">
        <p>参加ユニット</p>
        <div class="unit_list">
          <div v-for="unit in units_belong" v-bind:key="unit.name">
          <p>{{unit.name}}</p>
          <img class="icon" v-for="member in unit.member" v-bind:key="member" v-bind:src="getIcon(member)" v-on:click="changeIdol(member)">
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import 'normalize.css'

export default {
  name: 'App',
  components: {
  },
  data() {
    return{
      idoldata: [],
      unitdata: [],
      tmp_idol: 124,
      tmp_img: 0,
      state: 0
    }
  },
  created() {
    this.axios.get('http://localhost:8080/idol_data.json')
    .then(response => {
      this.idoldata = response.data})
    this.axios.get('http://localhost:8080/unit_data.json')
    .then(response => {
      this.unitdata = response.data})
  },
  computed: {
    getImage: function(){
      let idoldata = this.idoldata
      let tmp_idol = this.tmp_idol
      let tmp_img = this.tmp_img
      let state = this.state

      if(idoldata[tmp_idol]){
        switch(state){
          case 0 :
            return  idoldata[tmp_idol].imgs[tmp_img].card
          case 1 :
            return  idoldata[tmp_idol].imgs[tmp_img].fullsize
          case 2 :
            return  idoldata[tmp_idol].imgs[tmp_img].standalone
          case 3 :
            return  idoldata[tmp_idol].imgs[tmp_img].petit
        }
      }
      return null
    },
    //watchにしたいが初期描画がうまくいかないのでcomputedにする
    units_belong: function(){
      let units_belong = []
      let tmp_idol = this.tmp_idol
      let idoldata = this.idoldata
      if(!idoldata[tmp_idol]){
        //初期ロード時にエラーが出ないように
        return []
      }
      this.unitdata.forEach(unit =>{
        if(unit.member.includes(idoldata[tmp_idol].name)){
            units_belong.push(unit);
        }
      })
      return units_belong
    }
  },
  methods: {
    getIcon(member){
      let idoldata = this.idoldata
      let tmp = idoldata.find(idol => idol.name === member)
      return tmp.imgs[0].icon
    },
    changeIdol(member){
      let idoldata = this.idoldata
      let tmp = idoldata.find(idol => idol.name === member)
      this.tmp_idol = idoldata.indexOf(tmp)
    }
  },
  watch:{
    tmp_idol: function(){
      let tmp_idol = this.tmp_idol
      let idoldata = this.idoldata
      //tmp_idolが変更された時だけ最新の画像を表示する
      this.tmp_img = idoldata[tmp_idol].imgs.length - 1
    }    
  }

}
</script>

<style>
body {
    margin: 0 auto;
    padding: 0;
    height: 100%;
    max-width: 100vh;
    min-width: 15rem;
    min-height: 500px;
    position: relative;
    background: #fff;
}
#index {
    position: relative;
    background: #CEDEE8;
    text-align: right;
    min-height: 5px;
    vertical-align: middle;
}
.loupe {
    position: relative;
    right: 0.5rem;
    top: 0.2rem;
}
.main {
    margin: 0;
}
.name {
    position: relative;
    width: 100%;
    height: 7.5vh;
    font-family: Adamina;
    font-style: normal;
    font-weight: normal;
    font-size: 4vh;
    align-items: center;
    text-align: center;
    letter-spacing: 0.25rem;
    top: 1vh;
    margin: 0;

    color: #58889D;
}
.card_image{
    position: relative;
    text-align: center;
}
.img_displayed {
    max-width: 100%;
    min-height: 15rem;
}
.buttons {
    position: absolute;
    top: 85%;
    left: 0;
    right: 0;
    margin: auto;
    /*letter-spacing:1rem;*/
}
.variable {
    color: #5989cf;
    background: #c6e4ff;
    border-bottom: solid 6px #aac5de;
    border-radius: 9px;
}
.icon{
    max-height: 10%;
    max-width: 10%;
    min-height: 5%;
    min-width: 5%;
}
.unit{
    text-align: center;
    background-color: #CEDEE8;
    border-radius: 1rem;
}
.unit_list{
    text-align: left;
    list-style: none;
}
p {
    size: 2vh;
    margin: 0;

}
#search{
    position: relative;
    background: #CEDEE8;
    text-align: left;
    vertical-align: middle;
    min-height: 5vh;
}
.triangle {
    position: relative;
    left: 0.5rem;
    top: 0.2rem;
}
.search_area{
    text-align: center;
    min-height: 10vh;
    vertical-align: middle;
    border-bottom: 1px solid #CEDEE8;
}
.inputs{
    padding-top: 2vh;
}
.name_input{
    border: 1px solid #CEDEE8;
    box-sizing: border-box;
    border-radius: 10px;
    width: 40vw;
    height: 4vh;
}
.search_button{
    background: #CEDEE8;
    border: 1px solid #CEDEE8;
    box-sizing: border-box;
    border-radius: 10px;
    height: 4vh;
    color: #FFFFFF;
}
.display_area{
    height: 84vh;
    padding-top: 1vh;
}
</style>
