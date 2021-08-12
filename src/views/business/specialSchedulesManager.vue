<template>
  <v-popup-dialog
    ref="formSpecialSchedules"
    :model="open"
    :title="$t('message.vBusinessSchedulesManagment')"
    showRequired
    @onClose="closeDialog"
    @onSave="saveSchedule"
  >
    <v-container style="padding-left: 0px;" pt-4>
      <v-form ref="specialSchedules" onSubmit="return false;" lazy-validation>
        <v-layout
          wrap
          row
          :class="$vuetify.breakpoint.mdAndUp?'scheduleContainer':'scheduleContainerMobile'"
        >
          <v-flex xs12 sm12 lg12 md12 xl12 v-for="(schedule,index) in schedules" :key="index">
            <v-layout wrap row>
              <v-flex xs12 sm12 lg12 md12 xl12>
                <v-layout wrap row>
                  <div
                    v-if="!$vuetify.breakpoint.mdAndUp"
                    :class="$vuetify.breakpoint.mdAndUp?'switchStatus pl-3':' flex xs12 sm12'"
                  ></div>
                  <v-flex :class="$vuetify.breakpoint.mdAndUp?'md8 lg4 xl3':'xs11 sm11'">
                    <v-menu
                      :ref="'menuDay'+index"
                      :close-on-content-click="false"
                      v-model="schedule.menuD"
                      :nudge-right="40"
                      :return-value.sync="schedule.day"
                      lazy
                      transition="scale-transition"
                      offset-y
                      full-width
                      max-width="290px"
                      min-width="290px"
                    >
                      <v-text-field
                        :color="currentBussinesClub.theme.primary"
                        slot="activator"
                        v-model="schedule.day"
                        :label="$t('message.vBusinessSelectDate')"
                        readonly
                      ></v-text-field>
                      <v-date-picker
                        v-model="schedule.day"
                        :min="calculateMinDate()"
                        :color="currentBussinesClub.theme.primary"
                        :locale="selectedLocale.locale"
                        @change="saveDay('menuDay'+index,schedule.day)"
                      ></v-date-picker>
                    </v-menu>
                  </v-flex>
                  <v-flex xs1 sm1 v-if="!$vuetify.breakpoint.mdAndUp">
                    <div style="line-height: 65px;padding-left: 7px;">
                      <v-icon
                        :style="'cursor:pointer;'"
                        :title="$t('message.vBusinessDeleteSchedule')"
                        @click="removeItem(index)"
                      >mdi mdi-delete</v-icon>
                    </div>
                  </v-flex>
                  <v-layout
                    v-if="!$vuetify.breakpoint.mdAndUp"
                    wrap
                    row
                    style="justify-content: flex-end;height: 32px;"
                  >
                    <v-flex sm3 xs3 lg6 xl6 style="text-align: right;">
                      <span
                        style="margin-top: 18px;color:black"
                      >{{schedule.status?$t('message.open'):$t('message.closed')}}</span>
                    </v-flex>
                    <v-flex sm2 xs2 style="padding-left: 16px;">
                      <v-switch
                        v-model="schedule.status"
                        :color="currentBussinesClub.theme.primary"
                      ></v-switch>
                    </v-flex>
                  </v-layout>
                  <div
                    v-if="$vuetify.breakpoint.mdAndUp"
                    :class="$vuetify.breakpoint.mdAndUp?'switchStatus pl-3':' flex xs12 sm12'"
                    :style="$vuetify.breakpoint.lgAndDown?'width:139px':'width:186px'"
                  >
                    <v-layout wrap row>
                      <v-flex md9 sm3 xs3 lg6 xl6>
                        <span
                          style="margin-top: 18px;color:black"
                        >{{schedule.status?$t('message.open'):$t('message.closed')}}</span>
                      </v-flex>
                      <v-flex md3 sm4 xs4 lg4>
                        <v-switch
                          v-model="schedule.status"
                          :color="currentBussinesClub.theme.primary"
                        ></v-switch>
                      </v-flex>
                    </v-layout>
                  </div>
                  <v-flex :class="$vuetify.breakpoint.mdAndUp?'md11 lg5 xl6':'xs12 sm12'">
                    <v-layout wrap row>
                      <v-flex sm12 xs12 lg5 md5 xl5>
                        <v-menu
                          :ref="'menu'+index"
                          :close-on-content-click="false"
                          v-model="schedule.menuO"
                          :nudge-right="40"
                          :return-value.sync="schedule.open"
                          lazy
                          transition="scale-transition"
                          offset-y
                          full-width
                          max-width="290px"
                          min-width="290px"
                        >
                          <v-text-field
                            :color="currentBussinesClub.theme.primary"
                            slot="activator"
                            v-model="schedule.open"
                            :label="$t('message.openAt')"
                            readonly
                          ></v-text-field>
                          <v-time-picker
                            :color="currentBussinesClub.theme.primary"
                            v-if="schedule.menuO"
                            v-model="schedule.open"
                            full-width
                            @change="saveHour('menu'+index,schedule.open)"
                          ></v-time-picker>
                        </v-menu>
                      </v-flex>
                      <div v-if="$vuetify.breakpoint.mdAndUp">
                        <span
                          style="font-size: 40px;font-weight: 900;margin-right: 10px;margin-left: 10px;"
                        >-</span>
                      </div>
                      <v-flex sm12 xs12 lg5 md5 xl5>
                        <v-menu
                          :ref="'menuC'+index"
                          :close-on-content-click="false"
                          v-model="schedule.menuC"
                          :nudge-right="40"
                          :return-value.sync="schedule.close"
                          lazy
                          transition="scale-transition"
                          offset-y
                          full-width
                          max-width="290px"
                          min-width="290px"
                        >
                          <v-text-field
                            :color="currentBussinesClub.theme.primary"
                            slot="activator"
                            v-model="schedule.close"
                            :label="$t('message.closeAt')"
                            readonly
                          ></v-text-field>
                          <v-time-picker
                            :color="currentBussinesClub.theme.primary"
                            v-if="schedule.menuC"
                            v-model="schedule.close"
                            full-width
                            @change="saveHour('menuC'+index,schedule.close)"
                          ></v-time-picker>
                        </v-menu>
                      </v-flex>
                    </v-layout>
                  </v-flex>
                  <div v-if="$vuetify.breakpoint.mdAndUp" style="line-height: 65px;">
                    <v-icon
                      :style="'cursor:pointer;'"
                      :title="$t('message.vBusinessDeleteSchedule')"
                      @click="removeItem(index)"
                    >mdi mdi-delete</v-icon>
                  </div>
                </v-layout>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
        <v-flex
          :class="$vuetify.breakpoint.mdAndUp?'scheduleContainer':'scheduleContainerMobile'"
          lg12
          md12
          sm12
          xs12
          xl12
        >
          <span
            :style="'cursor:pointer;color:'+currentBussinesClub.theme.primary"
            @click="addNewSchedule"
          >{{$t('message.vBusinessAddNewSchedule')}}</span>
        </v-flex>
      </v-form>
    </v-container>
  </v-popup-dialog>
