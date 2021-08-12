<template>
  <v-container fluid py-0 ma-0>
    <transition name="fade">
      <v-timeline dense clipped align-top v-if="selectedTicket&&selectedTicket.agent&&profiles">
        <v-timeline-item
          right
          :v-if="selectedTicket!=null"
          v-for="(state, i) in selectedTicket.states"
          :key="i"
          fill-dot
        >
          <span slot="icon">
            <v-avatar size="39" color="red">
              <img
                v-if="state.type=='agent'"
                :src="getProfileImage(96, 128,selectedTicket.agent)"
                alt
              >
              <img v-else :src="getProfileImage(96, 128,selectedTicket.userId)" alt>
            </v-avatar>
          </span>
          <v-card :color="currentBussinesClub.theme.primary" v-if="state.status!='new'">
            <v-card-title style="color:white;font-size:14px !important" class="title">
              {{$t("message."+state.status)}}
              <v-spacer></v-spacer>
              <span>{{state.createdAt | moment("DD MMMM YYYY")}}</span>
            </v-card-title>
            <div style="background-color:white">
              <v-container fluid py-3>
                <span
                  class="label"
                  v-if="state.description=='acceptedByAgent'"
                  v-html="$t('message.'+state.description)"
                ></span>
                <span class="label" v-else v-html="state.description"></span>
              </v-container>
              <v-container fluid py-3>
                <v-layout>
                  <v-flex lg12>
                    <transition>
                      <div v-if="state.comments.length>0">
                        <v-layout row align-center>
                          <div>
                            <span
                              :style="'color:'+currentBussinesClub.theme.secondary+';!important;font-size:17px;cursor:pointer' "
                              @click="showComments(i)"
                            >({{$t('message.comments')}}&nbsp;{{state.comments.length}})</span>
                            <v-btn small icon @click="showComments(i)">
                              <v-icon
                                color="dark_caption"
                              >{{ displayComments[i].state ? 'keyboard_arrow_down':'keyboard_arrow_up' }}</v-icon>
                            </v-btn>
                          </div>
                          <v-divider></v-divider>
                        </v-layout>
                      </div>
                    </transition>
                    <transition name="fade">
                      <div v-if="displayComments[i].state">
                        <v-layout
                          :v-if="(j< state.comments.length-1)"
                          v-for="(comment, j) in state.comments"
                          :key="j"
                        >
                          <v-flex xs12 sm12 md11 lg12 xl11>
                            <v-flex>
                              <strong
                                :style="'color:'+currentBussinesClub.theme.primary+';!important;font-size: 18px !important;font-weight: 500;'"
                              >{{comment.name}}</strong>
                              <span
                                :style="'font-weight: 350;color:'+currentBussinesClub.theme.secondary"
                              >&nbsp;{{$t('message.commentedOnTheDay')}}&nbsp;{{comment.creationDatetime | moment("dddd DD MMMM YYYY, h:mm:ss a")}}</span>
                              <p
                                style="font-weight: 410;color:black !important"
                              >{{comment.commentText}}</p>
                            </v-flex>
                          </v-flex>
                        </v-layout>
                      </div>
                    </transition>
                    <v-layout row wrap align-center>
                      <v-flex xs12>
                        <v-text-field
                          :color="currentBussinesClub.theme.secondary"
                          v-on:keyup.enter="
                          addComment(state._id,i)"
                          v-model="commentField[i]"
                          :id="state._id"
                          :label="$t('message.newComment')"
                        >
                          <template slot="append-outer">
                            <v-btn
                              v-if="$vuetify.breakpoint.mdAndUp"
                              @click="addComment(state._id,i)"
                              class="ma-0 pa-0 ml-1"
                              :color="currentBussinesClub.theme.secondary"
                              dark
                              small
                            >{{$t('message.comment')}}</v-btn>
                            <v-btn
                              icon
                              flat
                              class="ma-0 pa-0 ml-1"
                              @click="addComment(state._id,i)"
                              v-else
                            >
                              <v-icon :color="currentBussinesClub.theme.secondary">send</v-icon>
                            </v-btn>
                          </template>
                        </v-text-field>
                      </v-flex>
                    </v-layout>
                  </v-flex>
                </v-layout>
              </v-container>
              <v-container fluid py-3 v-if="state.attachment.length>0">
                <v-icon :color="currentBussinesClub.theme.primary">attach_file</v-icon>
                <span v-for="(attach, j) in state.attachment" :key="j">
                  <a
                    :style="'color:'+currentBussinesClub.theme.primary"
                    :href="`${FMS_URL}/file/fetch/Ticket/${attach.url.url}?token=${token}`"
                    target="_blank"
                    :download="attach.url.filename"
                  >
                    <span
                      :style="'color:'+currentBussinesClub.theme.primary"
                      style="font-size:14px"
                    >{{attach.url.filename}}</span>
                  </a>
                  <span v-if="j!=state.attachment.length-1">&nbsp;|&nbsp;</span>
                </span>
              </v-container>
              <div v-if="selectedTicket.status=='progress'&&i==selectedTicket.states.length-1">
                <v-divider></v-divider>
                <v-subheader>{{$t('message.changedOfState')}}</v-subheader>
                <v-container py-0 fluid>
                  <v-add-state></v-add-state>
                </v-container>
              </div>
            </div>
          </v-card>
          <v-card :color="currentBussinesClub.theme.primary" v-else>
            <v-card-title style="color:white;font-size:14px !important" class="title">
              {{$t("message."+state.status)}}
              <v-spacer></v-spacer>
              <span>{{state.createdAt | moment("DD MMMM YYYY")}}</span>
            </v-card-title>
            <div style="background-color:white">
              <v-container fluid py-3>
                <span class="label" v-html="state.description"></span>
              </v-container>
              <v-container fluid pl-3 py-2>
                <v-icon
                  v-if="state.attachment.length>0"
                  :color="currentBussinesClub.theme.primary"
                >attach_file</v-icon>
                <span v-for="(attach, j) in state.attachment" :key="j">
                  <a
                    :style="'color:'+currentBussinesClub.theme.primary"
                    :href="`${FMS_URL}/file/fetch/Ticket/${attach.url.url}?token=${token}`"
                    target="_blank"
                    :download="attach.url.filename"
                  >
                    <span
                      :style="'color:'+currentBussinesClub.theme.primary"
                      style="font-size:14px"
                    >{{attach.url.filename}}</span>
                  </a>
                  <span v-if="j!=state.attachment.length-1">&nbsp;|&nbsp;</span>
                </span>
              </v-container>
            </div>
          </v-card>
        </v-timeline-item>
      </v-timeline>
    </transition>
  </v-container>
