<template>
  <div>
    <v-toolbar class="Vuely-toolbar" color="transparent">
      <div>
        <v-btn
          @click="openCreate()"
          slot="activator"
          :color="currentBussinesClub.theme.primary"
          id="v-step-0"
        >
          <span style="color:white">{{$t('message.createFaq')}}</span>
        </v-btn>
      </div>
      <v-container ma-0 py-0 pr-0 grid-list-xs></v-container>
      <div>
        <v-list-tile>
          <v-btn
            id="v-step-2"
            @click="changeView()"
            v-if="viewFaqs=='unPinneds'"
            :color="currentBussinesClub.theme.primary"
            flat
            icon
          >
            <v-icon>mdi-pin-off</v-icon>
          </v-btn>
          <v-btn
            id="v-step-3"
            @click="changeView()"
            v-else
            :color="currentBussinesClub.theme.secondary"
            flat
            icon
          >
            <v-icon>mdi-pin</v-icon>
          </v-btn>
          <v-tooltip top>
            <v-btn @click="openCategories();" flat icon id="v-step-3" slot="activator">
              <v-icon :color="currentBussinesClub.theme.primary">mdi-format-list-bulleted-type</v-icon>
            </v-btn>
            <span>{{$t('message.categories')}}</span>
          </v-tooltip>
        </v-list-tile>
      </div>
    </v-toolbar>
    <v-faq-form ref="faqFormCreate" type="create"></v-faq-form>
    <v-faq-form ref="faqFormUpdate" type="update"></v-faq-form>
    <v-category ref="categoryDialog"></v-category>
    <v-card color="white">
      <vue-perfect-scrollbar v-if="faqList.length>0" style="height:50vh" :settings="settings">
        <Waterfall px-0 align="center" v-if="faqList">
          <WaterfallItem :width="355" v-for="(faq,i) in faqList" :key="i">
            <v-faq :faq="faq"></v-faq>
          </WaterfallItem>
          <WaterfallItem :width="355"></WaterfallItem>
        </Waterfall>
      </vue-perfect-scrollbar>
      <v-layout style=";height:calc(100vh - 180px )" v-else align-center justify-center>
        <span v-if="totalFaqs==0">{{$t('message.noFaqsMessage')}}</span>
        <span v-if="faqList.length==0&&totalFaqs>0">{{$t('message.noCheckFaqPriority')}}</span>
      </v-layout>
    </v-card>
  </div>
</template>
<script>
import faqCard from "./faqCard";
import faqForm from "./faqForm";
import categoryFaq from "./categoryDialog";
import { Waterfall, WaterfallItem } from "vue2-waterfall";
import { mapGetters } from "vuex";
export default {
  mounted() {
    if (JSON.parse(this.userProfile)._id) {
      let payload={
        userId:JSON.parse(this.userProfile)._id,
        parent:this.currentBussinesClub.clubId
      }
      this.$store.dispatch("getFaqs", payload);
    }
  },
  computed: {
    ...mapGetters([
      "faqList",
      "viewFaqs",
      "totalFaqs",
      "userProfile",
      "currentBussinesClub",
    ])
  },
  data() {
    return {
      faq: {
        answer: "the answer is in your heart",
        removing: false,
        viewCount: 4000,
        category: "Testing",
        pinned: true,
        selected: false,
        question:
          "Question is defined as a large text from people whitout know of programation as like us"
      },
      settings: {
        maxScrollbarLength: 160
      },
      view: false
    };
  },
  methods: {
    openCreate() {
      this.$refs.faqFormCreate.openForm();
    },
    openCategories() {
      this.$refs.categoryDialog.openForm();
    },
    changeView() {
      this.$store.dispatch(
        "changeFaqListView",
        JSON.parse(this.userProfile)._id
      );
    }
  },
  components: {
    "v-faq": faqCard,
    "v-faq-form": faqForm,
    "v-category": categoryFaq,
    Waterfall,
    WaterfallItem
  }
};
</script>