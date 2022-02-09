<template>
  <v-row>
    <v-col v-for="(deal, n) in deals" :key="n" cols="4">
      <DealCard :deal="deal" />
    </v-col>
  </v-row>
</template>

<script>
export default {
  middleware: "auth",
  data: () => ({
    deals: [],
    lat: "",
    lng: "",
  }),
  methods: {
    async loadDeals() {
      let url = "/deal/search";
      if(this.lat && this.lng){
        url += "?lat=" + this.lat + "&lng=" + this.lng;
      }
      this.$axios
        .post(url)
        .then((res) => {
          this.deals = res.data;
        })
        .catch((err) => {
          console.log(err);
          alert('Erro ao carregar negociações, tente novamente.')
        });
    },
    setLatLng() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition((position) => {
          this.lat = position.coords.latitude;
          this.lng = position.coords.longitude;
        });
      }
    },
  },
  mounted() {
    this.setLatLng();
    this.loadDeals();
  },
};
</script>

<style>
</style>