<template>
  <v-card>
    <v-tabs v-model="tab" icons-and-text>
      <v-tabs-slider></v-tabs-slider>

      <v-tab href="#tab-deals">
        Negociações
        <v-icon>mdi-phone</v-icon>
      </v-tab>

      <v-tab href="#tab-bids">
        Ofertas
        <v-icon>mdi-heart</v-icon>
      </v-tab>
    </v-tabs>

    <v-tabs-items v-model="tab">
      <v-tab-item :key="1" :value="'tab-deals'">
        <v-card flat>
          <v-card-text>
            <v-row>
              <v-col cols="6" v-for="(deal, i) in deals" :key="i"
                ><DealCard :deal="deal"/></v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-tab-item>
      <v-tab-item :key="2" :value="'tab-bids'">
        <v-card flat>
          <v-card-text>
            <v-row>
              <v-col cols="6" v-for="(bid, i) in bids" :key="i"><DealCard :deal="bid"/></v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-tab-item>
    </v-tabs-items>
  </v-card>
</template>

<script>
export default {
  name: "MyDeals",
  middleware: "auth",
  data: () => ({
    reveal: false,
    deals: [],
    bids: [],
    tab: null,
  }),
  methods: {
    loadDeals() {
      this.$axios.get('/deal')
        .then(res => {
          console.log(res);
          this.deals = res.data;
        })
        .catch(e => {
          console.log(e);
          alert('Erro ao carregar negociações, tente novamente.')
        })
    },
    loadBids() {
      this.$axios.get("/deal/userBids")
        .then(res => {
          this.bids = res.data;
        })
        .catch(err => {
          console.log(err);
          alert('Erro ao carregar ofertas, tente novamente.')
        })
    },
  },
  mounted(){
    this.loadDeals();
    this.loadBids();
  }
};
</script>

<style>
.v-card--reveal {
  bottom: 0;
  opacity: 1 !important;
  position: absolute;
  width: 100%;
}
</style>