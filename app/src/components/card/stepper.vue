<template>
  <v-container fluid>
    <v-stepper v-model="e1" dark style="backgroundColor: #212121">
      <v-stepper-header>
        <v-stepper-step :complete="e1 > 1" step="1" :color="colorLabel">Set your name</v-stepper-step>

        <v-divider></v-divider>

        <v-stepper-step :complete="e1 > 2" step="2" :color="colorLabel">Choose your race</v-stepper-step>

        <v-divider></v-divider>

        <v-stepper-step :complete="e1 > 3" step="3" :color="colorLabel">Set attributes</v-stepper-step>

        <v-divider></v-divider>
        <v-stepper-step :complete="e1 > 4" step="4" :color="colorLabel">Card Preview</v-stepper-step>
      </v-stepper-header>

      <v-stepper-items style="padding: 10vh;">
        <v-stepper-content step="1" style="padding: 19vh">
          <namePlayer @get-player="getPlayer"></namePlayer>
        </v-stepper-content>

        <v-stepper-content step="2" width="10vw">
          <racesComponent @emit-click="getRace"></racesComponent>

          <v-btn
            color="deep-purple ligthen-2"
            style="margin: 40px"
            dark
            :disabled="!idRaceSelected"
            @click="e1 = 3"
          >Continue</v-btn>
          <v-btn :color="colorButton" text @click="e1 = 1">Voltar</v-btn>
        </v-stepper-content>

        <v-stepper-content step="3" style="padding: 10vh">
          <attributesComponent @emit-click-attribute="getAttributes"></attributesComponent>
          <v-btn color="deep-purple ligthen-2" style="margin: 5vh" @click="e1 = 2">Voltar</v-btn>
        </v-stepper-content>

        <v-stepper-content step="4" style="padding: 8vh">
          <v-card
            class="mx-auto mb-12"
            elevation="15"
            min-height="40vh"
            width="55vw"
            style="backgroundColor: #212121"
            dark
          >
            <v-row class="mx-auto">
              <v-col cols="12" sm="3" md="3" lg="3">
                <v-text-field label="Name" :value="nameToSave" readonly required></v-text-field>
              </v-col>
              <v-col cols="12" sm="3" md="3" lg="3">
                <v-text-field label="Alignment" :value="alignmentToSave" readonly required></v-text-field>
              </v-col>
              <v-col cols="12" sm="3" md="3" lg="3">
                <v-text-field label="Race" :value="raceSelected" readonly required></v-text-field>
              </v-col>

              <v-col cols="12" sm="3" md="3" lg="3">
                <v-text-field label="Charisma" :value="charismaToSave" readonly required></v-text-field>
              </v-col>

              <v-col cols="12" sm="3" md="3" lg="3">
                <v-text-field label="Intelligence" :value="intelligenceToSave" readonly required></v-text-field>
              </v-col>

              <v-col cols="12" sm="3" md="3" lg="3">
                <v-text-field label="Dexterity" :value="dexterityToSave" readonly required></v-text-field>
              </v-col>
              <v-col cols="12" sm="3" md="3" lg="3">
                <v-text-field label="Winsdow" :value="winsdowToSave" readonly required></v-text-field>
              </v-col>

              <v-col cols="12" sm="3" md="3" lg="3">
                <v-text-field label="Strength" :value="strengthToSave" readonly required></v-text-field>
              </v-col>
              <v-col cols="12" sm="3" md="4" lg="3">
                <v-text-field label="Constitution" :value="constitutionToSave" readonly required></v-text-field>
              </v-col>
              <v-col cols="12" sm="3" md="3" lg="3">
                <v-text-field label="HP" :value="hp" readonly required></v-text-field>
              </v-col>
            </v-row>
          </v-card>

          <template v-slot:loader>
            <span class="custom-loader">
              <v-icon light>mdi-cached</v-icon>
            </span>
          </template>
             <v-btn v-if="connected" color="deep-purple ligthen-2" dark @click="createCard">Save</v-btn>
             
          <v-btn :color="colorButton" text @click="e1 = 3">Voltar</v-btn>
        </v-stepper-content>
      </v-stepper-items>
    </v-stepper>
    <v-row>
      <v-col>
        <div class="text-center">
          <v-snackbar v-model="snackbar" class="white--text" :timeout="timeout">
            {{ message }}
            <v-btn dark text @click="snackbar = false" class="white--text">Close</v-btn>
          </v-snackbar>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>



