<template>
  <v-card class="mx-auto mb-5" max-width="700">
    <v-img
      :src="deal.photos.length > 0 ? deal.photos[0] : 'https://anest-iwata.com.br/wp-content/uploads/2016/10/Sem-imagem.png'"
      height="200px"
    ></v-img>

    <v-card-title> {{deal.title}} </v-card-title>

    <v-card-subtitle> {{deal.location.city}}/{{deal.location.state}} </v-card-subtitle>

    <v-card-actions>
      <v-btn color="orange lighten-2" text :to="'/negociacao?id=' + deal._id"> Mostrar </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  props: {
    deal: {
      required: true
    }
  }
};
</script>

<style>
</style>