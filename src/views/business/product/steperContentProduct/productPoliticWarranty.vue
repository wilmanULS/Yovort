<template>
  <div>
    <v-container grid-list-xl class="pl-1 pt-0">
      <v-form>
        <v-layout row wrap>
          <v-flex xs12 md12>
            <v-card-text class="pl-0 pb-1">
              <span class="textLabelEditors">{{$t('message.vProductPolitic')}}</span>
            </v-card-text>
            <vue-editor v-model="politicWarranty"></vue-editor>
          </v-flex>
          <v-flex xs12 md6>
            <v-card-text class="pl-0 pb-1">
              <span class="textLabelEditors">{{$t('message.vProductTime')}}</span>
            </v-card-text>
            <vue-editor v-model="timeWarranty"></vue-editor>
          </v-flex>
          <v-flex xs12 md6>
            <v-card-text class="pl-0 pb-1">
              <span class="textLabelEditors">{{$t('message.vProductType')}}</span>
            </v-card-text>
            <vue-editor v-model="typeWarranty"></vue-editor>
          </v-flex>
        </v-layout>
        <v-container pl-1>
          <v-layout mt-3>
            <v-btn
              :color="currentBussinesClub.theme.primary"
              dark
              @click="SavePoliticsWarranty()"
            >{{$t('message.vBusinessStepBusinessContinue')}}</v-btn>
            <v-btn
              @click="GoBack(3)"
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
import { VueEditor } from "vue2-editor";
export default {
 components: {
    "vue-editor": VueEditor
  },
  computed: {
    ...mapGetters(["userProfile", "currentBussinesClub", "userProducts"]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  data() {
    return {
      politicWarranty: "",
      timeWarranty: "",
      typeWarranty: ""
    };
  },
methods: {
    SavePoliticsWarranty() {
      let warrantyData = {
        productData: {
          productId: this.userProducts._id,
          warranty: {
            time: this.timeWarranty,
            policy: this.politicWarranty,
            typeWarranty: this.typeWarranty
          }
        },
        type: "Warranty"
      };
      this.$store.dispatch("doUpdateUserProducts", warrantyData).then(res => {
          this.$emit("onContinue");
        })      
  },
   GoBack(data) {
    this.$emit("onGoBack", data);
  }
}
}
</script>
<style>
</style>