<script>
import racesComponent from "./racesComponent";
import attributesComponent from "./attributesComponent";
import namePlayer from "./namePlayer";
import Axios from "axios";
export default {
  name: "stepper",
  data: () => ({
    e1: 0,
    ok: true,
    message: "",
    timeout: 2000,
    snackbar: false,
    loader: null,
    loading: false,
    colorButton: "deep-purple lighten-2 ",
    colorLabel: "deep-purple lighten-2 ",
    nameToSave: "",
    alignmentToSave: "",
    raceSelected: "",
    idRaceSelected: "",
    charismaToSave: "",
    intelligenceToSave: "",
    winsdowToSave: "",
    constitutionToSave: "",
    dexterityToSave: "",
    strengthToSave: "",
    kindToSave: "",
    hp: 0,
    valid: false,
    connected: false
  }),
  components: {
    racesComponent,
    attributesComponent,
    namePlayer
  },
  created() {
    this.$eventHub.$on("logged", this.logged);
    this.$eventHub.$on("logout", this.logout);
    if (localStorage.getItem("user_token")) {
      this.connected = true;
    }
  },
  beforeDestroy() {
    this.$eventHub.$off("logged");
  },
  methods: {
    logged() {
      this.connected = true;
    },
    logout() {
      this.connected = false;
      localStorage.clear();
      this.$router.push("/");
    },
    getPlayer(user) {
      this.nameToSave = user.name;
      this.alignmentToSave = user.alignment;
      this.e1 = 2;
    },
    getRace(race) {
      this.snackbar = true;
      this.raceSelected = race.races;
      this.idRaceSelected = race.idRace;
      this.message = race.races;
    },
    getAttributes(form) {
      this.e1 = 4;
      this.$emit("emit-attribute", form);
      this.charismaToSave = form.charisma;
      this.intelligenceToSave = form.intelligence;
      this.dexterityToSave = form.dexterity;
      this.winsdowToSave = form.winsdow;
      this.constitutionToSave = form.constitution;
      this.strengthToSave = form.strength;
      this.kindToSave = form.kind;

       if (this.raceSelected == 1) {
        this.dexterityToSave += 2;
      }
      if (this.kindToSave.idKind == 1) {
        this.hp = 6;
      }
       if (this.raceSelected == 3) {
        this.strengthToSave += 4;
      }
      if (this.kindToSave.idKind == 2) {
        this.hp = 8;
      }
      
      if (this.kindToSave.idKind == 3) {
        this.hp = 10;
      }
    },
    createCard() {
      this.loading = true;
      Axios.post("http://localhost:3000/v1/card/create", {
        name: this.nameToSave,
        alignment: this.alignmentToSave,
        idRace: this.idRaceSelected,
        idKind: this.kindToSave,
        charisma: this.charismaToSave,
        intelligence: this.intelligenceToSave,
        dexterity: this.dexterityToSave,
        winsdow: this.winsdowToSave,
        constitution: this.constitutionToSave,
        strength: this.strengthToSave,
        hp: this.hp
      })
        .catch(e => {
          this.snackbar = true;
          this.message = e.response.data;
        })
        .then(res => {
          this.snackbar = true;
          this.ok = false;
          this.message = res.data;
        })
        .finally(() => {
          this.loading = false;
        });
    }
  },
  watch: {
    loader() {
      const l = this.loader;
      this[l] = !this[l];

      setTimeout(() => (this[l] = false), 3000);

      this.loader = null;
    }
  }
};
</script>

<style scoped>
.custom-loader {
  animation: loader 1s infinite;
  display: flex;
}
@-moz-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-webkit-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-o-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>