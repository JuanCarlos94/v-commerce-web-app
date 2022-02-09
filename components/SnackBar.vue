<template>
  <v-snackbar v-model="show">
    {{ message }}

    <template v-slot:action="{ attrs }">
      <v-btn color="pink" text v-bind="attrs" @click="show = false">
        Close
      </v-btn>
    </template>
  </v-snackbar>
</template>

<script>
export default {
  props: {
    message: null,
  },
  data() {
    return {
      show: false,
    };
  },
};
</script>