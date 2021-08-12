<template>
  <div>
    <v-card>
      <v-card-actions>
        <span>{{$t('message.tickets')}}</span>
        <v-spacer></v-spacer>
        <v-text-field
          v-model="searchField"
          :label="$t('message.search')"
          append-icon="search"
          single-line
          hide-details
        ></v-text-field>
      </v-card-actions>
      <v-data-table 
      :headers="headers"
      :items="ticketList"
      :search="searchField"
      :rows-per-page-text="$t('message.rowpage')"
       >
        <template v-slot:items="props">
          <td @click="openTicketDetail(props.item)" class="noselect">{{props.item.ticketNumber}}</td>
          <td @click="openTicketDetail(props.item)" class="noselect">
            <v-chip :color="getPriorityColor(props.item.priority)" small>
              <span style="color:white">{{$t('message.'+props.item.priority)}}</span>
            </v-chip>
          </td>
          <td @click="openTicketDetail(props.item)" class="noselect">
            <v-chip small :color="currentBussinesClub.theme.primary">
              <span style="color:white">{{props.item.objectCategory[0].name}}</span>
            </v-chip>
          </td>
          <td @click="openTicketDetail(props.item)" class="noselect">{{props.item.topic}}</td>
          <td @click="openTicketDetail(props.item)" class="noselect">
            <v-chip small :color="currentBussinesClub.theme.secondary">
              <span style="color:white">{{$t('message.'+props.item.status)}}</span>
            </v-chip>
          </td>
        </template>
        <template
          slot="pageText"
          slot-scope="props"
        >{{ props.pageStart }}&nbsp;-&nbsp;{{ props.pageStop }}&nbsp;{{$t('message.of')}}&nbsp;{{ props.itemsLength }}</template>
      </v-data-table>
    </v-card>
    <v-ticket-detail-dialog ref="ticketDetailDialog"></v-ticket-detail-dialog>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
import detailTicketDialog from "./ticketDetailDialog";
export default {
  data() {
    return {
      searchField: "",
      headers: [
        {
          text: "Número de Ticket",
          sortable: true,
          align: "left",
          value: "ticketNumber"
        },
        {
          text: "Prioridad",
          sortable: false,
          align: "left",
          value: "priority"
        },
        {
          text: "Categoría",
          sortable: false,
          align: "left",
          value: "objectCategory[0].name"
        },
        {
          text: "Asunto",
          sortable: false,
          align: "left",
          value: "topic"
        },
        {
          text: "Estado",
          sortable: true,
          align: "left",
          value: "status"
        }
      ]
    };
  },
  components: {
    "v-ticket-detail-dialog": detailTicketDialog
  },
  computed: {
    ...mapGetters(["ticketList", "userProfile", "currentBussinesClub"])
  },
  mounted() {
    this.$store
      .dispatch("getTickets", this.currentBussinesClub.clubId)
      .then(response => {});
  },
  methods: {
    openTicketDetail(ticket) {
      this.$store.commit("setSelectedTicket", ticket);
      this.$refs.ticketDetailDialog.show();
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
.noselect {
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  cursor: pointer;
}
</style>