</template>

<script>
import Vue from "vue";
import { mapGetters } from "vuex";
export default {
  watch: {},
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale"
    ])
  },
  data() {
    return {
      open: false,
      schedules: [],
      order: [
        "Domingo",
        "Lunes",
        "Martes",
        "Miércoles",
        "Jueves",
        "Viernes",
        "Sábado",
        ""
      ],
      days: [
        {
          day: "Lunes",
          disabled: false
        },
        {
          day: "Martes",
          disabled: false
        },
        {
          day: "Miércoles",
          disabled: false
        },
        {
          day: "Jueves",
          disabled: false
        },
        {
          day: "Viernes",
          disabled: false
        },
        {
          day: "Sábado",
          disabled: false
        },
        {
          day: "Domingo",
          disabled: false
        }
      ]
    };
  },
  methods: {
    /**
     * Calculate the minimal date allowed to register date
     */
    calculateMinDate() {
      let maxDate = new Date();
      maxDate.setFullYear(maxDate.getFullYear());
      const month = ("0" + (maxDate.getMonth() + 1)).slice(-2);
      const date = ("0" + maxDate.getDate()).slice(-2);
      const year = maxDate.getFullYear();
      const formattedDate = year + "-" + month + "-" + date;
      return formattedDate;
    },
    saveSchedule() {
      this.open = false;
    },
    openDialog() {
      this.open = true;
    },
    closeDialog() {
      this.open = false;
      this.schedules = [];
      this.days = [
        {
          day: "Lunes",
          disabled: false
        },
        {
          day: "Martes",
          disabled: false
        },
        {
          day: "Miércoles",
          disabled: false
        },
        {
          day: "Jueves",
          disabled: false
        },
        {
          day: "Viernes",
          disabled: false
        },
        {
          day: "Sábado",
          disabled: false
        },
        {
          day: "Domingo",
          disabled: false
        }
      ];
    },
    addNewSchedule() {
      var newSchedule = {
        open: "",
        menuO: false,
        menuC: false,
        close: "",
        status: true,
        day: "",
        subSchedules: []
      };
      this.schedules.push(newSchedule);
      let el = this;
      this.schedules.sort(function(a, b) {
        var dateA = new Date(a.day),
          dateB = new Date(b.day);
        return dateA - dateB;
      });
    },
    saveHour(ref, hour) {
      this.$refs[ref][0].save(hour);
    },
    saveDay(ref, day){
      this.$refs[ref][0].save(day);
      setTimeout(() => {
      this.schedules.sort(function(a, b) {
        var dateA = new Date(a.day);
        var dateB = new Date(b.day);
        return dateA - dateB;
      });
          
      }, 1);
    },
    removeItem(index) {
      let el = this;
      var result = this.days.find(obj => {
        return obj.day === el.schedules[index].day;
      });
      if (typeof result != "undefined") {
        result.disabled = false;
      }
      this.schedules.splice(index, 1);
      this.schedules.sort(function(a, b) {
        var dateA = new Date(a.day);
        var dateB = new Date(b.day);
        return dateA - dateB;
      });
    },
    disableItem(event, schedule) {
      if (schedule.day != "") {
        var result = this.days.find(obj => {
          return obj.day === schedule.day;
        });
        result.disabled = false;
      }
      schedule.day = event;
      let el = this;
      setTimeout(() => {
        var result = el.days.find(obj => {
          return obj.day === schedule.day;
        });
        result.disabled = true;
        el.schedules.sort(function(a, b) {
          return el.order.indexOf(a.day) - el.order.indexOf(b.day);
        });
      }, 1);
      /* var result = this.days.find(obj => {
        return obj.day === day
        })
        result.disabled=true;
        console.log(result) */
    }
  }
};
</script>

<style>
.subScheduleContainerMobile {
  margin-left: 0px !important;
  margin-right: 0px !important;
}
.subScheduleContainer {
  margin-left: 24px !important;
  margin-right: 0px !important;
}
.wrapperSelectSchedule .v-select__selection--disabled {
  color: black !important;
}
.scheduleContainer {
  margin: 0 auto;
  padding: 0px 10px 10px 43px;
  position: relative;
}
.scheduleContainerMobile {
  margin: 0 auto;
  padding: 0px;
  position: relative;
}
.switchStatus {
  width: 139px;
  margin-right: 0px;
  margin-left: 0px;
}

.wrapperButtonSchedules .v-btn--disabled {
  background-color: rgb(255, 0, 0) !important;
}
</style>
