<template>
  <v-container grid-list-md class="no-padding">
    <v-layout row wrap>
      <v-flex xs12>
        <vue-editor v-model="deliveryProduct"></vue-editor>
      </v-flex>
      <v-flex xs12>
        <vue-editor v-model="returnsProduct"></vue-editor>
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
      getObjectPath: Vue.getObjectPath,
      deliveryProduct: "",
      returnsProduct: ""
    };
  },
  computed: {
    ...mapGetters(["currentBussinesClub", "getSelectedProductInformation"])
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
      this.deliveryProduct = this.getObjectPath(
        this.getSelectedProductInformation,
        "shippingPolicy.delivery",
        ""
      );
      this.returnsProduct = this.getObjectPath(
        this.getSelectedProductInformation,
        "shippingPolicy.returns",
        ""
      );
    }
  }
};
</script>
