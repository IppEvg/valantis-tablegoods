<template>
  <v-container>
 
  </v-container>
</template>

<script>
  export default {
    name: 'TableGoods',
    data(){
      return{
        password:'Valantis',
      }
    },

   beforeMount(){
    const date = new Date().toISOString().slice(0,10).replace(/-/g, '');
    let params = {
      method:'POST',
      headers:{
        'X-Auth':`md5${this.password}_${date}`
      },
      action:'get_fields'


    }
    console.log(params.headers);
    async function getListGoods(){
      try {
        let response = await fetch('http://api.valantis.store:40000/',params)
    let goods = await response.json();
    return console.log(goods); 
      } catch (error) {
        console.log(error);
      }
      
    }
    getListGoods()
    
     

   }
  }
</script>

<style scoped lang="scss">
</style>
