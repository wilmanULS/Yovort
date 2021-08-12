<template>
  <div>
    <v-form-dialog
      v-if="type=='create'"
      :model="open"
      ref="createForm"
      :title="$t('message.createFaq')"
      showRequired
      @onSave="createFaq()"
      @onClose="closeForm()">
      <v-textarea
        v-model="newFaq.question"
        :label="$t('message.question')"
        :rules="requiredFieldElements"
        auto-grow
        rows="1"
        :color="currentBussinesClub.theme.primary"
      ></v-textarea>
      <v-textarea
        v-model="newFaq.answer"
        :label="$t('message.answer')"
        :rules="requiredFieldElements"
        auto-grow
        rows="1"
        :color="currentBussinesClub.theme.primary"
      ></v-textarea>
      <v-select
        :items="categoriesFaq"
        v-model="newFaq.category"
        :label="$t('message.category')"
        :rules="requiredFieldElements"
        item-text="name"
        item-value="_id"
        :color="currentBussinesClub.theme.primary"
      >
      </v-select>
    </v-form-dialog>
    <v-form-dialog
      ref="updateForm"
      v-if="type=='update'&&currentEditFaq!==null"
      :model="open"
      :title="$t('message.updateFaq')"
      showRequired
      @onSave="updateFaq()"
      @onClose="closeForm()"
      :color="currentBussinesClub.theme.primary"
    >
      <v-textarea
        v-model="currentEditFaq.question"
        :label="$t('message.question')"
        :rules="requiredFieldElements"
        auto-grow
        rows="1"
        :color="currentBussinesClub.theme.primary"
      ></v-textarea>
      <v-textarea
        v-model="currentEditFaq.answer"
        :label="$t('message.answer')"
        :rules="requiredFieldElements"
        auto-grow
        rows="1"
        :color="currentBussinesClub.theme.primary"
      ></v-textarea>
      <v-select
        :items="categoriesFaq"
        v-model="currentEditFaq.category"
        :label="$t('message.category')"
        :rules="requiredFieldElements"
        item-text="name"
        item-value="_id"
        :color="currentBussinesClub.theme.primary"
      ></v-select>
    </v-form-dialog>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  props: {
    type: String
  },
  computed: {
    ...mapGetters(["currentEditFaq", "categoriesFaq","currentBussinesClub"])
  },

  data() {
    return {
      open: false,
      newFaq: {
        question: "",
        answer: "",
        category: "",
        type: "club",
        parent: "5c81a44bf113aa231d35ea97"
      },
      requiredField: [v => !!v || "Campo requerido"],
      requiredFieldElements: [v => (v && v.length > 0) || "Campo requerido"]
    };
  },
  methods: {
    /* Form Options */
    openForm() {
      if (this.type === "create") {
        this.$refs.createForm.$refs.popupDialog.reset();
      }
      this.$store.
      dispatch("getCategories", { type: "faq", parent:  this.currentBussinesClub.clubId })
      .then(res => {
        if (this.categoriesFaq.length > 0) {
          this.open = true;
        } else {
          this.$store.dispatch("doNotification", {
              msg:this.$t("message.noCategoriesFaqError"),
              type: "error"
            });
        }
      });
    },
    closeForm() {
      this.open = false;
    },
    /* Create methods */
    createFaq() {
      this.$store.dispatch("createFaq", this.newFaq).then(res => {
        this.closeForm();
      });
    },
    updateFaq() {
      this.$store.dispatch("updateFaq", this.currentEditFaq).then(res => {
        this.closeForm();
      });
    }
  }
};
</script>
