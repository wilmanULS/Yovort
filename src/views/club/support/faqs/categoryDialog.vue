<template>
  <v-dialog
    v-model="open"
    :fullscreen="$vuetify.breakpoint.xsOnly"
    :content-class="$vuetify.breakpoint.xsOnly?'':'dialog-dims'"
    persistent
  >
    <v-card>
      <v-card-actions class="dialog-title linear-gradient" primary-title>
        <v-card-title>{{$t('message.categories')}}</v-card-title>
        <v-spacer></v-spacer>
        <v-btn fab flat icon dark small @click="closeForm()" style="margin-right: 8px!important;">
          <v-icon>close</v-icon>
        </v-btn>
      </v-card-actions>
      <v-container grid-list-xs v-if="open">
        <v-layout row wrap>
          <v-flex xs12>
            <v-text-field
              :disabled="saving"
              @keyup.enter="createNewCategory()"
              v-model="newCategory"
              placeholder="Escriba el nombre de la categoría"
              hide-details
              :color="currentBussinesClub.theme.secondary">
              <template slot="append-outer">
                <v-btn
                  v-if="$vuetify.breakpoint.mdAndUp"
                  :disabled="saving||newCategory===''"
                  :loading="saving"
                  @click="createNewCategory()"
                  class="ma-0 pa-0 ml-1"
                  small
                  :color="currentBussinesClub.theme.secondary"
                >
                  <span style="color:white">{{$t('message.create')}}</span>
                </v-btn>
              </template>
            </v-text-field>
          </v-flex>
          <v-flex xs12 v-if="$vuetify.breakpoint.smAndDown">
            <v-btn
              :disabled="saving||newCategory===''"
              :loading="saving"
              @click="createNewCategory()"
              block
              :color="currentBussinesClub.theme.secondary"
            >
              <span style="color:white">{{$t('message.create')}}</span>
            </v-btn>
          </v-flex>
          <v-flex xs12 class="pt-4">
            <label>{{$t('message.categoryList')}}</label>
            <v-container v-if="categoriesFaq.length>0" grid-list-xs>
              <v-chip
                :disabled="saving"
                v-for="(category, i) in categoriesFaq"
                :key="i"
                text-color="white"
                :color="currentBussinesClub.theme.primary"
                close
                @input="onRemoveCategory(category)"
                slot="activator"
                small
              >
                <span class="mobileSpan">{{category.name}}</span>
              </v-chip>
            </v-container>
            <v-layout v-else align-center justify-center>
              <span>{{$t('message.noCategoryFaqsMessage')}}</span>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-container>
    </v-card>
  </v-dialog>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  computed: {
    ...mapGetters(["categoriesFaq", "currentBussinesClub"])
  },
  data() {
    return {
      open: false,
      newCategory: "",
      saving: false
    };
  },
  methods: {
    createNewCategory() {
      if (this.newCategory !== "") {
        var payload = {
          parent: this.currentBussinesClub.clubId,
          type: "faq",
          name: this.newCategory
        };
        this.saving = true;
        this.$store.dispatch("createCategory", payload).then(res => {
          this.saving = false;
          this.newCategory = "";
        });
      }
    },
    onRemoveCategory(item) {
      var payload = {
        type: "faq",
        category: item
      };
      this.$store.dispatch("deleteCategory", payload).then(response => {
        if (response.error === 401) {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.noDeleteErrorCategories"),
            type: "error"
          });
        }
      });
    },
    openForm() {
      var payload = {
          parent: this.currentBussinesClub.clubId,
          type: "faq",
        };
      this.$store.dispatch("getCategories",payload).then(res => {
        
        this.open = true;
        this.newCategory = "";
      });
    },
    closeForm() {
      this.open = false;
    }
  }
};
</script>
<style scoped>
.form-dialog {
  color: white;
  padding: 0px;
  background-color: #5d92f4;
}
</style>

