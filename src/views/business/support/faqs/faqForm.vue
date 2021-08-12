<template>
  <div>
    <v-form-dialog
      v-if="isCreatingFaq"
      :model="open"
      ref="createForm"
      :title="$t('message.createFaq')"
      showRequired
      @onSave="createFaq()"
      @onClose="closeForm()">
      <v-select
        :items="categoriesFaq"
        v-model="newFaq.category"
        :label="$t('message.category')"
        :rules="requiredFieldElements"
        item-text="name"
        item-value="_id"
        :color="currentBussinesClub.theme.primary"
      ></v-select>
      <v-textarea
        v-model="newFaq.question"
        :label="$t('message.question')"
        :rules="requiredFieldElements"
        auto-grow
        rows="1"
        :color="currentBussinesClub.theme.primary"
      ></v-textarea>
      <span style="color:grey">{{$t('message.answer')}}</span>
     <vue-editor v-model="newFaq.answer" ></vue-editor>
      <v-text-field v-show="false" v-model="newFaq.answer" :rules="requiredField"></v-text-field>
    </v-form-dialog>
    <v-form-dialog
      ref="updateForm"
      v-if="!isCreatingFaq"
      :model="open"
      :title="$t('message.updateFaq')"
      showRequired
      @onSave="updateFaq()"
      @onClose="closeForm()"
      :color="currentBussinesClub.theme.primary"
    >
      <v-select
        :items="categoriesFaq"
        v-model="currentEditFaq.category"
        :label="$t('message.category')"
        :rules="requiredFieldElements"
        item-text="name"
        item-value="_id"
        :color="currentBussinesClub.theme.primary"
      ></v-select>
      <v-textarea
      v-if="open"
        v-model="currentEditFaq.question"
        :label="$t('message.question')"
        :rules="requiredFieldElements"
        auto-grow
        rows="1"
        :color="currentBussinesClub.theme.primary"
      ></v-textarea>
      <span style="color:grey">{{$t('message.answer')}}</span>
      <vue-editor v-model="currentEditFaq.answer" ></vue-editor>
      <v-text-field v-show="false" v-model="currentEditFaq.answer" :rules="requiredField"></v-text-field>
    </v-form-dialog>
  </div>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import { createEditor } from "vueditor";
import { VueEditor } from 'vue2-editor';
export default {
  components: {
      "vue-editor":VueEditor
   },
  props: {
    type: String
  },
  computed: {
    ...mapGetters([
      "isCreatingFaq",
      "currentEditFaq",
      "categoriesFaq",
      "currentBussinesClub",
      "businessId"
    ])
  },

  data() {
    return {
      modelEditor:'<h1>Some initial content</h1>',
      textAnswerEditor: null,
      configEditText: {
        toolbar: [
          "removeFormat",
          "undo",
          "|",
          "elements",
          "fontName",
          "fontSize",
          "foreColor",
          "backColor",
          "divider",
          "bold",
          "italic",
          "underline",
          "links",
          "divider",
          "justifyLeft",
          "justifyCenter",
          "justifyRight",
          "justifyFull",
          "|",
          "indent",
          "outdent",
          "insertOrderedList",
          "insertUnorderedList"
        ],
        fontName: [
          {
            val: "arial black"
          },
          {
            val: "times new roman"
          },
          {
            val: "Courier New"
          }
        ],
        fontSize: [
          "12px",
          "14px",
          "16px",
          "18px",
          "20px",
          "24px",
          "28px",
          "32px",
          "36px"
        ],
        uploadUrl: "",
        classList: ["color:blue"]
      },
      types: [
        {
          value: "club",
          name: "Club"
        },
        {
          value: "bussines",
          name: "Bussines"
        }
      ],
      open: false,
      answer: "",
      newFaq: {
        question: "",
        answer: "",
        category: "",
        type: "bussines",
        parent: ""
      },
      requiredField: [v => !!v || "Campo requerido"],
      requiredFieldElements: [v => (v && v.length > 0) || "Campo requerido"]
    };
  },
  methods: {
    /* Form Options */
    openForm() {
      if (this.isCreatingFaq) {
        this.newFaq.parent = this.businessId;
        this.$refs.createForm.$refs.popupDialog.reset();
      }
      this.$store
        .dispatch("getCategories", { type: "faq", parent: this.businessId })
        .then(res => {
          if (this.categoriesFaq.length > 0) {
            this.open = true;
          } else {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.noCategoriesFaqError"),
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
<style>
.ql-editor{
  min-height: 20vh!important;
}
.ql-container{
  min-height: 20vh;
}

</style>
