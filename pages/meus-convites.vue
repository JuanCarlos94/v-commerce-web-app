<template>
  <div>
    <v-card max-width="600" class="mx-auto">
      <v-card-title
        >Meus convites <v-spacer></v-spacer
        ><v-btn class="primary" @click="dialog = true"
          >Novo</v-btn
        ></v-card-title
      >
      <v-container>
        <v-row dense>
          <v-col cols="12">
            <v-card class="mb-2" :color="invite.user_invited ? 'success' : 'primary'" dark v-for="(invite, i) in invites" :key="i">
              <v-card-title class="text-h5"> Convite enviado para {{invite.name}} pelo e-mail
                <b>{{invite.email}}</b></v-card-title>
              <v-card-subtitle>Convite Aceito</v-card-subtitle>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-card>
    <v-dialog v-model="dialog" persistent max-width="600">
      <v-card>
        <v-card-title>Enviar oferta</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="12" md="12">
                <v-text-field v-model="invite.name" :rules="nameRules" label="Nome" required></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="12" md="12">
                <v-text-field
                  v-model="invite.email"
                  :rules="emailRules"
                  label="E-mail"
                  required
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog = false"
            >Cancelar</v-btn
          >
          <v-btn color="blue darken-1" text @click="saveInvite">Enviar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  name: 'MyBids',
  middleware: 'auth',
  data: () => ({
    dialog: false,
    invites: [],
    invite: {
      name: "",
      email: "",
      user: "",
      user_invited: null
    },
    emailRules: [
      (v) => !!v || "E-mail é obrigatório",
      (v) => /.+@.+\..+/.test(v) || "E-mail deve ser válido",
    ],
    nameRules: [
      (v) => !!v || "Nome é obrigatório"
    ]
  }),
  methods: {
    loadInvites(){
      this.$axios.get('/user/' + this.$auth.user._id + '/invite')
        .then(res => {
          this.invites = res.data;
        })
        .catch(err => {
          console.log(err);
          alert('Erro ao carregar convites, tente novamente.')
        })
    },
    saveInvite(){
      this.invite.user = this.$auth.user._id;
      this.$axios.post('/user/' + this.$auth.user._id + '/invite', this.invite)
        .then(res => {
          this.invites.push(res.data);
          window.location.reload();
        })
        .catch(err => {
          console.log(err);
          alert('Erro ao salvar convite, tente novamente.')
        })
    }
  },
  mounted(){
    this.loadInvites();
  }
};
</script>

<style>
</style>