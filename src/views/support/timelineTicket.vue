<template>
  <v-card v-if="openDetail">
    <v-layout grid-list-md>
      <v-btn @click="close()" icon>
        <v-icon medium>fa fa-angle-left</v-icon>
      </v-btn>
      <v-flex xs11>
        <v-layout justify-center align-center>
          <span
            class="grey--text"
          >{{$t('message.ticketNumber')}}:&nbsp;{{selectedTicketUser.ticketNumber}}</span>
        </v-layout>
      </v-flex>
    </v-layout>
    <v-layout row wrap pl-3>
      <v-flex xs12>
        <v-card flat class="ma-0 pa-0">
          <div>
            <span class="grey--text">{{selectedTicketUser.topic}}</span>
          </div>
          <div>
            <span
              class="grey--text"
              style="font-size:12px;"
            >{{$t('message.category')}}:&nbsp;{{selectedTicketUser.objectCategory[0].name.toUpperCase()}}</span>
          </div>
          <div>
            <span class="grey--text" style="font-size:12px;">{{$t('message.state')}}:&nbsp;</span>
            <!-- <v-chip color="#0e2b4b" style="height:18px" small outline> -->
            <span
              class="grey--text"
              style="font-size:12px;"
            >{{$t('message.'+selectedTicketUser.status).toUpperCase()}}</span>
            <!--  </v-chip> -->
          </div>
          <div>
            <span class="grey--text" style="font-size:12px;">{{$t('message.priority')}}:&nbsp;</span>
            <!-- <v-chip color="#0e2b4b" style="height:18px" small outline> -->
            <span
              class="grey--text"
              style="font-size:12px;"
            >{{$t('message.'+selectedTicketUser.priority).toUpperCase()}}</span>
            <!--   </v-chip> -->
          </div>
          <div
            v-if="selectedTicketUser.status!=='cancelled'"
            style="position:absolute;right:10px;bottom:1px"
          >
            <v-btn @click="cancelDialog=true" small :color="currentBussinesClub.theme.secondary">
              <span style="color:white">{{$t('message.cancelTicket')}}</span>
            </v-btn>
          </div>
        </v-card>
      </v-flex>
    </v-layout>
    <v-divider class="mx-3"></v-divider>
    <v-timeline dense class="px-3">
      <v-timeline-item
        left
        v-for="(state, i) in selectedTicketUser.states"
        :key="i"
        color="#00adef"
        fill-dot
      >
        <span slot="icon">
          <v-avatar size="39" color="red">
            <img
              v-if="state.type=='agent'"
              :src="getProfileImage(96, 128,selectedTicketUser.agent)"
              alt
            >
            <img v-else :src="getProfileImage(96, 128,selectedTicketUser.userId)" alt>
          </v-avatar>
        </span>
        <v-card :color="currentBussinesClub.theme.primary" v-if="state.status!='New'">
          <v-card-title style="color:white;font-size:14px !important" class="title">
            <span>{{$t('message.'+state.status).toUpperCase()}}</span>
            <v-spacer></v-spacer>
            <span>{{state.updatedAt | moment("dddd DD MMMM YYYY, h:mm:ss a")}}</span>
          </v-card-title>
          <v-container
            grid-list
            :style="'background-color:white;border-left:2px'+currentBussinesClub.theme.primary+' solid'"
          >
            <v-card-text
              style="padding-top:0px;padding-bottom:0px;padding-left:4px;"
              class="white text--primary"
              v-if="state.description=='acceptedByAgent'"
              v-html="$t('message.'+state.description)"
            ></v-card-text>
            <v-card-text
              style="padding-top:0px;padding-bottom:0px;padding-left:4px;"
              class="white text--primary"
              v-else
              v-html="state.description"
            ></v-card-text>
            <v-layout pr-2 pl-1>
              <v-flex lg12>
                <div>
                  <v-layout v-if="i==selectedTicketUser.states.length-1" row wrap align-center>
                    <v-flex sm12 md12 xs12 lg12>
                      <v-text-field
                        :color="currentBussinesClub.theme.primary"
                        v-model="commentField"
                        ref="newcomen"
                        :label="$t('message.newComment')"
                        @keyup.enter="addComment(state)"
                      >
                        <template slot="append-outer">
                          <v-btn
                            v-if="$vuetify.breakpoint.mdAndUp"
                            class="ma-0 pa-0 ml-1"
                            :color="currentBussinesClub.theme.secondary"
                            dark
                            small
                            @click="addComment(state)"
                          >{{$t('message.comment')}}</v-btn>
                          <v-btn v-else @click="addComment(state)" icon flat>
                            <v-icon
                              class="ma-0 pa-0 ml-1"
                              :color="currentBussinesClub.theme.secondary"
                            >send</v-icon>
                          </v-btn>
                        </template>
                      </v-text-field>
                    </v-flex>
                  </v-layout>
                  <v-layout row align-center v-if="state.comments.length>0">
                    <div>
                      <span
                        style="color:#2196f3 !important;font-size:14px;cursor:pointer"
                        @click="showComments(i)"
                      >{{$t('message.comments')}}({{state.comments.length}})</span>
                      <v-btn small icon @click="showComments(i)">
                        <v-icon
                          color="dark_caption"
                        >{{ displayComments[i].state ? 'keyboard_arrow_down':'keyboard_arrow_up' }}</v-icon>
                      </v-btn>
                    </div>
                  </v-layout>
                  <v-layout v-else pl-1>
                    <span style="font-size:11px">{{$t('message.noComments')}}</span>
                  </v-layout>
                </div>
                <transition>
                  <div v-if="displayComments[i].state">
                    <v-layout
                      :v-if="(j< state.comments.length-1)"
                      v-for="(comment, j) in state.comments"
                      :key="j"
                    >
                      <v-flex pl-3 xs12 sm12 md11 lg12 xl11>
                        <v-flex>
                          <strong
                            style="color:#2196f3 !important;font-size: 15px !important;font-weight: 500;"
                          >{{comment.name}}</strong>
                          <span
                            style="font-weight: 350;color:#2196f3 !important;font-size: 15px"
                          >&nbsp;{{$t('message.commentedOnTheDay')}} {{comment.creationDatetime | moment("dddd DD MMMM YYYY, h:mm:ss a")}}</span>
                          <p style="font-weight: 410;color:black !important">{{comment.commentText}}</p>
                        </v-flex>
                      </v-flex>
                    </v-layout>
                  </div>
                </transition>
              </v-flex>
            </v-layout>
            <v-layout align-center pr-2 pl-0>
              <v-flex xs12>
                <div>
                  <v-icon v-if="state.attachment.length>0" color="#033188">attach_file</v-icon>
                  <span v-for="(attach, j) in state.attachment" :key="j">
                    <a
                      :href="`${FMS_URL}/file/fetch/Ticket/${attach.url.url}?token=${token}`"
                      target="_blank"
                      style="color:#2196f3 !important"
                      :download="attach.url.filename"
                    >
                      <div class="cut-text">{{attach.url.filename}}</div>
                    </a>
                    <span v-if="j!=state.attachment.length-1">&nbsp;|&nbsp;</span>
                  </span>
                </div>
              </v-flex>
            </v-layout>
            <span
              v-if="state.status=='waiting'&&i==selectedTicketUser.states.length-1"
              :style="'font-size:11px;color:'+currentBussinesClub.theme.secondary"
            >{{$t('message.expirationTimeTicketMessage',{date:expiration})}}</span>
          </v-container>
          <v-divider
            style="padding:0px;margin:0px"
            v-if="state.status=='waiting'&&i==selectedTicketUser.states.length-1"
          ></v-divider>
          <div
            :style="'background-color:white;border-left:2px'+currentBussinesClub.theme.primary+' solid'"
            v-if="state.status=='waiting'&&i==selectedTicketUser.states.length-1"
          >
            <v-subheader>{{$t('message.answer')}}</v-subheader>
            <v-container
              py-0
              grid-list-xs
              style="background-color:white"
              v-if="state.status=='waiting'&&i==selectedTicketUser.states.length-1"
            >
              <v-layout
                row
                wrap
                v-if="state.status=='waiting'&&i==selectedTicketUser.states.length-1"
              >
                <v-text-field
                  :color="currentBussinesClub.theme.primary"
                  v-model="descriptionChangeState"
                  ref="newcomen"
                  :label="$t('message.description')"
                ></v-text-field>
              </v-layout>
              <v-card-actions
                v-if="state.status=='waiting'&&i==selectedTicketUser.states.length-1"
                style="background-color:white"
                ma-0
                pa-0
              >
                <v-upload-button refName="uploadTimeline" ref="upload"></v-upload-button>
                <v-btn :color="currentBussinesClub.theme.primary" icon @click="changeState(state)">
                  <v-icon color="white">send</v-icon>
                </v-btn>
              </v-card-actions>
            </v-container>
          </div>
        </v-card>
        <v-card color="#00adef" v-else>
          <v-card-title style="color:white;font-size:14px !important" class="title">
            <span v-if="state.status == 'New'">{{$t('message.'+state.status)}}</span>
            <v-spacer></v-spacer>
            <span>{{selectedTicketUser.createdDatetime | moment("dddd DD MMMM YYYY, h:mm:ss a")}}</span>
          </v-card-title>
          <v-container style="background-color:white" pa-0>
            <v-card-text v-html="state.description" style="white-space: pre-line;font-size:14px"></v-card-text>
          </v-container>
        </v-card>
      </v-timeline-item>
    </v-timeline>
    <v-popup-dialog
      ref="cancelTicketForm"
      :model="cancelDialog"
      :title="$t('message.cancelTicket')"
      showRequired
      @onSave="dispatchCancelTicket()"
      @onClose="cancelDialog=false"
    >
      <v-layout row wrap>
        <v-text-field :label="$t('message.reason')+'*'" v-model="cancelReasonField"></v-text-field>
      </v-layout>
    </v-popup-dialog>
  </v-card>
