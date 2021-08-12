<template>
  <v-container v-if="mountedReady" class="ma-0 pa-0">
    <v-layout row wrap ref="containerHeighttAll">
      <v-flex sm4 md4 lg4 xs12>
        <v-card flat class="border-1px bg-color-grey">
          <v-card-title primary-title class="ma-0 pa-0">
            <span
              class="titleDivider noselect"
              v-bind:style="{'color':currentBussinesClub.theme.primary}"
            >&nbsp;{{$t('message.categories')}}</span>
          </v-card-title>

          <ul style="width:100%;">
            <li style="position:relative;">
              <a
                v-on:click="getAllFaqs()"
                class="btn-options"
                :style="categorySelect == -1?'color:'+currentBussinesClub.theme.secondary:''"
              >&nbsp;&nbsp;{{$t('message.all')}}</a>
              <v-badge
                style="position:absolute;right:30px;top:20px;"
                :color="currentBussinesClub.theme.secondary"
              >
                <small slot="badge" v-if="totalFaqsUser < 99">{{totalFaqsUser }}</small>
                <small slot="badge" v-else>+99</small>
              </v-badge>
            </li>
            <li
              v-if="Category.faq.length>0"
              v-for="(Category, indexCategory) in categoriesFaqs"
              :key="indexCategory"
              style="position:relative;"
            >
              <a
                v-on:click="CategoryFaq(Category,indexCategory)"
                class="btn-options"
                :style="categorySelect ==indexCategory?'color:'+currentBussinesClub.theme.secondary:''"
              >&nbsp;&nbsp;{{Category.name}}</a>
              <v-badge
                style="position:absolute;right:30px;top:20px;"
                :color="currentBussinesClub.theme.secondary"
              >
                <small slot="badge">{{Category.faq.length}}</small>
              </v-badge>
            </li>
          </ul>
        </v-card>
      </v-flex>
      <v-fade-transition>
        <v-flex xs12 sm8 md8 lg8 v-if="!loadingFaqs">
          <v-expansion-panel popout>
            <v-expansion-panel-content
              class="border-1px bg-color-grey"
              style="border-top:1px #cfd8dc solid !important"
              ref="xpand"
              v-for="(contentFaqs, intemp) in faqListUser"
              :key="intemp"
              :expand="panel"
              @input="incrementViewCount(contentFaqs._id,intemp)"
            >
              <v-flex
                slot="header"
                :class="panel[intemp]?'':'cut-text'"
                :style="'color:'+currentBussinesClub.theme.primary"
              >{{ contentFaqs.question}}</v-flex>
              <v-card>
                <v-card-text style="color:#2c3e52">{{contentFaqs.answer}}</v-card-text>
              </v-card>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-flex>
      </v-fade-transition>
    </v-layout>
  </v-container>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  data() {
    return {
      mountedReady: false,
      panel: ["true"],
      rating: 0,
      categorySelect: -1
    };
  },
  computed: {
    ...mapGetters([
      "faqListUser",
      "categoriesFaqs",
      "loadingFaqs",
      "userProfile",
      "currentBussinesClub",
      "totalFaqsUser"
    ])
  },
  mounted() {
    this.$store
      .dispatch("getCategoriesUser", {
        type: "faq",
        parent: this.currentBussinesClub.clubId
      })
      .then(res => {
        this.$store
          .dispatch("getFaqsUser", this.currentBussinesClub.clubId)
          .then(res => {
            this.mountedReady = true;
          });
      });
  },
  methods: {
    incrementViewCount(faqId, index) {
      if (this.$refs.xpand[index].isActive) {
        let payload = {
          faqId: faqId,
          type: "club"
        };
        this.$store.dispatch("incrementFaqViews", payload).then(res => {});
      }
    },
    getAllFaqs() {
      this.categorySelect = -1;
      this.$store
        .dispatch("getFaqsUser", this.currentBussinesClub.clubId)
        .then(res => {});
    },
    CategoryFaq(faqCategoryList, index) {
      this.$store.commit("setFaqListUser", faqCategoryList.faq);
      this.categorySelect = index;
    },
    qualify(faq) {
      var payload = {
        faqId: faq._id,
        userId: faq.rating[0].userId,
        value: faq.rating[0].value
      };
      this.$store.dispatch("qualify", payload).then(res => {
        faq.rating[0].rated = true;
      });
    }
  }
};
</script>
<style scoped>
.cut-text {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.redirectImg {
  cursor: pointer;
}
.containerMain {
  max-width: 1000px !important;
}
.btn-options {
  position: relative;
  width: 90%;
  margin: 8px 0px 8px 0px;
  font-size: 12pt;
  color: #303030;
}
.redirectImg {
  cursor: pointer;
}

a:link {
  text-decoration: none;
}
a:hover {
  color: #009ee0;
}
ul li {
  border-bottom: 1px solid rgb(223, 223, 223);
}
ul {
  padding: 0px !important;
  list-style-type: none;
}
.icons-card {
  margin: 0px 3px 5px 0px;
}
h1 {
  color: #252a37;
  font-size: 26pt;
  font-weight: 700;
}
.custom-loader {
  animation: loader 1s infinite;
  display: flex;
  color: white;
}
@-moz-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-webkit-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-o-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
.hoverSearch:hover {
  color: #00aeef;
}
.active {
  color: #4eaeef;
}
.border-1px {
  border-style: solid;
  border-width: 1px;
  border-color: #cfd8dc;
}
.bg-color-grey {
  background-color: #f4f7fa !important;
}
</style>