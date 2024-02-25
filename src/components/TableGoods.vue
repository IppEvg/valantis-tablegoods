<template>
<div>
  <v-progress-circular 
  v-if="loading" 
  model-value="20" 
  :size="57" 
  :width="10" 
  color="primary" 
  indeterminate>
  </v-progress-circular>
  <div>
    <h1> Список продуктов</h1>
    <v-list-item>
      <v-card
    class="mx-auto my-8"
    max-width="1200"
    elevation="16"
    v-for="item of listGoods" :key="item.id"
      >
        <v-card-title>
          {{item.product}}
        </v-card-title>
        <v-card-subtitle>
          {{item.brand}}
        </v-card-subtitle>
        <v-card-text>
          {{item.price}}
        </v-card-text>
      </v-card>
    </v-list-item>
    <div class="pagination">
      <v-pagination :length="getPages"  ></v-pagination>
    </div>
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
        listGoods:[],
        page:1,
        loading:false
        
      }
    },
    methods:{
      getTimestamp: function() {
        return new Date().toISOString().slice(0,10).replace(/-/g, '');
      },
      getterIdFromApi:function(data){
        let goodsId = [...new Set(data.result)]//избавляемся от дублей сразу, если они есть 
        this.listId=goodsId
        // let periodOfPage = goodsId
      console.log(goodsId.slice(0,50));
        this.handlerGetter("get_items", { "ids": goodsId.slice(0,50) }, this.getterGoodsFromApi)
      },  
      getterGoodsFromApi: function(data){
        let dataWithoutDublicate = [...data.result.filter((item, index, self) => index == self.findIndex((e) => e.id === item.id))]
      this.listGoods=[...this.listGoods,...dataWithoutDublicate]
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
    },
    computed:{
      getPages: function(){
      return parseInt(this.listId.length/50)
      }
    },
    mounted (){
     
    this.handlerGetter("get_ids",{},this.getterIdFromApi)
    
      // for (let i = 0 ; i <Math.ceil(this.listId.length/100);i++){
      //   console.log(133333);
      //     this.handlerGetter("get_items",{"ids": this.listId.filter(e=>e.page==i)},this.getterGoodsFromApi)
      // }
   }
  }
  
</script>

<style scoped lang="scss">
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
  display: block;
  min-height: 35px;
}
.v-card{
  display: grid;
    grid-template-columns: 1.5fr 1fr 1fr;
    grid-template-rows: 35px;
    width: 100%;
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
  width: fit-content;
}
.pagination{
  width: 50%;
  margin: 20px auto;
}
</style>
