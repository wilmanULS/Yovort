<template>
  <div lazy>
    <v-container grid-list-xs class="no-padding">
      <v-layout row wrap>
        <v-flex xs12 sm12 md6 lg6 xl6>
          <v-text-field
            :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
            autofocus
            :color="currentBussinesClub.theme.primary"
            v-model="search"
            append-icon="search"
            v-bind:label="$t('message.searchUsers')"
            hide-details
            v-on:keydown.enter="dispatchFilter"
          ></v-text-field>
        </v-flex>
        <v-flex xs6 sm6 md1 lg1 xl1>
          <v-text-field
            class="mr05"
            :class="{'ml05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
            v-model="minAge"
            :color="currentBussinesClub.theme.primary"
            hide-details
            type="number"
            name="minEdad"
            v-bind:label="$t('message.minAge')"
            @change="checkMinAge"
          ></v-text-field>
        </v-flex>
        <v-flex xs6 sm6 md1 lg1 xl1>
          <v-text-field
            class="ml05"
            :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
            v-model="maxAge"
            :color="currentBussinesClub.theme.primary"
            hide-details
            type="number"
            name="maxEdad"
            v-bind:label="$t('message.maxAge')"
            @change="checkMaxAge"
          ></v-text-field>
        </v-flex>
        <v-flex xs6 sm6 md2 lg2 xl2>
          <v-select
            class="mr05"
            :class="{'ml05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
            v-model="filterGender"
            :color="currentBussinesClub.theme.primary"
            :items="genders"
            item-text="name"
            item-value="code"
            hide-details
            v-bind:no-data-text="$t('message.nodatafound')"
            v-bind:label="$t('message.gender')"
            @change="dispatchFilter"
          ></v-select>
        </v-flex>
        <v-flex xs6 sm6 md2 lg2 xl2>
          <v-select
            class="ml05"
            :class="{'mb-1':$vuetify.breakpoint.smAndDown }"
            v-model="stateMembers"
            :color="currentBussinesClub.theme.primary"
            :items="itemsState"
            item-text="name"
            item-value="code"
            hide-details
            v-bind:no-data-text="$t('message.nodatafound')"
            v-bind:label="$t('message.state')"
            prepend-inner-icon="fas fa-sliders-h"
            @change="dispatchFilter"
          ></v-select>
        </v-flex>
      </v-layout>
      <br>
      <v-layout align-center justify-space-between row fill-height class="ma-2">
        <v-btn
          @click="clearFilters"
          outline
          :color="currentBussinesClub.theme.primary"
          class="rounded-buttons ma-0"
          style="background-color: white!important;"
        >{{$t('message.clearFilters')}}</v-btn>
        <v-btn
          v-if="currentBussinesClub.generalSettings.membersSettings.registering==1 ||currentBussinesClub.generalSettings.membersSettings.registering==3"
          @click="openUserListDialog()"
          outline
          :color="currentBussinesClub.theme.primary"
          class="rounded-buttons ma-0"
          style="background-color: white!important;"
        >{{$t('message.addMembers')}}</v-btn>
      </v-layout>
    </v-container>
    <v-data-table
      :no-data-text="$t('message.emptyDataTable')"
      :loading="loadingMembers"
      :pagination.sync="membersPagination"
      :rows-per-page-items="membersPagination.rowsPerPageItems"
      :total-items="membersPagination.totalItems"
      :rows-per-page-text="$t('message.rowpage')"
      :headers="$vuetify.breakpoint.mdAndUp? headers:headersMobile"
      :items="membersItems"
    >
      <template slot="items" slot-scope="props">
        <td
          style="cursor:pointer"
          @click="openInformationDialog(props.item)"
          v-if="$vuetify.breakpoint.mdAndUp"
        >
          <v-list class="transparent-list" two-line>
            <template>
              <v-list-tile>
                <v-list-tile-avatar>
                  <v-avatar size="64">
                    <v-img
                      style="cursor:pointer"
                      @click="openInformationDialog(props.item)"
                      class="user-avatar-desktop user-profile-img"
                      :src="`${FMS_URL}/image/fetch/${getObjectPath(props, 'item.profile.picture','/static/img/perfil_yovirt.png')}?tx=resize(w:64,h:64)&username=${getObjectPath(props, 'item.profile.names','')} ${getObjectPath(props, 'item.profile.surnames','')}&color=${getObjectPath(props, 'item.profile.color','no')}&size=64`"
                      lazy-src="/static/img/perfil_yovirt_small.png"
                    ></v-img>
                  </v-avatar>
                </v-list-tile-avatar>&nbsp;
                <v-list-tile-content>
                  <v-list-tile-title
                    class="darker-titles"
                    v-html="getObjectPath(props,'item.profile.names','')+' '+getObjectPath(props,'item.profile.surnames','')"
                  ></v-list-tile-title>
                  <v-list-tile-sub-title
                    class="membersSubTitle"
                    v-if="!getObjectPath(props,'item.profile.addresses.address.city',null) && !getObjectPath(props,'item.profile.addresses.address.province',null)"
                    v-html="messageNoUbication"
                  ></v-list-tile-sub-title>
                  <v-list-tile-sub-title
                    v-else
                    class="membersSubTitle"
                    v-html="getObjectPath(props,'item.profile.addresses.address.province','')+' - '+getObjectPath(props,'item.profile.addresses.address.city','')+', '+getObjectPath(props,'item.profile.addresses.address.country','')"
                  ></v-list-tile-sub-title>
                  <v-list-tile-sub-title
                    class="membersSubTitle"
                    v-html="calculateAgeStr(getObjectPath(props,'item.profile.birthdate',''))"
                  ></v-list-tile-sub-title>
                </v-list-tile-content>
              </v-list-tile>
            </template>
          </v-list>
        </td>
        <td
          class="text-xs-left"
          v-if="$vuetify.breakpoint.mdAndUp"
        >{{GetFormattedDate(getObjectPath(props,'item.registerDate',''))}}</td>
        <td
          class="text-xs-left"
          v-if="$vuetify.breakpoint.mdAndUp"
        >{{GetFormattedDate(getObjectPath(props,'item.requestDate',''))}}</td>
        <td class="text-xs-left" v-if="$vuetify.breakpoint.mdAndUp">
          <v-chip
            v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===2"
            flat
            :color="currentBussinesClub.theme.primary"
            text-color="white"
          >{{$t('message.approve')}}</v-chip>
          <v-chip
            v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===0"
            flat
            color="green"
            text-color="white"
          >{{$t('message.pending')}}</v-chip>
          <v-chip
            v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===8"
            flat
            color="green"
            text-color="white"
          >{{$t('message.invitedMember')}}</v-chip>
          <v-chip
            v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===4"
            flat
            color="red"
            text-color="white"
          >{{$t('message.rejectUser')}}</v-chip>
          <v-chip
            v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===6"
            flat
            :color="currentBussinesClub.theme.accent"
            text-color="white"
          >{{$t('message.block')}}</v-chip>
        </td>
        <td class="text-xs-left" v-if="$vuetify.breakpoint.mdAndUp">
          <v-tooltip bottom>
            <v-icon
              v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)!==2 && getObjectPath(props, 'item.membership.status_flag_catalog', 0)!==6&& getObjectPath(props, 'item.membership.status_flag_catalog', 0)!==8"
              slot="activator"
              small
              class="mr-2"
              @click="approveMember(props.item)"
            >
              fas
              fa-user-check
            </v-icon>
            <span>{{$t('message.userApprove')}}</span>
          </v-tooltip>
          <v-tooltip bottom>
            <v-icon
              v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===6 "
              slot="activator"
              small
              class="mr-2"
              @click="approveMember(props.item)"
            >fas fa-user-check</v-icon>
            <span>{{$t('message.unlockMember')}}</span>
          </v-tooltip>
          <v-tooltip bottom>
            <v-icon
              v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)!==6"
              slot="activator"
              class="mr-2"
              small
              @click="openDialogBlockMember(props.item)"
            >fas fa-user-slash</v-icon>
            <span>{{$t('message.userBlock')}}</span>
          </v-tooltip>
          <v-tooltip bottom>
            <v-icon
              v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)!==4 && getObjectPath(props, 'item.membership.status_flag_catalog', 0)!==6 "
              slot="activator"
              small
              @click="openDialogRejectMember(props.item)"
            >
              fas
              fa-user-times
            </v-icon>
            <span>{{$t('message.userReject')}}</span>
          </v-tooltip>
        </td>
        <td
          style="cursor:pointer"
          @click="openInformationDialog(props.item)"
          v-if="$vuetify.breakpoint.smAndDown"
        >
          <v-list class="transparent-list" two-line>
            <template>
              <v-list-tile>
                <v-list-tile-avatar>
                  <v-avatar size="64">
                    <v-img
                      style="cursor:pointer"
                      @click="openInformationDialog(props.item)"
                      class="user-avatar-desktop user-profile-img"
                      :src="`${FMS_URL}/image/fetch/${getObjectPath(props, 'item.profile.picture','/static/img/perfil_yovirt.png')}?tx=resize(w:64,h:64)&username=${getObjectPath(props, 'item.profile.names','')} ${getObjectPath(props, 'item.profile.surnames','')}&color=${getObjectPath(props, 'item.profile.color','no')}&size=64`"
                      lazy-src="/static/img/perfil_yovirt_small.png"
                    ></v-img>
                  </v-avatar>
                </v-list-tile-avatar>&nbsp;
                <v-list-tile-content>
                  <v-list-tile-title
                    class="darker-titles"
                    v-html="getObjectPath(props,'item.profile.names','')+' '+getObjectPath(props,'item.profile.surnames','')"
                  ></v-list-tile-title>
                  <v-list-tile-sub-title
                    class="membersSubTitle"
                    v-if="!getObjectPath(props,'item.profile.addresses.address.city',null) && !getObjectPath(props,'item.profile.addresses.address.province',null)"
                    v-html="messageNoUbication"
                  ></v-list-tile-sub-title>
                  <v-list-tile-sub-title
                    v-else
                    class="membersSubTitle"
                    v-html="getObjectPath(props,'item.profile.addresses.address.province','')+' - '+getObjectPath(props,'item.profile.addresses.address.city','')+', '+getObjectPath(props,'item.profile.addresses.address.country','')"
                  ></v-list-tile-sub-title>
                  <v-list-tile-sub-title>
                    <v-chip
                      v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===2"
                      flat
                      :color="currentBussinesClub.theme.primary"
                      text-color="white"
                    >{{$t('message.approve')}}</v-chip>
                    <v-chip
                      v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===0"
                      flat
                      color="green"
                      text-color="white"
                    >{{$t('message.pending')}}</v-chip>
                    <v-chip
                      v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===8"
                      flat
                      color="green"
                      text-color="white"
                    >{{$t('message.invitedMember')}}</v-chip>
                    <v-chip
                      v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===4"
                      flat
                      color="red"
                      text-color="white"
                    >{{$t('message.rejectUser')}}</v-chip>
                    <v-chip
                      v-if="getObjectPath(props, 'item.membership.status_flag_catalog', 0)===6"
                      flat
                      :color="currentBussinesClub.theme.accent"
                      text-color="white"
                    >{{$t('message.block')}}</v-chip>
                  </v-list-tile-sub-title>
                </v-list-tile-content>
              </v-list-tile>
            </template>
          </v-list>
        </td>
      </template>
      <v-alert
        slot="no-results"
        :value="true"
        :color="currentBussinesClub.theme.primary"
        icon="warning"
      >{{$t('message.noSearchResult')}}.</v-alert>
    </v-data-table>
    <v-confirm-dialog
      v-bind:titleToolbar="$t('message.discardChanges')"
      ref="confirmDiscard"
      type="question"
      :heading="$t('message.discardChanges')"
      v-bind:message="$t('message.discardChangesLabel')"
      @onConfirm="discardCausesReject()"
    ></v-confirm-dialog>
    <v-confirm-dialog
      v-bind:titleToolbar="$t('message.userBlock')"
      ref="blockMember"
      type="question"
      :heading="$t('message.userBlock')"
      v-bind:message="$t('message.deleteQuestionBlock')"
      @onConfirm="blockMember(itemBlockMember)"
    ></v-confirm-dialog>
    <v-information-dialog
      :userData="itemAux"
      @onApprove="approveMember(itemAux)"
      @onReject="openDialogRejectMember(itemAux) "
      @onBlock="blockMember(itemAux)"
      @onCancel="closeInformationDialog()"
      ref="informationDialog"
    ></v-information-dialog>
    <v-user-list ref="userListDialog"></v-user-list>
    <v-popup-dialog
      ref="formRejectUser"
      :model="openRejectDialog"
      :title="$t('message.messageRejectedTittleDialog')"
      showRequired
      @onClose="closeDialogRejectMember"
      @onSave="rejectMember(itemRejectMember)"
    >
      <v-flex xs12 sm12 md12 lg12 xl12>
        <v-card flat>
          <v-card-actions>
            <span
              v-bind:style="{'color':currentBussinesClub.theme.primary}"
            >{{$t('message.addCauseslabel')}}</span>
          </v-card-actions>
          <v-card
            flat
            style="height: 200px; border-style: solid; border-width: 1px; border-color: #cfd8dc;"
          >
            <Vueditor id="editorContainer" ref="textEditor1"></Vueditor>
          </v-card>
          <br>
          <v-text-field
            v-model="daysreject"
            :color="currentBussinesClub.theme.primary"
            :rules="rulesDay"
            requerid
            type="number"
            v-bind:label="$t('message.daysRejectUser')+' *'"
          ></v-text-field>
        </v-card>
      </v-flex>
    </v-popup-dialog>
  </div>
