<template>
  <v-container grid-list-md v-if="getSelectedProductInformation!=null">
    <v-layout row wrap>
      <v-flex xs12>
        <strong style="color:black" class="pt-2">Datos iniciales</strong>
      </v-flex>
      <v-divider></v-divider>
      <v-flex xs12 sm6 mt-1>
        <v-text-field
          v-model="nameProduct"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vProductInformationName')"
          clearable
          required
          hide-details
        ></v-text-field>
      </v-flex>
      <v-flex xs12 sm6 mt-1>
        <v-text-field
          v-model="skuProduct"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vProductSku')"
          clearable
          required
          hide-details
        ></v-text-field>
      </v-flex>
      <v-flex xs12 sm6 mt-1>
        <vue-editor v-model="descriptionProduct.short"></vue-editor>
      </v-flex>
      <v-flex xs12 sm6 mt-1>
        <vue-editor v-model="descriptionProduct.long"></vue-editor>
      </v-flex>
      <v-flex xs12 mt-1>
        <vue-editor v-model="descriptionProduct.long"></vue-editor>
      </v-flex>
      <v-flex xs12>
        <strong style="color:black" class="pt-2">Detalles del producto</strong>
      </v-flex>
      
      <v-flex xs12 sm6 mt-1>
        <v-text-field
          v-model="modelProduct"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vProductModel')"
          clearable
          required
          hide-details
        ></v-text-field>
      </v-flex>
      <v-flex xs12 sm6 mt-1>
        <v-text-field
          v-model="brandProduct"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vProductMark')"
          clearable
          required
          hide-details
        ></v-text-field>
      </v-flex>
      <v-flex xs12 sm6 mt-1>
        <v-text-field
          v-model="pricesProduct.vCredit"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vProductCredit')"
          clearable
          required
          hide-details
        ></v-text-field>
        <v-text-field
          v-model="pricesProduct.vMoney"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vProductMoney')"
          clearable
          required
          hide-details
        ></v-text-field>
        <v-text-field
          v-model="pricesProduct.vPoint"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vProductPoints')"
          clearable
          required
          hide-details
        ></v-text-field>
      </v-flex>
      <v-flex xs12 sm6 mt-1>
        <v-select
          v-model="categoriesSelected"
          :items="firstCategory"
          item-text="name"
          item-value="code"
          :label="$t('message.vBusinessStepAdditionalInfoCategories')"
          :color="currentBussinesClub.theme.primary"
          :no-data-text="$t('message.nodatafound')"
        ></v-select>
      </v-flex>
    </v-layout>
    <!-- 
    <v-dialog v-model="dialog" hide-overlay persistent width="300">
      <v-card color="primary" dark>
        <v-card-text>
          Please stand by
          <v-progress-linear indeterminate color="white" class="mb-0"></v-progress-linear>
        </v-card-text>
      </v-card>
    </v-dialog>-->
  </v-container>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import { VueEditor } from "vue2-editor";
export default {
  data() {
    return {
      getObjectPath: Vue.getObjectPath,
      dialog: false,
      informationProduct: null,
      nameProduct: "",
      skuProduct: "",
      descriptionProduct: {
        short: "",
        long: "",
        detail: ""
      },
      categoriesSelected: "",
      firstCategory: [],
      modelProduct: "",
      brandProduct: "",
      pricesProduct: {}
    };
  },
  computed: {
    ...mapGetters([
      "currentBussinesClub",
      "getSelectedProductInformation",
      "businessCategoriesList"
    ])
    /* getSelectedProductInformation: {
      get() {
        if (this.$store.getters.getSelectedProductInformation != null)
          return this.$store.getters.getSelectedProductInformation;
      }
    } */
  },

  components: {
    "vue-editor": VueEditor
  },
  created: {},
  mounted() {
    this.$nextTick(() => {
      this.loadData();
    });
  },
  methods: {
    loadData() {
      this.nameProduct = this.getObjectPath(
        this.getSelectedProductInformation.names[0],
        "name",
        ""
      );
      this.skuProduct = this.getObjectPath(
        this.getSelectedProductInformation,
        "sku",
        ""
      );
      this.descriptionProduct.short = this.getObjectPath(
        this.getSelectedProductInformation.names[0],
        "description.short",
        ""
      );
      this.descriptionProduct.long = this.getObjectPath(
        this.getSelectedProductInformation.names[0],
        "description.long",
        ""
      );
      this.descriptionProduct.detail = this.getObjectPath(
        this.getSelectedProductInformation.names[0],
        "description.detail",
        ""
      );
      this.pricesProduct = this.getObjectPath(
        this.getSelectedProductInformation,
        "price",
        ""
      );
      this.modelProduct = this.getObjectPath(
        this.getSelectedProductInformation,
        "model",
        ""
      );
      this.brandProduct = this.getObjectPath(
        this.getSelectedProductInformation,
        "brand",
        ""
      );

    //  console.log(this.getSelectedProductInformation.category);
     //this.businessCategoriesList
    }
  }
};
</script>
