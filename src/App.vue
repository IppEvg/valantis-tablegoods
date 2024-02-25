<template>
  <v-app>
    
    <v-main>
      <TableGoods></TableGoods>
    </v-main>
    <v-footer   class="bg-broun footer">
      <div class="copyright">
        &copy; 2024 Ippolitov 
      </div>
      <div class="links">
        <p> Phone: 8(903)2452420</p>
        <div class="messanger">
        <div><v-btn size="x-small" text href="https://t.me/EvgeniyIppolitov"><img  class="imgMessamger" src="./assets/telegr.svg" alt="Telegram"/> //t.me/Evgeniy Ippolitov</v-btn> </div>
        </div>
      </div>
    </v-footer>
  </v-app>
</template>

<script>
import TableGoods from './components/TableGoods.vue';

export default {
  name: 'App',

  components: {
    TableGoods,
  },

  data: () => ({
    //
  }),
};
</script>

<style lang="scss">

.v-sheet{
  display: flex;
  .links{
    p{
    margin-bottom: 5px;
  }
}
}

.footer {
  background-color: blue;
  display: flex;
  align-items: center;
  justify-content: space-around;
  padding: 2em;
  text-align: center;
}
.messanger {
  display: flex;
  max-height: 20px;

  .imgMessamger {
    height: 20px;
    min-width: 20px;
    margin-right: 0.5em;
  }

}



</style>