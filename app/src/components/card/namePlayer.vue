<template>
  <v-card width="50vw" elevation="15" min-height="200px" class="mx-auto" style="backgroundColor: #212121" >
    <v-form ref="form" v-model="valid">
      <v-row class="mx-auto" justify="center">
        <v-col cols="12" sm="6" md="11" lg="12">
          <v-text-field label="Name" :rules="rules" v-model="user.name" clearable></v-text-field>
        </v-col>
        <v-col cols="12" sm="6" md="11" lg="12">
           <v-select label="Alignment" :items="alignment" v-model="user.alignment"></v-select>
        </v-col>
      </v-row>
      <v-row class="mx-auto">
        <v-col cols="12">
          <v-btn block class="deep-purple lighten-2 " dark :disabled="!valid" @click="getPlayer">Continue</v-btn>
        </v-col>
      </v-row>
    </v-form>
  </v-card>
</template>

<script>
export default {
  name: "namePlayer",
  data: () => ({
     alignment: ['neutral', 'bad', 'good'],
    user: {
      name: "",
      alignment: ''
    },
    valid: false,
    rules: [value => !!value || "Required."]
  }),
  methods: {
    getPlayer() {
      this.$emit("get-player", this.user);
    }
  }
};
</script>

<style scoped>
</style>