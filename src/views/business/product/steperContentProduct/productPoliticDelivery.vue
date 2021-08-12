<template>
  <div>
    <v-container grid-list-xl class="pl-1 pt-0">
      <v-form>
        <v-layout row wrap>
          <v-flex xs12 md6>
            <v-card-text class="pl-0 pb-1">
              <span class="textLabelEditors">{{$t('message.vProductDeliver')}}</span>
            </v-card-text>
           <vue-editor v-model="productDelivery"></vue-editor>
          </v-flex>
          <v-flex xs12 md6>
            <v-card-text class="pl-0 pb-1">
              <span class="textLabelEditors">{{$t('message.vProductRepayment')}}</span>
            </v-card-text>
           <vue-editor v-model="productRepayment"></vue-editor>
          </v-flex>
        </v-layout>
        <v-container pl-1>
          <v-layout mt-3>
            <v-btn
              :color="currentBussinesClub.theme.primary"
              dark
              @click="SavePoliticsDelivery()"
            >{{$t('message.vBusinessStepBusinessFinish')}}</v-btn>
            <v-btn
              @click="GoBack(4)"
              flat
              :color="currentBussinesClub.theme.secondary"
            >{{$t('message.vBusinessStepBusinessBack')}}</v-btn>
          </v-layout>
        </v-container>
      </v-form>
    </v-container>
  </div>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import { VueEditor } from 'vue2-editor';
export default {
   components: {
      "vue-editor":VueEditor
   },
  computed: {
    ...mapGetters(["userProfile", "currentBussinesClub","userProducts"]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  data() {
    return {
      productDelivery:"",
      productRepayment:""

    };
  },
  mounted(){
  },
  methods: {
    SavePoliticsDelivery() {

      let dataDelivery={
        productData: {
            productId: this.userProducts._id,
            shippingPolicy: {
            delivery: this.productDelivery,
            returns: this.productRepayment
    		  }
          },
          type:"Shipping"
      }
     this.$store.dispatch("doUpdateUserProducts",dataDelivery).then(res=>{
       this.$emit("onContinue");
        this.$store.dispatch("doNotification", {
                  msg: this.$t("message.vProductCreateSucces"),
                  type: "success"
                });
     }).finally(()=>{
     
     })
    },
    GoBack(data) {
      this.$emit("onGoBack", data);
    }
  }
};
</script>
<style>
</style>