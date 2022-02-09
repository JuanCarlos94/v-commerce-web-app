<template>
  <v-app dark>

    <div>
      <v-app-bar>
      <v-app-bar-title>Vibbra Commerce</v-app-bar-title>
      <v-divider class="mx-4" vertical></v-divider>
      <v-spacer></v-spacer>
      <div v-if="!$auth.loggedIn">
        <v-btn text href="/">Login</v-btn>
        <v-divider vertical></v-divider>
        <v-btn text href="/cadastro">Cadastrar</v-btn>
      </div>
      <div v-else>
        <v-btn text href="/">Home</v-btn>
        <v-divider vertical></v-divider>
        <v-btn text href="/criar-negociacao">Nova negociação</v-btn>
        <v-divider vertical></v-divider>
        <v-btn text href="/minhas-negociacoes">Minhas negociações</v-btn>
        <v-divider vertical></v-divider>
        <v-btn text href="/meus-convites">Meus convites</v-btn>
        <v-divider vertical></v-divider>
        <v-btn text @click="logout">Logout</v-btn>
      </div>
    </v-app-bar>
    </div>

    <v-main>
      <v-container>
        <Nuxt />
        <SnackBar :message="msg"/>
      </v-container>
    </v-main>

    <v-footer app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {
      title: "Vibbra commerce",
      msg: ''
    };
  },
  methods: {
    async logout(){
      await this.$auth.logout();
      window.location.href = '/login';
    }
  },
};
</script>
