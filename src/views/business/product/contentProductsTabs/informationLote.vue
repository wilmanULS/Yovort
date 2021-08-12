<template>
<v-container grid-list-md class="no-padding">
    <v-layout row wrap>
          <v-flex xs12 md6>
          <v-text-field  :color="currentBussinesClub.theme.primary" label="SKU"></v-text-field>
        </v-flex>
        <v-flex xs12 md6>
          <v-menu
            class="pt-0 pb-0"
            ref="dateExpiration"
            v-model="dateExpirationMenu"
            :close-on-content-click="false"
            :nudge-right="40"
            :return-value.sync="dateExpiration"
            lazy
            transition="scale-transition"
            offset-y
            full-width
            min-width="290px"
          >
            <template v-slot:activator="{ on }">
              <v-text-field
                :class="{'mr05': $vuetify.breakpoint.mdAndUp , 'mb-1':$vuetify.breakpoint.smAndDown }"
                v-model="dateExpiration"
                ref="dateExpiration"
                readonly
                v-on="on"
                :color="currentBussinesClub.theme.primary"
                v-bind:label="'Fecha de expedición'"
                append-icon="event"
                hide-details    
                @change="save"
              ></v-text-field>
            </template>
            <v-date-picker
              v-model="dateExpiration"
              ref="dateExpirationPicker"
              :color="currentBussinesClub.theme.primary"
              :locale="selectedLocale.locale"
            ></v-date-picker>
          </v-menu>
        </v-flex>
        <v-flex xs12 md6>
          <v-text-field :color="currentBussinesClub.theme.primary" name="name" label="Precio de compra"></v-text-field>
        </v-flex>
        <v-flex xs12 md6>
          <v-text-field :color="currentBussinesClub.theme.primary" name="name" label="Elemento multimedia" id="id"></v-text-field>
        </v-flex>
        <v-flex xs12 md6>
          <v-text-field v-model="barcodeValue" :color="currentBussinesClub.theme.primary"  name="name" label="Código de barra" id="id"></v-text-field>
            <barcode v-if="barcodeValue!=''" v-bind:value="barcodeValue"></barcode>
        </v-flex>
        <v-flex xs12 md6>
          <v-text-field v-model="qrCode" :color="currentBussinesClub.theme.primary" name="name" label="Código de QR" id="id"></v-text-field>
          <qr-code v-if="qrCode!=''" size=64 :text="qrCode"></qr-code>
        </v-flex>
        <v-flex xs12 md6></v-flex>    
    </v-layout>
</v-container>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import VueBarcode from 'vue-barcode';
export default {
    data(){
        return{
    dateExpiration:null,
      dateExpirationMenu:false,
      qrCode:"",
      barcodeValue:""
        }
    },
    computed: {
    ...mapGetters([
      "currentBussinesClub",
      "selectedLocale"
    ]),
  }, 
  components:{
       'barcode': VueBarcode
  },
  methods: {
    save(){}
  },
}
</script>