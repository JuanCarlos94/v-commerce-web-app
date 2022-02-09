<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="4" md="4">
      <v-card>
        <v-card-title class="headline justify-center"> Login </v-card-title>
        <v-card-text>
          <v-container>
            <v-form v-model="valid">
              <v-row>
                <v-col cols="12" sm="12">
                  <v-text-field
                    label="Usuário"
                    v-model="login"
                    :rules="[rules.required]"
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" sm="12">
                  <v-text-field
                    v-model="password"
                    :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                    :rules="[rules.required, rules.passwordSize]"
                    :type="show1 ? 'text' : 'password'"
                    label="Password"
                    @click:append="show1 = !show1"
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-form>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" @click="authenticate" :disabled="!valid">
            Login
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data() {
    return {
      valid: false,
      show1: false,
      rules: {
        required: (value) => !!value || "Campo obrigatório.",
        passwordSize: (v) =>
          (v.length >= 6 && v.length <= 8) ||
          "Password deve ter entre 6 a 8 caractéres.",
      },
      password: "",
      login: "",
    };
  },
  methods: {
    async authenticate() {
      await this.$auth
        .loginWith("local", {
          data: { login: this.login, password: this.password },
        })
        .then(() => {
          window.location.href = "/";
        })
        .catch((err) => {
          alert('Credenciais inválidas');
        });
    }
  },
};
</script>

<style>
</style>