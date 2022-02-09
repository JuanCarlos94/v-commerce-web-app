<template>
  <v-snackbar :color="color" v-model="showMsg" :multi-line="multiLine">
    {{ msg }}
    <template v-slot:action="{ attrs }">
        <v-btn
          color="white"
          text
          v-bind="attrs"
          @click="hideMessage"
        >
          Fechar
        </v-btn>
      </template>
  </v-snackbar>
</template>

<script>
export default {
  data: () => ({
    showMsg: false,
    msg: "",
    multiLine: true,
    color: "primary"
  }),
  methods: {
    showMessage(msg, color) {
      this.showMsg = true;
      this.msg = msg;
      this.color = color;
    },
    hideMessage() {
      this.showMsg = false;
      this.msg = "";
      this.color = "primary";
    },
  },
};
</script>

<style>
</style>