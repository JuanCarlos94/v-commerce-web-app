<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="8" lg="6">
      <v-card>
        <v-card-title class="headline justify-center"> Cadastro </v-card-title>
        <v-card-text>
          <v-container>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-select
                v-model="deal.type"
                :items="dealTypes"
                label="Selecione..."
              ></v-select>
              <v-text-field
                v-model="deal.value"
                label="Valor"
                prefix="R$"
                :rules="[rules.required]"
                v-money="money"
              ></v-text-field>
              <v-text-field
                v-model="deal.description"
                label="Descrição"
                required
                :rules="[rules.required]"
              ></v-text-field>
              <v-text-field
                v-model="deal.trade_for"
                label="Troca por"
                required
              ></v-text-field>
              <v-text-field
                v-model="deal.location.lat"
                :counter="40"
                label="Latitude"
                required
              ></v-text-field>
              <v-text-field
                v-model="deal.location.lng"
                :counter="40"
                label="Longitude"
                required
              ></v-text-field>
              <v-text-field
                v-model="deal.location.address"
                :counter="40"
                label="Endereço"
                :rules="[rules.required]"
                required
              ></v-text-field>
              <v-text-field
                v-model="deal.location.city"
                :counter="40"
                label="Cidade"
                :rules="[rules.required]"
                required
              ></v-text-field>
              <v-text-field
                v-model="deal.location.state"
                :counter="40"
                label="Estado"
                :rules="[rules.required]"
                v-mask="'AA'"
                required
              ></v-text-field>
              <v-text-field
                v-model="deal.location.zip_code"
                :counter="40"
                label="CEP"
                :rules="[rules.required]"
                required
              ></v-text-field>
              <v-select
                v-model="deal.urgency.type"
                :items="urgencyTypes"
                label="Urgência"
              ></v-select>
              <v-text-field
                v-model="deal.urgency.limit_date"
                label="Data limite"
                v-mask="'##/##/####'"
                v-if="deal.urgency.type == 'Data'"
              ></v-text-field>
              <v-text-field
                v-model="currentPhoto"
                :append-outer-icon="'mdi-plus'"
                clear-icon="mdi-close-circle"
                label="Foto URL"
                type="text"
                @click:append-outer="addPhoto"
              ></v-text-field>
              <v-chip
                v-for="(p, i) in deal.photos"
                :key="i"
                class="ma-2"
                close
                @click="removePhoto(i)"
              >
                {{ p.src }}
              </v-chip>
            </v-form>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red accent-4" dark @click="back"> Cancelar </v-btn>
          <v-spacer />
          <v-btn color="primary" @click="update" :disabled="!valid">
            Editar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import { VMoney } from "v-money";
export default {
  name: "EditarNegociacao",
  middleware: "auth",
  data() {
    return {
      valid: false,
      dealTypes: ["Venda", "Troca", "Desejo"],
      urgencyTypes: ["Baixa", "Média", "Alta", "Data"],
      menu: false,
      deal: {
        _id: "",
        type: "",
        value: "",
        description: "",
        trade_for: "",
        location: {
          lat: "",
          lng: "",
          address: "",
          city: "",
          state: "",
          zip_code: "",
        },
        urgency: {
          type: "",
          limit_date: "",
        },
        photos: [],
      },
      currentPhoto: "",
      rules: {
        required: (value) => !!value || "Campo obrigatório",
      },
      money: {
        decimal: ",",
        thousands: ".",
        precision: 2,
        masked: true /* doesn't work with directive */,
      },
    };
  },
  methods: {
    addPhoto() {
      this.deal.photos.push({ src: this.currentPhoto });
      this.currentPhoto = "";
    },
    removePhoto(i) {
      this.deal.photos.splice(i, 1);
    },
    loadDeal(id) {
      this.$axios.get("/deal/" + id).then((res) => {
        this.deal = res.data.deal;
        this.money.value = res.data.deal.value;
        this.userOwner = this.$auth.user._id === this.deal.user._id;
      });
    },
    update() {
      this.$axios
        .put("/deal/" + this.deal._id, this.deal)
        .then((res) => {
          console.log(res);
          window.location.href = "/negociacao?id=" + this.deal._id;
        })
        .catch((err) => {
          console.log(err);
          alert('Erro ao editar negociação, tente novamente.')
        });
    },
    back() {
      window.location.href = "/negociacao?id=" + this.deal._id;
    },
  },
  mounted() {
    this.loadDeal(this.$route.query.id);
  },
  directives: {
    money: VMoney,
  },
};
</script>

<style>
</style>