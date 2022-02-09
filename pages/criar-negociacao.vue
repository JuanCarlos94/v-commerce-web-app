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
                v-if="deal.type == 'Troca'"
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
                v-mask="'########'"
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
          <v-spacer />
          <v-btn color="primary" @click="save" :disabled="!valid"> Salvar </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import { VMoney } from "v-money";
export default {
  name: "CriarNegociacao",
  middleware: "auth",
  data() {
    return {
      valid: false,
      dealTypes: ["Venda", "Troca", "Desejo"],
      urgencyTypes: ["Baixa", "Média", "Alta", "Data"],
      menu: false,
      deal: {
        type: "Venda",
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
          decimal: ',',
          thousands: '.',
          precision: 2,
          masked: true /* doesn't work with directive */
        }
    };
  },
  methods: {
    save() {
      this.$axios
        .post("/deal", this.deal)
        .then((res) => {
          window.location.href = "/negociacao?id=" + res.data.deal._id;
        })
        .catch((err) => {
          console.log(err);
          alert('Erro ao salvar, tente novamente!');
        });
    },
    addPhoto() {
      this.deal.photos.push({ src: this.currentPhoto });
      this.currentPhoto = "";
    },
    removePhoto(i) {
      this.deal.photos.splice(i, 1);
    },
  },
  mounted() {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition((position) => {
        this.deal.location.lat = position.coords.latitude;
        this.deal.location.lng = position.coords.longitude;
      });
    }
    this.deal.location.address = this.$auth.user.location.address;
    this.deal.location.city = this.$auth.user.location.city;
    this.deal.location.state = this.$auth.user.location.state;
    this.deal.location.zip_code = this.$auth.user.location.zip_code;
  },
  directives: {
    money: VMoney,
  },
};
</script>

<style>
</style>