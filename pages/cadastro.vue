<template>
  <v-row justify="center" align="center">
    <v-col cols="6">
      <v-card>
        <v-card-title class="headline justify-center"> Cadastro </v-card-title>
        <v-card-text>
          <v-container>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-text-field
                v-model="user.name"
                :counter="40"
                :rules="[rules.required]"
                label="Name"
                required
              ></v-text-field>
              <v-text-field
                v-model="user.email"
                :rules="emailRules"
                label="E-mail"
                required
              ></v-text-field>
              <v-text-field
                v-model="user.login"
                :counter="20"
                label="Login"
                :rules="[rules.required]"
                required
              ></v-text-field>
              <v-text-field
                  v-model="user.password"
                  :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                  :rules="[rules.required]"
                  :type="show1 ? 'text' : 'password'"
                  label="Password"
                  @click:append="show1 = !show1"
                ></v-text-field>
                <v-text-field
                  v-model="user.confirmPassword"
                  :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                  :rules="[rules.required]"
                  :type="show1 ? 'text' : 'password'"
                  label="Confirm Password"
                  @click:append="show1 = !show1"
                ></v-text-field>
                <v-text-field
                v-model="user.location.lat"
                :counter="40"
                label="Latitude"
                required
              ></v-text-field>
              <v-text-field
                v-model="user.location.lng"
                :counter="40"
                label="Longitude"
                required
              ></v-text-field>
                <v-text-field
                v-model="user.location.address"
                :counter="40"
                label="Endereço"
                :rules="[rules.required]"
                required
              ></v-text-field>
              <v-text-field
                v-model="user.location.city"
                :counter="40"
                label="Cidade"
                :rules="[rules.required]"
                required
              ></v-text-field>
              <v-text-field
                v-model="user.location.state"
                :counter="40"
                label="Estado"
                :rules="[rules.required]"
                required
                v-mask="'AA'"
              ></v-text-field>
              <v-text-field
                v-model="user.location.zip_code"
                :counter="40"
                label="CEP"
                :rules="[rules.required]"
                v-mask="'########'"
                required
              ></v-text-field>
            </v-form>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" @click="register" :disabled="!valid"> Enviar </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data() {
    return {
      nameRules: [],
      valid: false,
      user: {
        name: "",
        email: "",
        login: "",
        password: "",
        confirmPassword: "",
        location: {
            lat: '',
            lng: '',
            address: "",
            city: "",
            state: "",
            zip_code: ''
        }
      },
      emailRules: [
        v => !!v || 'E-mail é obrigatório',
        v => /.+@.+\..+/.test(v) || 'E-mail deve ser válido',
      ],
      show1: false,
      rules: {
        required: (value) => !!value || "Campo obrigatório",
        min: (v) => v.length >= 8 || "min 6, max 8",
        emailMatch: () => `The email and password you entered don't match`,
      }
    };
  },
  methods: {
    async register(){
      this.$axios.post('/user', this.user)
      .then(async (res) => {
        await this.$auth.loginWith('local', {data: {login: this.user.login, password: this.user.password}});
        window.location.href = '/';
      })
      .catch(err => {
        console.log(err.response);
        alert('Erro durante o cadastro, tente novamente!')
      })
    }
  },
  mounted(){
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition((position) => {
        this.user.location.lat = position.coords.latitude;
        this.user.location.lng = position.coords.longitude;
      });
    }
  }
};
</script>

<style>
</style>