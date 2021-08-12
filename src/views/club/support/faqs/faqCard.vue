<template>
  <v-container grid-list-xs ma-0 pa-2>
    <v-card class="noselect" @click="selectFaq(faq)" style="cursor: pointer">
      <v-container pa-0>
        <div
          @click.stop="check()"
          style="position:absolute;right:0%;padding-top:5px;padding-bottom:8px;padding-right:5px;padding-left:8px;z-index:1"
        >
          &nbsp;
          <v-icon small :style="'color:'+currentBussinesClub.theme.primary" v-if="!faq.pinned">mdi-pin-outline</v-icon>
          <v-icon small :style="'color:'+currentBussinesClub.theme.primary" v-else >mdi-pin</v-icon>
        </div>
      </v-container>
      <v-container
        :class="faq.selected?'':'cut-text'"
        pt-3
        pb-0
        px-3
        style="text-align:left!important"
      >
        <span style="font-weight: 410;color:grey">{{faq.question}}</span>
      </v-container>
      <v-container pt-1 pb-2 px-3 style="text-align:left!important">
        <v-layout row wrap style="padding:0px;margin:0px">
          <v-flex lg12 style="padding:0px;margin:0px">
            <v-layout row wrap class="align-center">
              <v-tooltip top>
                <v-chip style="height: 21px;" :color="currentBussinesClub.theme.primary" text-color="white" slot="activator">
                  <span class="mobileSpan">{{faq.categoryObject[0].name}}</span>
                </v-chip>
                <span>{{faq.categoryObject[0].name}}</span>
              </v-tooltip>
              <v-icon py-1 small color="grey" style="vertical-align:middle;">visibility</v-icon>
              <span style="font-size:12px;color:grey">&ensp;{{faq.viewCount}}&nbsp;{{$t('message.views')}}</span>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-container>
      <transition name="expand_card" @enter="enter" @after-enter="afterEnter" @leave="leave">
        <div v-if="faq.selected" style="text-align: justify;text-justify: inter-word">
          <v-card-text style="color:grey" class="pb-0">{{faq.answer}}</v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <div style="height:28px;width:178px">
              <v-scroll-x-transition>
                <div v-if="!faq.removing" style="position:absolute;right:10px;bottom:7px">
                  <v-icon
                    :id="faq.selected?'v-step-6':''"
                    class="pa-1"
                   :color="currentBussinesClub.theme.secondary"
                    small
                    @click.stop="faq.removing=true"
                  >delete</v-icon>
                  <v-icon
                    @click.stop="openFormUpdateFaq()"
                    :id="faq.selected?'v-step-7':''"
                    small
                    class="pa-1"
                    :style="'color:'+currentBussinesClub.theme.primary"
                  >edit</v-icon>
                </div>
              </v-scroll-x-transition>
              <v-scroll-x-transition>
                <div v-if="faq.removing">
                  <v-btn
                    style="padding:0px; margin:0px"
                    :color="currentBussinesClub.theme.primary"
                    flat
                    @click.native.stop="faq.removing=false"
                    small
                  >{{$t('message.cancel')}}</v-btn>
                  <v-btn
                    style="padding:0px; margin:0px"
                    :color="currentBussinesClub.theme.secondary"
                    flat
                    @click.native="remove()"
                    small
                  >{{$t('message.delete')}}</v-btn>
                </div>
              </v-scroll-x-transition>
            </div>
          </v-card-actions>
        </div>
      </transition>
    </v-card>
  </v-container>
</template>
<script>
import { mapGetters } from "vuex";
export default {
    computed: {
    ...mapGetters(["userProfile","currentBussinesClub"])
  },
  props: {
    faq: { type: Object }
  },
  data() {
    return {};
  },
  methods: {
    enter(element) {
      const width = getComputedStyle(element).width;
      element.style.width = width;
      element.style.position = "absolute";
      element.style.visibility = "hidden";
      element.style.height = "auto";
      const height = getComputedStyle(element).height;
      element.style.width = null;
      element.style.position = null;
      element.style.visibility = null;
      element.style.height = 0;
      setTimeout(() => {
        element.style.height = height;
      });
    },
    afterEnter(element) {
      element.style.height = "auto";
    },
    leave(element) {
      const height = getComputedStyle(element).height;
      element.style.height = height;
      setTimeout(() => {
        element.style.height = 0;
      });
    },
    selectFaq(faq) {
      this.faq.removing = false;
      this.faq.selected = !faq.selected;
    },
    openFormUpdateFaq() {
      this.$store.dispatch("changeCurrentEditFaq", this.faq).then(response => {
        if (response) {
          let form = this.$parent.$parent.$parent.$parent.$parent.$children[2];
          form.openForm();
        }
      });
    },
    check() {
      if (!this.faq.pinned) {
        /*  this.faq.pinned=true; */
        this.pinFaq(this.faq);
      } else {
        /* this.faq.pinned=false; */
        this.unPinFaq(this.faq);
      }
    },
    pinFaq() {
            this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.savingInformation")
      );
      let payload={
        faq:this.faq,
        userId:JSON.parse(this.userProfile)._id
      };
      this.$store
        .dispatch("pin", payload)
        .then(res => {
                      this.$root.$children[0].$emit(
        "hideLoading"
      );
        })
        .catch(res => {
          this.$root.$emit("showSnackBar", "¡Error al marcar la FAQ!", "error");
        });
    },
    unPinFaq() {
                  this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.savingInformation")
      );
            let payload={
        faq:this.faq,
         userId:JSON.parse(this.userProfile)._id
      }
      this.$store.dispatch("unPin",payload).then(res => {
          this.$root.$children[0].$emit(
        "hideLoading"
      );
      });
    },
    remove() {
      this.$store.dispatch("removeFaq", this.faq);
    }
  }
};
</script>
<style>
.cut-text {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.expand_card-enter-active,
.expand_card-leave-active {
  transition: height 0.1s ease-in-out;
  overflow: hidden;
}

.expand_card-enter,
.expand_card-leave-to {
  height: 0;
}
.expand_card-enter-active,
.expand_card-leave-active .expand_card-enter,
.expand_card-leave-t {
  will-change: height;
  transform: translateZ(0);
  backface-visibility: hidden;
  perspective: 1000px;
}
.pinned {
  color: #5d92f4;
}
.unPinned {
  color: #464d69;
}
.noselect {
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
</style>
