<template>
  <div>
    <h1 class="titleList"> Список продуктов</h1>
    <form  class="filterForm" @submit.prevent="getFilter(input)">
      <div class="filterForm_block" >
        <v-text-field
            label="Search" 
            v-model="input" 
          />
        <v-btn type="submit">
          <img src="../assets/icons8-поиск.svg" class="filterForm_icon"  alt="find">
        </v-btn>
      </div>
    </form>
    <v-progress-circular 
      v-if="loading" 
      model-value="20" 
      :size="57" 
      :width="10" 
      color="primary" 
      indeterminate>
    </v-progress-circular>
    <v-list-item v-else >
      <v-card class="head_table">
        <v-card-subtitle>
          Id
        </v-card-subtitle>
        <v-card-title>
          Название
        </v-card-title>
        <v-card-subtitle >
          Бренд
        </v-card-subtitle> 
        <v-card-text>
          Цена
        </v-card-text>
      </v-card>
      <v-card
    class="mx-auto my-8"
    max-width="1200"
    elevation="16"
    v-for="item of listGoods" :key="item.id"
      >
        <v-card-subtitle>
          {{item.id}}
        </v-card-subtitle>
        <v-card-title>
          {{item.product}}
        </v-card-title>
        <v-card-subtitle v-if="item.brand">
          {{item.brand}}
        </v-card-subtitle> 
        <v-card-subtitle v-else>
               ...
        </v-card-subtitle>
        <v-card-text>
          {{item.price}}
        </v-card-text>
      </v-card>
    </v-list-item>
    <div class="pagination">
      <v-pagination :length="getPages" v-model="page" > </v-pagination>
    </div>
  </div>
</template>

<script>
import md5 from 'js-md5'
  export default {
    name: 'TableGoods',
    data(){
      return{
        password:'Valantis',
        listId:[],
        listBrands:[],
        listGoods:[],
        page:1,
        loading:false,
        input:""
        
      }
    },
    methods:{
      getTimestamp: function() {
        return new Date().toISOString().slice(0,10).replace(/-/g, '');
      },
      getterIdFromApi:function(data){
        
        let goodsId = [...new Set(data.result)]//избавляемся от дублей сразу, если они есть 
        this.listId=goodsId
      },  
      getterGoodsFromApi: function(data){
        // let dataWithoutDublicate = [...data.result.filter((item, index, self) => index == self.findIndex((e) => e.id === item.id))]
      this.listGoods=[...data.result.filter((item, index, self) => index == self.findIndex((e) => e.id === item.id))]
      this.loading=false
      },
      handlerGetter: function(action,params,func){
        this.loading=true
        fetch('http://api.valantis.store:40000/',{
        method:'POST',
        headers:{
          'X-Auth':`${md5(`${this.password}_${this.getTimestamp()}`)}`,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          'action':action,
          'params': params
          })
      })
          .then(response => {
            if (response.ok) {
              return response.json()
            }else{
              response.json()
              throw new Error('Ошибка при получении данных');
            }
          })
          .then((data)=>func(data))
      },
      getListBrands:function(data){
        this.listBrands = [...new Set(data.result)]
      },
      getFilter(input){
        this.loading=true
        if(input.match(/\d+/i)){
          this.handlerGetter("filter", {"price": +input}, this.getterIdFromApi)
        }
        if(this.listBrands.find(item=>item==input)){
          this.handlerGetter("filter", {"brand": input}, this.getterIdFromApi)
        }else if(input.match(/\D+/i)){
          this.handlerGetter("filter", {"product": input}, this.getterIdFromApi)
        }
        if(input===""){
          return this.handlePaginationClick
        }
      },
    },
    computed:{
      getPages: function(){
      return parseInt(this.listId.length/50)
      },
      handlePaginationClick: function(){
      return this.handlerGetter("get_items", { "ids": this.listId.slice((this.page-1)*50,this.page*50) }, this.getterGoodsFromApi)
      }
    },
    mounted (){

  this.handlerGetter("get_ids",{},this.getterIdFromApi)
  this.handlerGetter("get_fields", {"field":"brand"}, this.getListBrands) 
   }
  }
  
</script>

<style scoped lang="scss">
.titleList{
  margin:  15px auto 5px;
  text-align: center;
}
.theme--light.v-btn.v-btn--has-bg{
  background-color:none ;
}
.v-btn:not(.v-btn--round).v-size--default{
  padding: 2px;
  width: auto;
  height: 100%;
}
.theme--light.v-card > .v-card__text{
  color: black;
}
.filterForm{
  display: flex;
  justify-content: end;
  align-items: center; 
}
.filterForm_block[data-v-5b586258]{
  display: flex;
  padding: 5px;
  flex-direction: row;
  align-items: center;
  gap: 4px;
  .v-text-field__details{
  display: none;
}
  .filterForm_block{
    display: flex;
    justify-content: end;
  border: 1px solid black;
  padding: 5px;
  }

}
.v-text-field__details{
  display: none!important;
}
.filterForm_icon{
  height: 30px;
}

.load{
  display: block;
  flex-direction: column;
  align-items: center;
  justify-items: center;
}
.v-progress-circular{
  display: block;
  margin: 40px auto;
}
.v-list-item{
  min-height: 35px;
   display: block;
}
.v-card{
  width: 100%;
  display: grid;
  grid-template-columns: 1.3fr 2fr 1fr 1fr;
  grid-template-rows: 35px;
  gap:1px;
}
.v-application, .my-8{

  line-height: normal;
  margin-top: 3px!important;
  margin-bottom: 3px!important;
}
.v-sheet{
  justify-content: space-between;
}
.container{
padding: 0;
}

.head_table{
  background-color:lightgrey ;
  width: 100%;
}
.v-card__title, .v-card__subtitle, .v-card__text{
  font-size: small;
  line-height: normal;
  padding: 3px;
  margin: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.v-card__text{
  width: 100%;
}
.pagination{
  width: 50%;
  margin: 20px auto;
}


</style>