</template>
<script>
import { createEditor } from "vueditor";
import confirmDialog from "../../components/ySocialHome/confirmDialog";
import informationAdminDialog from "../../components/ySocialHome/informationAdminDialog";
import userListToMember from "../../components/ySocialHome/userListDialog";
import { mapGetters } from "vuex";
import userCardMembers from "./userCardMembers";
import { setTimeout } from "timers";
import Vue from "vue";
import AppConfig from "../../constants/AppConfig.js";
export default {
  components: {
    "v-user-card-members": userCardMembers,
    "v-confirm-dialog": confirmDialog,
    "v-information-dialog": informationAdminDialog,
    "v-user-list": userListToMember
  },
  created() {
    this.$store.dispatch("doGetGenders");
  },
  mounted() {
    this.inst = createEditor("#editorContainer", this.configEditText);
  },
  watch: {
    membersPagination: {
      handler() {
        this.reloadData();
      },
      deep: true
    },
    search: function(newVal, oldVal) {
      if (newVal === "") {
        this.dispatchFilter();
      }
    }
  },
  computed: {
    ...mapGetters([
      "userProfile",
      "getBusinessMembers",
      "currentBussinesClub",
      "genders",
      "membersPagination",
      "membersItems"
    ]),
    userProfile: {
      get() {
        return JSON.parse(this.$store.getters.userProfile);
      }
    },
    membersPagination: {
      get: function() {
        return this.$store.getters.membersPagination;
      },
      set: function(value) {
        this.$store.commit("setPagination", value);
      }
    },
    membersItems() {
      return this.$store.getters.membersItems;
    }
  },
  data() {
    return {
      getObjectPath: Vue.getObjectPath,
      FMS_URL: AppConfig.FMS_URL,

      /*var for instancias */
      getObjectPath: Vue.getObjectPath,
      inst: "",
      causesReject: "",
      tabs: 0,
      search: "",

      /*var for modals */
      textEditor1: null,
      minAge: null,
      maxAge: null,
      filterGender: null,
      stateMembers: null,
      daysreject: 30,

      /*var for dialogs */
      itemAux: {},
      itemApproveMember: {},
      itemBlockMember: {},
      itemRejectMember: {},
      openRejectDialog: false,
      userListDialog: false,
      /*rules and validations */
      rulesDay: [v => !!v || this.$t("message.daysRequerid")],
      validRejectUser: true,

      /*configs */
      itemsState: [
        {
          name: "Todos",
          code: "T"
        },
        {
          name: "Aprobados",
          code: "2"
        },
        {
          name: "Pendientes",
          code: "0"
        },
        {
          name: "Rechazados",
          code: "4"
        },
        {
          name: "Bloqueados",
          code: "6"
        }
      ],
      messageNoUbication: this.$t("message.noUbication"),

      headers: [
        {
          text: this.$t("message.users"),
          align: "left",
          value: "profile.names"
        },
        {
          text: this.$t("message.dateRegister"),
          value: "registerDate"
        },
        {
          text: this.$t("message.dateApplication"),
          value: "requestDate"
        },
        {
          text: this.$t("message.state"),
          value: "membership.status"
        },
        {
          text: this.$t("message.actions"),
          value: null,
          sortable: false
        }
      ],
      headersMobile: [
        {
          text: this.$t("message.users"),
          align: "left",
          value: "data.profile.names"
        }
      ],
      loadingMembers: false,
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
          "insertUnorderedList",
          "|",
          "picture",
          "tables",
          "|",
          "switchView"
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
        classList: []
      }
    };
  },
  methods: {
    /**
     * Reload the current page information
     */
    reloadData() {
      this.loadingMembers = true;
      this.$store.dispatch("queryItems").finally(result => {
        this.loadingMembers = false;
      });
    },

    /**
     * Validate the minimum age filter
     */
    checkMinAge() {
      if (this.minAge) {
        let numeric = parseInt(this.minAge);
        let numericMax = parseInt(this.maxAge);
        if (
          !numeric ||
          numeric < 13 ||
          numeric > 120 ||
          (numericMax && numeric > numericMax)
        ) {
          this.minAge = null;
        }
      }
      this.dispatchFilter();
    },

    /**
     * Validate the maximum age filter
     */
    checkMaxAge() {
      if (this.maxAge) {
        let numeric = parseInt(this.maxAge);
        let numericMin = parseInt(this.minAge);
        if (
          !numeric ||
          numeric < 13 ||
          numeric > 120 ||
          (numericMin && numeric < numericMin)
        ) {
          this.maxAge = null;
        }
      }
      this.dispatchFilter();
    },

    /**
     * Dispatch the filter options to filter the information
     */
    dispatchFilter() {
      this.$store.commit("setFilters", {
        search: this.search,
        min: this.minAge,
        max: this.maxAge,
        gender: this.filterGender,
        status: this.stateMembers == "T" ? "" : this.stateMembers
      });

      this.reloadData();
    },

    /**
     * clear all filters
     */
    clearFilters() {
      this.search = null;
      this.minAge = null;
      this.maxAge = null;
      this.filterGender = null;
      this.stateMembers = null;
      this.$store.dispatch("clearMembersFilters");
      this.reloadData();
    },
    /**
     * Open modal dialog users list information
     */
    openUserListDialog() {
      this.$refs.userListDialog.openDialog();
    },
    /**
     * Close profile information dialog
     */
    closeUserListDialog() {
      this.$refs.userListDialog.closeDialog();
    },
    /**
     * Open modal dialog with profile information
     */
    openInformationDialog(data) {
      this.itemAux = data;
      this.$refs.informationDialog.openDialog();
    },

    /**
     * Close profile information dialog
     */
    closeInformationDialog() {
      this.$refs.informationDialog.close();
    },

    /**
     * discard changes in modal
     */
    needDiscard() {
      let causes = this.inst.getContent();
      if (this.causes != "" || this.daysreject != "") {
        return true;
      } else {
        return false;
      }
    },

    discardCausesReject() {
      this.$refs.confirmDiscard.close();
      this.openRejectDialog = false;
    },

    /**
     * Open dialog to reject a reject member
     */
    openDialogRejectMember(item) {
      this.inst.setContent("");
      this.itemRejectMember = item;
      this.openRejectDialog = true;
    },

    /**
     * Close dialog to reject member
     */
    closeDialogRejectMember() {
      if (this.needDiscard()) {
        this.$refs.confirmDiscard.openDialog();
      } else {
        this.openRejectDialog = false;
      }
    },

    /**
     * Open dialog to block a memeber
     */
    openDialogBlockMember(item) {
      this.itemBlockMember = item;
      this.$refs.blockMember.openDialog();
    },

    /**
     * method for reject member
     */
    rejectMember(item) {
      this.itemRejectMember = item;
      let causes = this.inst.getContent();
      if (this.$refs.formRejectUser.doValidate()) {
        if (causes != "") {
          let data = {
            user_id: item.user_id,
            causesReject: causes,
            days_unavailable: this.daysreject
          };

          this.$store.dispatch("rejectMember", data).finally(res => {
            this.openRejectDialog = false;
            this.closeInformationDialog();
            this.reloadData();
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.clubUserRejected"),
              type: "success"
            });
          });
        } else {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.erroMemberRejectCauseRequired"),
            type: "error"
          });
        }
      } else {
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.errorInvalidFields"),
          type: "error"
        });
      }
    },

    /**
     * Block an user member
     */
    blockMember(item) {
      let user_id = item.user_id;
      this.$store.dispatch("blockMember", user_id).finally(res => {
        this.$refs.blockMember.close();
        this.closeInformationDialog();
        this.reloadData();
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.clubUserBlocked"),
          type: "success"
        });
      });
    },

    /**
     * Format date for show in table
     */
    GetFormattedDate(dates) {
      let date = new Date(dates);
      date.getFullYear() + "-" + (date.getMonth() + 1) + "-" + date.getDate();
      var month = ("0" + (date.getMonth() + 1)).slice(-2);
      var day = ("0" + date.getDate()).slice(-2);
      var year = date.getFullYear();
      var formattedDate = day + "-" + month + "-" + year;
      return formattedDate;
    },

    /**
     * Calculate the user age
     */
    calculateAge(date) {
      /* Birthdate */
      const birthdate = new Date(date);
      const day = birthdate.getUTCDate();
      const month = birthdate.getUTCMonth();
      const year = birthdate.getUTCFullYear();

      /* Validate the birthdate */
      if (Number.isNaN(day) || Number.isNaN(month) || Number.isNaN(year)) {
        return -1;
      }

      /* Current date */
      const now = new Date();
      const dayNow = now.getUTCDate();
      const monthNow = now.getUTCMonth();
      const yearNow = now.getUTCFullYear();

      /* Precalculate the age by year */
      let age = yearNow - year;

      /* Check if the age was in the past or not */
      if (month > monthNow || (month === monthNow && day > dayNow)) {
        age--;
      }

      return age;
    },

    /**
     * Calculate the user age as string
     */
    calculateAgeStr(date) {
      /* Check for valid age */
      const age = this.calculateAge(date);
      if (age < 13) {
        return "";
      }

      return `${age} ${this.$t("message.years")}`;
    },

    /**approve members businnes club */
    approveMember(item) {
      let user_id = item.user_id;
      this.$store.dispatch("approveMember", user_id).finally(res => {
        this.closeInformationDialog();
        this.reloadData();
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.clubUserApproved"),
          type: "success"
        });
      });
    }
  }
};
</script>
<style scoped>
.membersSubTitle {
  font-size: 12px;
  color: gray !important;
}

.transparent-list {
  background: transparent !important;
}
</style>
