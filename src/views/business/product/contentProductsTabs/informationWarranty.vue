<template>
  <v-container grid-list-md>
    <v-layout row wrap>
      <v-flex xs12 sm6>
        <vue-editor v-model="politicsProduct"></vue-editor>
      </v-flex>
      <v-flex xs12 md6>
        <vue-editor v-model="TimeProduct"></vue-editor>
      </v-flex>
      <v-flex xs12>
        <vue-editor v-model="typeWarrantyProduct"></vue-editor>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import { VueEditor } from "vue2-editor";
export default {
  data() {
    return {
      politicsProduct: "",
      TimeProduct: "",
      typeWarrantyProduct: "",
      getObjectPath: Vue.getObjectPath
    };
  },
  computed: {
    ...mapGetters([
      "currentBussinesClub",
      "getSelectedProductInformation",
      "businessCategoriesList"
    ])
  },
  components: {
    "vue-editor": VueEditor
  },
  mounted() {
    this.$nextTick(() => {
      this.loadData();
    });
  },
  methods: {
    loadData() {
      this.politicsProduct = this.getObjectPath(
        this.getSelectedProductInformation,
        "warranty.policy",
        ""
      );
      this.TimeProduct = this.getObjectPath(
        this.getSelectedProductInformation,
        "warranty.time",
        ""
      );
      this.typeWarrantyProduct = this.getObjectPath(
        this.getSelectedProductInformation,
        "warranty.typeWarranty",
        ""
      );
    }
  }
};
</script>
<style scope>
.ql-editor {
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
    line-height: 1.42;
    height: 45% !important;
    outline: none;
    overflow-y: auto;
    padding: 12px 15px;
    -o-tab-size: 4;
    tab-size: 4;
    -moz-tab-size: 4;
    text-align: left;
    white-space: pre-wrap;
    word-wrap: break-word;
}
.ql-editor {
    min-height: 5vh!important;
}
</style>