</template>
<script>
import { mapGetters } from "vuex";
import AppConfig from "@/constants/AppConfig";
import addState from "./addState";
import Vue from "vue";
export default {
  props: {
    profiles: {
      type: Object
    }
  },
  mounted() {
    this.token = sessionStorage.getItem("VATUP");
  },
  data() {
    return {
      getObjectPath: Vue.getObjectPath,
      FMS_URL: AppConfig.FMS_URL,
      token: ""
    };
  },
  computed: {
    ...mapGetters([
      "selectedTicket",
      "commentField",
      "displayComments",
      "userProfile",
      "currentBussinesClub"
    ])
  },
  components: {
    "v-add-state": addState
  },
  methods: {
    getProfileImage(realSize, size, userId) {
      const profile = JSON.parse(this.userProfile);
      return `${AppConfig.FMS_URL}/image/fetch/${Vue.getObjectPath(
        this.profiles[userId],
        "photoURL",
        "/static/img/perfil_yovirt.png"
      )}?tx=resize(w:${realSize},h:${realSize})&username=${Vue.getObjectPath(
        profile,
        "personal_info.names",
        ""
      )} ${Vue.getObjectPath(
        profile,
        "personal_info.surnames",
        ""
      )}&color=${Vue.getObjectPath(
        profile,
        "personal_info.color",
        "no"
      )}&size=32`;
    },
    addComment(state_id, index) {
      this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.savingComment")
      );
      var payload = {
        ticketId: this.selectedTicket._id,
        stateId: state_id,
        comment: {
          name:
            JSON.parse(this.userProfile).personal_info.names +
            " " +
            JSON.parse(this.userProfile).personal_info.surnames,
          photoUrl: JSON.parse(this.userProfile).personal_info.picture,
          commentText: this.commentField[index]
        }
      };
      this.$store.dispatch("addCommentAgent", payload).then(res => {
        this.selectedTicket.states[index].comments.push(res.data.data);
        this.$store.commit("clearCommentField", index);
        this.$root.$children[0].$emit("hideLoading");
      });
    },
    showComments(index) {
      this.$store.commit("showComments", index);
    }
  }
};
</script>
<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.9s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
.label {
  color: gray;
}
</style>
