<template>
  <div>
    <br>
    <v-autocomplete
    class="ma-0 pa-0 mb-2"
      :color="currentBussinesClub.theme.primary"
      prepend-inner-icon="mdi-database-search"
      v-model="searchField"
      :items="userProfilesTickets"
      clearable
      hide-details
      hide-selected
      :label="$t('message.searchUser')"
      solo
    ></v-autocomplete>
    <br>
    <span style="color:grey">{{$t('message.ticketList')}}</span>
    <v-data-table
      :headers="headers"
      :items="ticketList"
      :search="searchField"
      style=" table-layout : fixed!important;"
      :rows-per-page-text="$t('message.rowpage')"
    >
      <template v-slot:items="props">
        <td class="noselect">
          <v-layout row wrap align-center>
            <v-avatar size="25" color="purple">
              <img :src="getProfileImage(96, 128,props.item.userProfile)" alt>
            </v-avatar>
            <v-flex xs10 class="pl-1">
              <span>{{getObjectPath(props.item,'userName','')}}</span>
            </v-flex>
          </v-layout>
        </td>
        <td class="noselect">{{getObjectPath(props.item,'topic','')}}</td>
        <td class="noselect">
          <v-layout row wrap align-center justify-center>
            <v-avatar v-if="props.item.agentProfile" size="40" color="purple">
              <img :src="getProfileImage(96, 128,props.item.agentProfile)" alt>
            </v-avatar>
            <span v-else>{{$t('message.unasigned')}}</span>
          </v-layout>
        </td>
        <td
          class="noselect"
        >{{$t('message.ago')}}&nbsp;{{props.item.createdAt |moment("from", "now", true)}}</td>
        <td class="noselect">
          <span>{{$t('message.'+getObjectPath(props.item,'priority',''))}}</span>
        </td>
        <td class="noselect">
          <v-btn small :color="currentBussinesClub.theme.secondary" :ripple="false">
            <span style="color:white">{{$t('message.'+getObjectPath(props.item,'status',''))}}</span>
          </v-btn>
        </td>
        <td class="noselect">
          <v-btn
            @click="openTicketDetail(props.item)"
            small
            icon
            flat
            :color="currentBussinesClub.theme.primary"
          >
            <v-icon>mdi-reply</v-icon>
          </v-btn>
          <v-btn small icon flat :color="currentBussinesClub.theme.primary">
            <v-icon>mdi-account-tie</v-icon>
          </v-btn>
        </td>
      </template>
      <template
        slot="pageText"
        slot-scope="props"
      >{{ props.pageStart }}&nbsp;-&nbsp;{{ props.pageStop }}&nbsp;{{$t('message.of')}}&nbsp;{{ props.itemsLength }}
      </template>
    </v-data-table>
    <v-ticket-detail-dialog ref="ticketDetailDialog"></v-ticket-detail-dialog>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
import Vue from "vue";
import detailTicketDialog from "./ticketDetailDialog";
import AppConfig from "@/constants/AppConfig";
export default {
  components: {
    "v-ticket-detail-dialog": detailTicketDialog
  },
  mounted() {
    this.$store.dispatch("getTickets", this.businessId).then(response => {});
  },
  computed: {
    ...mapGetters([
      "currentBussinesClub",
      "businessId",
      "ticketList",
      "userProfilesTickets"
    ])
  },
  data() {
    return {
      getObjectPath: Vue.getObjectPath,
      headers: [
        {
          text: "Nombres",
          sortable: true,
          align: "left",
          value: "userName",
          class: "userField"
        },
        {
          text: "Asunto",
          sortable: false,
          align: "left",
          value: "topic",
          class: "topicField"
        },
        {
          text: "Asignado a",
          sortable: false,
          align: "center",
          value: "createdAt"
        },
        {
          text: "Fecha",
          sortable: false,
          align: "center",
          value: "createdAt"
        },
        {
          text: "Prioridad",
          sortable: false,
          align: "left",
          value: "priority"
        },
        {
          text: "Estado",
          sortable: true,
          align: "center",
          value: "status"
        },
        {
          text: "Acciones",
          sortable: true,
          align: "center",
          class: "actionsField",
          value:"none"
        }
      ],
      searchField: ""
    };
  },
  methods: {
    openTicketDetail(ticket) {
      this.$store.commit("setSelectedTicket", ticket);
      this.$refs.ticketDetailDialog.show();
    },
    getProfileImage(realSize, size, userProfile) {
      const profile = userProfile;
      return `${AppConfig.FMS_URL}/image/fetch/${Vue.getObjectPath(
        userProfile,
        "photoURL",
        "static/img/perfil_yovirt.png"
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
    }
  }
};
</script>
<style>
.userField {
  min-width: 250px !important;
}
.actionsField {
  min-width: 150px !important;
}
.topicField {
  min-width: 300px !important;
}
</style>
