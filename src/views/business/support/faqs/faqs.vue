<template>
  <div>
    <br>
    <v-autocomplete
      class="ma-0 pa-0 mb-3"
      :color="currentBussinesClub.theme.primary"
      prepend-inner-icon="mdi-database-search"
      v-model="searchField"
      :items="searchFaq"
      clearable
      hide-details
      hide-selected
      :label="$t('message.searchFaq')"
      solo
    ></v-autocomplete>

    <v-text-field
      :disabled="saving"
      @keyup.enter="createNewCategory()"
      v-model="newCategory"
      :label="$t('message.addCategoryFaq')"
      hide-details
      color="secondary"
      clearable
    ></v-text-field>
    <v-container fluid ma-0 pa-0 v-if="categoriesFaq.length>0" grid-list-xs>
      <v-chip
        :disabled="saving"
        outline
        v-for="(category, i) in categoriesFaq"
        :key="i"
        :color="currentBussinesClub.theme.primary"
        close
        @input="onRemoveCategory(category)"
        slot="activator"
        small
      >
        <span style="color:grey">{{category.name}}</span>
      </v-chip>
    </v-container>
    <v-layout v-else align-center justify-center>
      <span>{{$t('message.noCategoryFaqsMessage')}}</span>
    </v-layout>
    <br>
    <v-card flat class="ma-0 pa-0">
      <v-card-actions class="ma-0 pa-0">
        <span style="color:grey">{{$t('message.faqList')}}</span>
        <v-spacer></v-spacer>
        <v-btn
        v-if="!$vuetify.breakpoint.smAndDown"
          small
          @click="openFormCreateFaq()"
          dark
          :color="currentBussinesClub.theme.primary"
        >{{$t('message.addFaq')}}</v-btn>
        <v-btn 
        v-else
        small
          @click="openFormCreateFaq()"
          dark
          :color="currentBussinesClub.theme.primary">
          {{$t('message.createFaq')}}
        </v-btn>
      </v-card-actions>
    </v-card>

    <v-data-table
      :headers="headers"
      :items="faqList"
      :search="searchField"
      class="mi-dataTable elevation-0"
      :rows-per-page-text="$t('message.rowpage')"
    >
      <template v-slot:items="props">
        <td
          @click="previewFaq(props.item)"
          class="noselect"
          :title="getObjectPath(props.item,'question','')"
        >
          <div class="cut-text noselect">{{getObjectPath(props.item,'question','')}}</div>
        </td>
        <!-- <td class="noselect" v-html="getObjectPath(props.item,'answer','')"></td> -->
        <td
          @click="previewFaq(props.item)"
          class="noselect text-xs-center"
        >{{getObjectPath(props.item.categoryObject[0],'name',$t('message.unasigned'))}}</td>
        <td class="noselect text-xs-center">
          <v-btn
            @click="previewFaq(props.item)"
            small
            icon
            flat
            :color="currentBussinesClub.theme.primary"
          >
            <v-icon>mdi-eye</v-icon>
          </v-btn>
          <v-btn
            @click="openFormUpdateFaq(props.item)"
            small
            icon
            flat
            :color="currentBussinesClub.theme.primary"
          >
            <v-icon>mdi-pencil</v-icon>
          </v-btn>
          <v-btn
            @click="removeFaq(props.item)"
            small
            icon
            flat
            :color="currentBussinesClub.theme.primary"
          >
            <v-icon>mdi-trash-can</v-icon>
          </v-btn>
        </td>
      </template>
      <template
        slot="pageText"
        slot-scope="props"
      >{{ props.pageStart }}&nbsp;-&nbsp;{{ props.pageStop }}&nbsp;{{$t('message.of')}}&nbsp;{{ props.itemsLength }}</template>
    </v-data-table>
    <v-faq-form ref="faqForm"></v-faq-form>
    <v-form-dialog
      :hideButtons="true"
      :model="openPreview"
      @onClose="openPreview=false"
      :title="$t('message.preview')"
    >
      <span style="color:grey">{{$t('message.question')}}</span>
      <v-card-text style="color:grey">{{SelectedPreviewFaq.question}}</v-card-text>
      <v-divider></v-divider>
      <span style="color:grey">{{$t('message.answer')}}</span>
      <v-card-text style="color:grey" v-html="SelectedPreviewFaq.answer"></v-card-text>
    </v-form-dialog>
  </div>
</template>
<script>
import faqForm from "./faqForm";
import { mapGetters } from "vuex";
import Vue from "vue";
import { METHODS } from "http";
export default {
  mounted() {
    this.$store
      .dispatch("getCategories", { type: "faq", parent: this.businessId })
      .then(res => {
        if (this.categoriesFaq.length > 0) {
          this.open = true;
        } else {
/*           this.$store.dispatch("doNotification", {
            msg: this.$t("message.noCategoriesFaqError"),
            type: "error"
          }); */
        }
      });
    if (JSON.parse(this.userProfile)._id) {
      let payload = {
        userId: JSON.parse(this.userProfile)._id,
        parent: this.businessId
      };
      this.$store.dispatch("getFaqs", payload);
    }
  },
  computed: {
    ...mapGetters([
      "businessId",
      "faqList",
      "viewFaqs",
      "totalFaqs",
      "userProfile",
      "currentBussinesClub",
      "searchFaq",
      "categoriesFaq"
    ])
  },
  components: {
    "v-faq-form": faqForm
  },
  data() {
    return {
      openPreview: false,
      SelectedPreviewFaq: {},
      saving: false,
      newCategory: "",
      searchField: "",
      getObjectPath: Vue.getObjectPath,
      headers: [
        {
          text: "Pregunta",
          sortable: true,
          align: "left",
          value: "question",
          class: "questionClass",
          width: "5%"
        },
        /* {
          text: "Respuesta",
          sortable: false,
          align: "left",
          value: "answer",
          class:'answerClass'
        }, */
        {
          text: "Categoría",
          sortable: false,
          align: "center",
          value: "categoryObject[0].name"
        },
        {
          text: "Acciones",
          sortable: true,
          align: "center",
          value: "none"
        }
      ]
    };
  },
  methods: {
    openFormUpdateFaq(faq) {
      this.$store.dispatch("changeCurrentEditFaq", faq).then(response => {
        if (response) {
          this.$refs.faqForm.openForm();
        }
      });
    },
    openFormCreateFaq() {
      this.$store.dispatch("changeCurrentEditFaq", null).then(response => {
        if (response) {
          this.$refs.faqForm.openForm();
        }
      });
    },
    removeFaq(faq) {
      this.$store.dispatch("removeFaq", faq);
    },
    createNewCategory() {
      if (this.newCategory !== "") {
        var payload = {
          parent: this.businessId,
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
    previewFaq(faq) {
      this.SelectedPreviewFaq["question"] = faq.question;
      this.SelectedPreviewFaq["answer"] = faq.answer;
      this.openPreview = true;
    }
  }
};
</script>
<style>
.questionClass {
  max-width: 10vw !important;
}
.cut-text {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 30vw;
}
.noselect {
  cursor: pointer;
  -webkit-touch-callout: none; /* iOS Safari */
  -webkit-user-select: none; /* Safari */
  -khtml-user-select: none; /* Konqueror HTML */
  -moz-user-select: none; /* Firefox */
  -ms-user-select: none; /* Internet Explorer/Edge */
  user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome and Opera */
}
</style>
