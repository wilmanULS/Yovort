<template>
  <v-flex>
    <v-layout row wrap>
      <v-flex xs12>
        <span class="vb-cp-title">{{$t('message.vBusinessUsers')}}</span>
        <span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessUsersDetail')}}</span>
      </v-flex>
    </v-layout>
    <v-flex pt-3>
      <v-layout row wrap>
        <v-flex xs12>
          <v-text-field
            ref="name"
            :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
            clearable
            v-model="searchData"
            hide-details
            :color="currentBussinesClub.theme.primary"
            v-bind:label="$t('message.searchUser')"
            required
            @input="searchUsers"
            solo
            prepend-inner-icon="mdi-database-search"
          ></v-text-field>
        </v-flex>
      </v-layout>
    </v-flex>

    <v-flex xs12 class="pb-4">
      <v-layout row wrap>
        <!--   <v-container
          :style="$vuetify.breakpoint.lgAndUp?'margin-left:0px!important':
                             ''"
        >-->
        <v-flex
          xs6
          sm2
          md2
          lg2
          xl1
          v-for="(user, index) in users"
          :key="index"
          style="width:100px;float: left;padding-bottom:30px"
        >
          <v-flex class="transparent" :style="'height: 70px!important;'">
            <v-container>
              <v-layout row wrap>
                <v-flex xs12 class="text-xs-center">
                  <v-avatar
                    style="cursor:pointer"
                    :size="63"
                    :color="currentBussinesClub.theme.primary"
                  >
                    <v-img
                      :ref="'avatar'+index"
                      :src="user.picture"
                      :lazy-src="'/static/img/perfil_yovirt.png'"
                    />
                  </v-avatar>
                </v-flex>
              </v-layout>
              <v-layout row wrap>
                <v-flex xs12>
                  <v-flex style="color:black;">
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on }">
                        <span v-on="on">{{user.names}}</span>
                      </template>
                      <span>{{user.names}}</span>
                    </v-tooltip>
                  </v-flex>
                </v-flex>
              </v-layout>
            </v-container>
          </v-flex>
        </v-flex>

        <!-- 
          <div
            v-for="(user, index) in users"
            :key="index"
            style="width:100px;float: left;padding-bottom:30px"
          >
            <center>
              <v-avatar
                style="cursor:pointer"
                :size="63"
                :color="currentBussinesClub.theme.primary"
              >
                <v-img
                  :ref="'avatar'+index"
                  :src="user.picture"
                  :lazy-src="'/static/img/perfil_yovirt.png'"
                />
              </v-avatar>
              <v-spacer></v-spacer>
              <div style="max-width:130px" class="cut-text">
                <span class="noselect darker-titles" style="font-size:10pt;">{{user.names}}</span>
              </div>
            </center>
        </div>-->
        <!--    </v-container> -->
      </v-layout>
    </v-flex>

    <v-layout row wrap>
      <v-flex xs12>
        <span class="vb-cp-title">{{$t('message.vBusinessUsersAdded')}}</span>
        <span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessUsersAddedDetail')}}</span>
      </v-flex>
    </v-layout>
    <v-flex pt-3>
      <v-layout row wrap>
        <v-flex>
          <v-layout align-end justify-end row fill-height>
            <v-btn
              dark
              :color="currentBussinesClub.theme.primary"
              @click="openCIIUModal()"
            >{{$t('message.vBusinessUsersAdd')}}</v-btn>
          </v-layout>
          <!--           <v-data-table :headers="headers" :items="users" class="elevation-1">
            <template v-slot:items="props">
              <td>{{ props.item.codes[0] }}</td>
              <td class="text-xs-left">{{ props.item.codes[1] }}</td>
              <td class="text-xs-left">{{ props.item.codes[2] }}</td>
              <td class="text-xs-left">{{ props.item.codes[3] }}</td>
              <td class="text-xs-left">{{ props.item.description }}</td>
              <td style="padding:5px;" class="text-xs-center">
                <v-btn
                  icon
                  small
                  @click="openConfirmDeleteCiiu(props.item._id,props.item.description )"
                >
                  <v-icon>mdi-trash-can</v-icon>
                </v-btn>
              </td>
            </template>
          </v-data-table>-->
        </v-flex>
      </v-layout>
    </v-flex>
  </v-flex>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig";
import confirmDialog from "../../components/ySocialHome/confirmDialog";
export default {
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "businessSelected",
      "businessId"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  watch: {
    search(val) {
      /**
       * If searchResult is not null then it means user selects an item, so it tries to save selected user as admin of business
       * otherwise just watch if input continues entering.
       */
      if (this.searchResult) {
        if (this.searchResult) {
          this.$store
            .dispatch("doAddAdministrator")
            .then()
            .dispatch("doAddBusinessAdministrator", {
              businessId: this.currentUserBusiness,
              adminInfo: { userId: this.searchResult }
            })
            .then(() => {
              this.$store.dispatch("doNotification", {
                msg:
                  "Se ha agregado satisfactoriamente un nuevo admnistrador al negocio.",
                type: "success"
              });
            })
            .catch()
            .finally(() => {
              this.searchResult = null;
              this.users = [];
              this.chargeInfoStepOne();
              this.getAdministrators();
              return;
            });
        }
      }
    }
  },
  created() {},
  mounted() {},
  data() {
    return {
      searchData: "",
      search: null,
      users: [],
      adminsList: [],
      headers: [
        {
          text: this.$t("message.vBusinessUsersHeaderName"),
          align: "left",
          sortable: false,
          value: "sector"
        },
        { text: this.$t("message.vBusinessUsersHeaderEmail"), sortable: false },
        {
          text: this.$t("message.vBusinessUsersHeaderLastTime"),
          sortable: false
        },
        { text: this.$t("message.vBusinessUsersHeaderRole"), sortable: false },
        { text: this.$t("message.vBusinessUsersHeaderVCode"), sortable: false },
        { text: this.$t("message.country"), sortable: false },
        { sortable: false }
      ]
    };
  },
  methods: {
    searchUsers() {
      if (this.searchData == "") {
        this.users = [];
      }
      if (this.searchData.length > 3) {
        this.$store
          .dispatch("doSearchUsersByName", {
            page: 1,
            size: 10,
            search: this.searchData
          })
          .then(res => {
            this.users = [];
            let usersResult = res.data.data.page;
            /**
             * If there is data on response then add this data to items which will show on autocomplete
             * displaying name, avatar and if of users.
             */
            if (usersResult && usersResult.length > 0) {
              usersResult.forEach(user => {
                this.users.push({
                  names:
                    user.personal_info.names +
                    " " +
                    user.personal_info.surnames,
                  picture: `${AppConfig.FMS_URL}/image/fetch/${
                    user.personal_info.picture
                  }?tx=resize(w:64,h:64)&username=${user.personal_info.names +
                    " " +
                    user.personal_info.surnames}&color=${
                    user.personal_info.color
                  }&size=64`,

                  id: user.userId
                });
              });
            }
          })
          .catch();
      }
    }
  }
};
</script>

