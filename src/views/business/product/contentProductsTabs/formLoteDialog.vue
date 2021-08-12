<template>
  <v-popup-dialog
    :model="open"
    ref="loteDataDialog"
    :title="'Ingrese los datos del lote'"
    @onClose="closeDialog()"
  >
    <v-container>

                <v-tabs color="white" v-model="tabs" :show-arrows="$vuetify.breakpoint.smAndDown">
            <v-tabs-slider :color="currentBussinesClub.theme.primary"></v-tabs-slider>
            <v-tab>Información de lote</v-tab>
            <v-tab>Información de variante</v-tab>
            <v-tabs-items v-model="tabs">
              <v-tab-item :transition="false" :reverse-transition="false">  
                  <v-info-lote></v-info-lote>
              </v-tab-item>
              <v-tab-item :transition="false" :reverse-transition="false">  
                  <v-info-variant></v-info-variant>  
                <!--  <v-store-panel-customization :item="storeData"></v-store-panel-customization> -->
              </v-tab-item>        
            </v-tabs-items>
          </v-tabs>
      
    </v-container>
  </v-popup-dialog>
</template>
<script>
import popupDialog from "../../../../components/PopupDialog/PopupDialog";
import informationLote from "./informationLote"
import informationVariant from "./informationVariant"
import Vue from "vue";
import { mapGetters } from "vuex";
import VueQRCodeComponent from 'vue-qrcode-component'
Vue.component('qr-code', VueQRCodeComponent)
export default {
  data() {
    return {
      open: false,
      tabs:0
    };
  },
      computed: {
    ...mapGetters([
      "currentBussinesClub",
      "selectedLocale"
    ]),
  },
  components: {
    "v-popup-dialog": popupDialog,
    "v-info-lote":informationLote,
    "v-info-variant":informationVariant
  },
  methods: {
      openDialog(){    
          this.open=true;
      },
      closeDialog(){
          this.open=false;
      },
         save(date) {
            this.$refs.birthdateMenu.save(date);
           
        },
  }
};
</script>
