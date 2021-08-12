<template>
  <v-container grid-list-md class="no-padding">
    <v-layout row wrap>
      <v-flex xs12 md4 lg4 xl4 class="pt-2">
        <v-btn small :color="currentBussinesClub.theme.primary" dark @click>
          <v-icon>mdi mdi-plus</v-icon> añadir variante
        </v-btn>
      </v-flex>
      <v-flex xs12>
        <v-layout row wrap>
          <v-flex xs12 md6>
          <v-text-field
            :color="currentBussinesClub.theme.primary"
            label="Nombre"
          ></v-text-field>
          </v-flex>
           <v-flex xs12 md6>
             <v-text-field
              :color="currentBussinesClub.theme.primary"
               label="Valor"
             ></v-text-field>
             <v-card flat>
               
             </v-card>
          </v-flex>
        </v-layout>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
export default {
  data() {
    return {
      itemsVariant:["Color","Tamaño"],
      variant:null
    };
  },
  computed: {
    ...mapGetters(["currentBussinesClub"])
  }
};
</script>