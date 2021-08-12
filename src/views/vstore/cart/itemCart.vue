<template>
  <v-layout row wrap style="border-bottom:1px #e0e0e0 solid">
    <v-flex xs4 sm4 md2 lg2 xl2 class="pa-2">
      <v-img contain :src="item.product.media[0].url" alt="product img"></v-img>
    </v-flex>
    <v-flex xs4 sm4 md8 lg8 xl8>
      <v-layout row wrap>
        <v-flex xs12 sm12 md4 lg4 xl4>
          <div style="display: flex;align-items: center">
            <div>
              <span style="font-size:0.7rem;color:grey;">{{item.product.category[0]}}</span>
              <br>
              <span style="font-weight:700;color:grey;">{{item.product.names[0].name.toUpperCase()}}</span>
            </div>
          </div>
        </v-flex>
        <v-flex xs12 sm12 md4 lg4 xl4>
          <div :class="$vuetify.breakpoint.smAndDown?'mobileInformation':'desktopInformation'">
            <span
              style="font-size:0.7rem;color:grey;"
            >{{$t('message.quantity').toUpperCase()}}:&nbsp;</span>
            <span style="font-weight:600;color:grey;">{{item.quantity}}</span>
          </div>
        </v-flex>
        <v-flex xs12 sm12 md4 lg4 xl4>
          <div :class="$vuetify.breakpoint.smAndDown?'mobileInformation':'desktopInformation'">
            <span style="font-size:0.7rem;color:grey;">{{$t('message.price').toUpperCase()}}:&nbsp;</span>
            <span style="font-weight:600;color:grey;">${{item.price}}</span>
          </div>
        </v-flex>
      </v-layout>
    </v-flex>
    <v-flex xs4 sm4 md2 lg2 xl2>
      <v-layout row wrap>
        <v-flex xs12 sm12 md6 lg6 xl6>
          <div :class="$vuetify.breakpoint.smAndDown?'mobileButtons':'desktopButtons'">
            <v-btn @click="showUpdateForm()" :color="currentBussinesClub.theme.primary" flat icon small>
              <v-icon :color="currentBussinesClub.theme.primary" small>mdi-pencil</v-icon>
            </v-btn>
          </div>
        </v-flex>
        <v-flex xs12 sm12 md6 lg6 xl6>
          <div :class="$vuetify.breakpoint.smAndDown?'mobileButtons':'desktopButtons'">
            <v-btn
              @click="removeDialog()"
              :color="currentBussinesClub.theme.secondary"
              flat
              icon
              small>
              <v-icon :color="currentBussinesClub.theme.secondary" small>mdi-window-close</v-icon>
            </v-btn>
          </div>
        </v-flex>
      </v-layout>
    </v-flex>
    <v-confirm-dialog
      v-bind:titleToolbar="'Eliminar item'"
      ref="removeDialog"
      type="question"
      heading="Eliminar"
      v-bind:message="'¿Está seguro que desea eliminar este producto del carrito?'"
      @onConfirm="confirmRemove()"
    ></v-confirm-dialog>
  </v-layout>
</template>
<script>
import { mapGetters } from "vuex";
import confirmDialog from "Components/ySocialHome/confirmDialog";
export default {
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  props: {
    item: { type: Object }
  },
  data() {
    return {};
  },
  components: {
    "v-confirm-dialog": confirmDialog
  },
  methods: {
    removeDialog() {
       this.$refs.removeDialog.openDialog();
    },
    confirmRemove() {
       this.$refs.removeDialog.close();
      this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.removingItemToCart")
      );
      this.$store
        .dispatch("removeItemToCart", this.item._id)
        .then(res => {
          this.$root.$children[0].$emit("hideLoading");
        })
        .catch(err => {
          this.$root.$children[0].$emit("hideLoading");
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.unknownError"),
            type: "error"
          });
        });
    },
    showUpdateForm()
    {
      this.$store.dispatch("setSelectedItem",this.item).then(res=>{
          this.$parent.$children[0].openForm();
      })
    }
  }
};
</script>
<style>
.mobileInformation {
  display: flex;
  align-items: center;
}
.desktopInformation {
  display: flex;
  align-items: center;
  justify-content: center;
}
.mobileButtons {
  display: flex;
  align-items: center;
  justify-content: flex-end;
}
.desktopButtons {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
