<template>
  <div>
    <v-form-dialog
    v-if="selectedItem!=null"
      :model="open"
      ref="createForm"
      :title="$t('message.updateItem')"
      @onSave="updateItem()"
      @onClose="closeForm()"
    >
      <v-container>
        <v-layout row wrap>
          <v-flex xs12 sm12 md4 lg4 xl4>
            <v-card-title primary-title class="pl-0">
              <span
                class="ma-1 textColor detail-product-name"
              >{{getObjectPath(selectedItem.product.names[0], 'name','')}}</span>
            </v-card-title>
            <v-card-text class="pb-0 pl-0 pt-0">
              <span
                class="textColor price-product"
              >${{getObjectPath(selectedItem.product.price,'vMoney','')}}</span>
            </v-card-text>
          </v-flex>
          <v-flex xs12 sm12 md8 lg8 xl8>
            <v-layout row wrap align-center justify-center>
              <img :src="selectedItem.product.media[0].url" style="height:120px">
            </v-layout>
          </v-flex>
        </v-layout>
        <v-card-text class="pb-0 pl-0">
          <p
            class="description-product"
          >{{getObjectPath(selectedItem.product.names[0],'description.long','')}}</p>
        </v-card-text>
        <v-container class="pl-1 pt-0 pb-0" grid-list-md>
          <v-layout row wrap>
            <v-flex xs6 md3>
              <v-select class="wraperselect" hide-details attach outline label="Color"></v-select>
            </v-flex>
            <v-flex xs6 md3>
              <v-select class="wraperselect" hide-details attach outline label="Tamaño"></v-select>
            </v-flex>
          </v-layout>
        </v-container>
        <v-divider></v-divider>
        <v-layout row wrap>
          <v-flex xs3 md2>
            <span class="pt-1 textColor">{{$t("message.quantity")}}</span>
          </v-flex>
          <v-flex xs9 md3>
            <div class="quantity-toggle">
              <button @click="decrement()">&mdash;</button>
              <input type="text" :value="quantity" readonly>
              <button @click="increment()">&#xff0b;</button>
            </div>
          </v-flex>
          <v-text-field
          v-model="changesDetected"
          :rules="requiredField"
          style="display:none"
          ></v-text-field>
        </v-layout>
        <v-divider></v-divider>
      </v-container>
    </v-form-dialog>
  </div>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
export default {
  computed: {
    ...mapGetters(["currentBussinesClub", "selectedItem"])
  },
  data() {
    return {
      getObjectPath: Vue.getObjectPath,
      open: false,
      quantity: 1,
      changesDetected:"",
      startForm:false,
       requiredField: [v => !!v || "Campo requerido"],
    };
  },
  methods: {
    updateItem() {
              this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.savingInformation")
      );
        this.$store.dispatch('updateItem',this.quantity)
        .then(res=>{
            this.$root.$children[0].$emit("hideLoading");
            this.closeForm();
        }).catch(()=>{
            this.$store.dispatch("doNotification", {
            msg: this.$t("message.unknownError"),
            type: "error"
          });
          this.closeForm();
        })
    },
    increment() {
      this.quantity++;
    },
    decrement() {
      if (this.quantity === 1) {
      } else {
        this.quantity--;
      }
    },
    openForm() {
      this.quantity = this.selectedItem.quantity;
      this.open = true;
       this.$nextTick(() => { 
           this.startForm=true;
       })
     
    },
    closeForm() {
      this.open = false;
    }
  },
  watch:{
      quantity(val)
      {
          if(this.startForm)
          {
              this.changesDetected="ReadyUpdate";
          }
      }
  }
};
</script>
