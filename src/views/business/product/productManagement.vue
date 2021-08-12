<template>
  <v-dialog v-model="openProductManagement" fullscreen persistent lazy>
    <v-card>
      <v-card-actions class="dialog-title linear-gradient" card dark>
        <v-card-title
          style="padding-left: 0!important;margin-left: 24px!important;"
        >{{$t('message.vproductManagement')}}</v-card-title>
        <v-spacer></v-spacer>
        <v-btn
          fab
          flat
          icon
          dark
          small
          style="margin-right: 24px!important;"
          @click="openConfirmDialog"
        >
          <v-icon>close</v-icon>
        </v-btn>
      </v-card-actions>
      <v-container class="no-padding">
        <v-tabs color="white" v-model="tabs" :show-arrows="$vuetify.breakpoint.smAndDown">
          <v-tabs-slider :color="currentBussinesClub.theme.primary"></v-tabs-slider>
          <v-tab>{{$t("message.vProductInformation")}}</v-tab>
          <v-tab>{{$t("message.vProductDataLote")}}</v-tab>
          <v-tab>{{$t("message.vProductPoliticsWarranty")}}</v-tab>
          <v-tab>{{$t("message.vProductPoliticsDelivery")}}</v-tab>
          <v-tabs-items v-model="tabs">
            <v-tab-item lazy :transition="false" :reverse-transition="false">
              <v-info-product></v-info-product>
            </v-tab-item>
            <v-tab-item :transition="false" :reverse-transition="false">
              <v-product-lote></v-product-lote>
              <!--  <v-store-panel-customization :item="storeData"></v-store-panel-customization> -->
            </v-tab-item>
            <v-tab-item :transition="false" :reverse-transition="false">
              <v-info-warranty></v-info-warranty>
            </v-tab-item>
            <v-tab-item :transition="false" :reverse-transition="false">
              <v-info-delivery></v-info-delivery>
              <!--    <v-store-panel-banner :item="storeData"></v-store-panel-banner> -->
            </v-tab-item>
          </v-tabs-items>
        </v-tabs>
      </v-container>
    </v-card>
    <v-confirm-dialog
      ref="dialogConfirm"
      type="question"
      heading="Salir del asistente"
      v-bind:message="'¿Está seguro de salir de la administración del producto?'"
      @onConfirm="ConfirmCloseDialog()"
    ></v-confirm-dialog>
  </v-dialog>
</template>
<script>
import { mapGetters } from "vuex";
import confirmDialog from "../../../components/ySocialHome/confirmDialog";
import productLote from "./contentProductsTabs/productLote";
import informationDelivery from "./contentProductsTabs/informationDelivery";
import informationProduct from "./contentProductsTabs/informationProduct";
import informationWarranty from "./contentProductsTabs/informationWarranty";
export default {
  props: {
    itemProduct: Object
  },
  data() {
    return {
      openProductManagement: false,
      tabs: 0
    };
  },
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  components: {
    "v-confirm-dialog": confirmDialog,
    "v-product-lote": productLote,
    "v-info-product": informationProduct,
    "v-info-delivery": informationDelivery,
    "v-info-warranty": informationWarranty
  },
  created(){},
  methods: {
    openDialog() {
      this.openProductManagement = true;
    },
    openConfirmDialog() {
      console.log(this.$refs.dialogConfirm);
      this.$refs.dialogConfirm.openDialog();
    },
    ConfirmCloseDialog() {
      this.$refs.dialogConfirm.close();
      this.openProductManagement = false;
      this.$emit("onClose");
    }
  }
};
</script>