</template>
<script>
import moment from "moment";
import { mapGetters } from "vuex";
import AppConfig from "../../constants/AppConfig";
import Vue from "vue";
export default {
  computed: {
    ...mapGetters([
      "currentBussinesClub",
      "userProfile",
      "selectedTicketUser",
      "openDetail"
    ])
  },
  props: {
    profiles: {
      type: Object
    }
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
    close() {
      Vue.nextTick(() => {
        this.$parent.$parent.$parent.$parent.$parent.$el.scrollTop = '100';
      });
      this.$store.commit("closeDetail");

    },
    show() {
      Vue.nextTick(() => {
        this.$parent.$parent.$parent.$parent.$parent.$el.scrollTop = '350';
      });
      this.displayComments = [];
      for (
        let index = 0;
        index < this.selectedTicketUser.states.length;
        index++
      ) {
        this.displayComments.push({
          state: false
        });
      }
      this.expiration = moment(this.selectedTicketUser.expiration).format(
        "dddd DD MMMM YYYY, h:mm:ss a"
      );
      this.token = sessionStorage.getItem("VATUP");
      this.$store.commit("openDetail");
      if (this.selectedTicketUser.status == "waiting" && !this.firstOpen) {
        Vue.nextTick(() => {
          this.$watch(
            () => {
              return this.$refs.upload[0].$refs.uploadTimeline.uploaded;
            },
            val => {
              if (val) {
                var noError = true;
                var attachements = [];
                var files = this.$refs.upload[0].$refs.uploadTimeline.files;
                if (files.length > 0) {
                  for (var i = 0; i < files.length; i++) {
                    try {
                      var contentType = files[i].response.data.mimetype;
                      var url = files[i].response.data.path.replace(
                        / /g,
                        "%20"
                      );
                      var fileName = files[i].response.data.originalName;
                      var size = files[i].response.data.size;
                      this.additionalAttachments.push({
                        contentType: contentType,
                        url: {
                          url: url,
                          filename: fileName,
                          size: size
                        }
                      });
                    } catch (error) {
                      noError = false;
                      this.$refs.upload[0].$refs.uploadTimeline.clear();
                    }
                  }
                  this.dispatchState();
                }
              }
            }
          );
        });
      }
    },
    showComments(index) {
      this.displayComments[index].state = !this.displayComments[index].state;
    },
    addComment(state) {
      if (this.commentField != "") {
        this.$root.$children[0].$emit(
          "showLoading",
          this.$t("message.savingComment")
        );
        var payload = {
          ticketId: this.selectedTicketUser._id,
          stateId: state._id,
          comment: {
            name:
              JSON.parse(this.userProfile).personal_info.names +
              " " +
              JSON.parse(this.userProfile).personal_info.surnames,
            photoUrl: JSON.parse(this.userProfile).personal_info.picture,
            commentText: this.commentField
          }
        };
        this.$store.dispatch("addComment", payload).then(res => {
          state.comments.push(res);
          this.commentField = "";
          this.$root.$children[0].$emit("hideLoading");
        });
      }
    },
    changeState(state) {
      this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.savingInformation")
      );
      if (!this.$refs.upload[0].$refs.uploadTimeline.uploaded) {
        this.$refs.upload[0].$refs.uploadTimeline.active = true;
      } else {
        this.dispatchState();
      }
    },
    dispatchState() {
      var newState = {
        ticketId: this.selectedTicketUser._id,
        description: this.descriptionChangeState,
        attachment: this.additionalAttachments
      };
      this.$store.dispatch("addState", newState).then(state => {
        this.selectedTicketUser.status = "progress";
        this.selectedTicketUser.states.push(state);
        this.$root.$children[0].$emit("hideLoading");
        this.displayComments.push({
          state: false
        });
        /* this.$refs.upload[0].$refs.upload.clear(); */
      });
    },
    openCancelTicket() {
      this.$refs.cancelTicketForm.$refs.popupDialog.reset();
      this.cancelReasonField = "";
      this.cancelDialog = true;
    },
    dispatchCancelTicket() {
      this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.savingInformation")
      );
      var payload = {
        ticketId: this.selectedTicketUser._id,
        description: this.cancelReasonField
      };
      this.$store.dispatch("cancelTicket", payload).then(state => {
        this.selectedTicketUser.states.push(state);
        this.cancelDialog = false;
        this.$root.$children[0].$emit("hideLoading");
        this.displayComments.push({
          state: false
        });
      });
    }
  },
  data() {
    return {
      firstOpen: false,
      open: false,
      FMS_URL: AppConfig.FMS_URL,
      token: "",
      displayComments: [
        {
          state: false
        }
      ],
      commentField: "",
      currentState: null,
      mountedReady: false,
      getObjectPath: Vue.getObjectPath,
      additionalAttachments: [],
      descriptionChangeState: "",
      cancelDialog: false,
      cancelReasonField: "",
      expiration: ""
    };
  }
};
</script>
<style scoped>
.cut-text {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
