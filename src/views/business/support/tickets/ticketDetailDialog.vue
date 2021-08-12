<template>
  <div class="text-xs-center" v-if="selectedTicket!=null" >
    <v-dialog v-model="open" persistent :fullscreen="$vuetify.breakpoint.mdAndDown">
      <v-card>
        <v-card-actions class="dialog-title linear-gradient form-dialog"  primary-title>
          <v-card-title>{{$t('message.ticketDetail')}}</v-card-title>
          <v-spacer></v-spacer>
          <v-btn fab flat icon dark small @click="hide()" style="margin-right: 8px!important;">
            <v-icon>close</v-icon>
          </v-btn>
        </v-card-actions>
        <vue-perfect-scrollbar ref="scroll" style="height:calc(100vh - 150px)" :settings="settings">
          <v-card class="pb-2">
            <v-card flat>
              <v-container pa-0 fluid>
                <v-layout row wrap>
                  <v-flex xs5 sm5 md5 lg10 xl10 class="align-center pl-3">
                    <span :style="'color:'+currentBussinesClub.theme.primary+';font-weight:410'">{{$t('message.basicInformation')}}</span>
                  </v-flex>
                  <v-flex xs7 sm7 md7 lg2 xl2 class="text-xs-right ">
                    <div class="pr-3">
                      <v-chip small :color="currentBussinesClub.theme.secondary" dark>{{$t("message."+selectedTicket.status)}}</v-chip>
                    </div>
                  </v-flex>
                </v-layout>
              </v-container>
            </v-card>
            <v-divider style="margin:0px"></v-divider>
            <v-layout align-center style="height: 31.98px">
              <v-flex xs3 sm2 md1 lg1 class="text-xs-right">
                <span class="label">{{$t('message.ticketN')}}:&nbsp;</span>
              </v-flex>
              <v-flex xs9 sm9 md9 lg9 class="text-xs-left cut-text">
                <strong :style="'color:'+currentBussinesClub.theme.primary+';font-size:18px'">{{selectedTicket.ticketNumber}}</strong>
              </v-flex>
            </v-layout>
            <v-layout align-center style="height: 31.98px">
              <v-flex xs3 sm2 md1 lg1 class="text-xs-right">
                <span class="label">{{$t('message.topic')}}:&nbsp;</span>
              </v-flex>
              <v-flex xs9 sm9 md9 lg9 class="text-xs-left cut-text">
                <span :style="'color:'+currentBussinesClub.theme.primary">{{selectedTicket.topic}}</span>
              </v-flex>
            </v-layout>
            <v-layout align-center style="height: 31.98px">
              <v-flex xs3 sm2 md1 lg1 class="text-xs-right">
                <span class="label">{{$t('message.created')}}:&nbsp;</span>
              </v-flex>
              <v-flex xs9 sm9 md9 lg9 class="text-xs-left cut-text">
                <span
                  :style="'color:'+currentBussinesClub.theme.primary"
                >{{selectedTicket.createdAt | moment("dddd DD MMMM YYYY, h:mm:ss a")}}</span>
              </v-flex>
            </v-layout>
            <v-layout align-center>
              <v-flex xs3 sm2 md1 lg1 class="text-xs-right">
                <span class="label">{{$t('message.priority')}}:&nbsp;</span>
              </v-flex>
              <v-flex xs9 sm10 md11 lg9 class="text-xs-left cut-text">
                <div>
                  <v-chip
                    dark
                    style="margin-left:0px"
                    :color="getPriorityColor(selectedTicket.priority)"
                    small
                  >{{$t('message.' + selectedTicket.priority)}}</v-chip>
                </div>
              </v-flex>
            </v-layout>
            <v-layout align-center>
              <v-flex xs3 sm2 md1 lg1 class="text-xs-right">
                <span class="label">{{$t('message.category')}}:&nbsp;</span>
              </v-flex>
              <v-flex xs9 sm9 md9 lg9 class="text-xs-left">
                <div>
                  <v-chip
                    style="margin-left:0px"
                    dark
                    :color="currentBussinesClub.theme.primary"
                    small
                  >{{selectedTicket.objectCategory[0].name}}</v-chip>
                </div>
              </v-flex>
            </v-layout>
            <v-layout align-center>
              <v-flex xs3 sm2 md1 lg1 class="text-xs-right">
                <span v-if="selectedTicket.tags.length!=0" class="label">Tags:&nbsp;</span>
              </v-flex>
              <v-flex xs9 sm9 md9 lg9 class="text-xs-left cut-text">
                <div>
                  <v-chip
                    small
                    dark
                    style="margin-left:0px"
                    color="#1f98f3"
                    v-for="(tag, i) in selectedTicket.tags"
                    :key="i"
                  >{{tag}}</v-chip>
                </div>
              </v-flex>
            </v-layout>
            <transition name="fade">
              <div v-if="!selectedTicket.agent">
                <v-divider style="margin:0px"></v-divider>
                <v-card-actions>
                  <ul>
                    <li class="label">{{$t('message.messageAcceptTicket')}}</li>
                  </ul>
                </v-card-actions>
                <v-card-actions style="padding-right:20px;padding-bottom:20px;height:60px">
                  <v-spacer></v-spacer>
                  <transition name="fade">
                    <v-btn
                      :loading="loadingAccept"
                      v-if="selectedTicket.status=='new'&&!selectedTicket.agent"
                      @click.native="acceptTicket()"
                      :color="currentBussinesClub.theme.primary"
                      dark
                    >{{$t('message.acceptButton')}}</v-btn>
                  </transition>
                </v-card-actions>
              </div>
            </transition>
          </v-card>
          <v-ticket-timeline :profiles="profiles"></v-ticket-timeline>
        </vue-perfect-scrollbar>
      </v-card>
    </v-dialog>
  </div>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import ticketTimeLine from "./ticketTimeline";
export default {
  computed: {
    ...mapGetters(["selectedTicket","userProfile","currentBussinesClub"])
  },
  data() {
    return {
      deletingTicket: false,
      loadingAccept: false,
      open: false,
      settings: {
        maxScrollbarLength: 160
      },
      profiles:{}
    };
  },
  components: {
    "v-ticket-timeline": ticketTimeLine
  },
  methods: {
    show() {
      this.open = true;
      let payload={users:[this.selectedTicket.userId,this.selectedTicket.agent]}
        this.$store.dispatch('getProfileUsers',payload)
        .then(response=>{
          this.profiles=response;
        })
      Vue.nextTick(() => {
        this.$refs.scroll.$el.scrollTop =0;
        });
        
        
    },
    hide() {
      this.open = false;
    },
    getPriorityColor(priority) {
      switch (priority) {
        case "low":
          return "#01a654";
          break;
        case "medium":
          return "#fec907";
          break;
        case "high":
          return "#ed1b23";
          break;
        default:
          break;
      }
    },
    acceptTicket() {
      this.loadingAccept = true;
      this.$store.dispatch("takeTicket");
      {
        this.loadingAccept = false;
      }
    }
  }
};
</script>
<style>
.form-dialog {
  color: white;
  padding: 0px;
  background-color: #5d92f4;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
.label{
  color:gray
}
</style>
