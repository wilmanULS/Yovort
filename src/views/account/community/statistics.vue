<template>
  <v-container class="ma-0 pa-0" grid-list-xs>
    <v-layout class="ma-0 pa-0" row wrap>
      <v-flex xs12 sm12 md6 lg3 xl3 pb-3>
        <v-layout row wrap class="pa-0" style="border:1px #c2d1df solid">
          <v-flex xs12 class="border-1p pa-3" style="border-bottom:1px #c2d1df solid">
            <v-layout row wrap>
              <v-flex xs12>
                <v-layout align-center justify-center row wrap fill-height style="position: relative;">
                  <v-flex xs12>
                    <span class="title-card">{{$t('message.summary')}}</span>
                    <div class="customBadge" :style="'background-color:'+currentBussinesClub.theme.primary">
                      <center>{{$t('message.planInactive')}}</center>
                    </div>
                    <v-tooltip class="customInfo" top>
                      <v-icon style="cursor:pointer" small color="#c2d1df" slot="activator">info</v-icon>
                      <span>{{$t('message.actualPosition')}}</span>
                    </v-tooltip>
                  </v-flex>
                </v-layout>
              </v-flex>
              <v-flex xs12>
                <span class="subtitle-Card">{{$t('message.currentPosition')}}</span>
              </v-flex>
              <v-flex xs12>
                <span class="detail-card">{{currentPosition[0]}}</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex xs12 class="border-1p pa-3" style="border-bottom:1px #c2d1df solid">
            <v-layout row wrap>
              <v-flex xs12>
                <v-layout align-center justify-center row wrap fill-height style="position: relative;">
                  <v-flex xs12>
                    <span class="title-card">{{$t('message.expiration')}}</span>
                    <v-tooltip class="customInfo" top>
                      <v-icon style="cursor:pointer" small color="#c2d1df" slot="activator">info</v-icon>
                      <span>{{$t('message.expirationTimeMessage')}}</span>
                    </v-tooltip>
                  </v-flex>
                </v-layout>
              </v-flex>
              <v-flex xs12>
                <span class="subtitle-Card">{{$t('message.expirationTime')}}</span>
              </v-flex>
              <v-flex xs12>
                <vac v-if="expirationDate" :end-time="expirationDate">
                  <span
                    class="detail-card"
                    slot="process"
                    slot-scope="{ timeObj }"
                  >{{ `${timeObj.d}D ${timeObj.h}H ${timeObj.m}M ${timeObj.s}S` }}</span>
                  <span class="subtitle-Card" slot="finish">0 D 0 H 0 M 0 S</span>
                </vac>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex xs12 class="border-1p pa-3">
            <v-layout row wrap>
              <v-flex xs12>
                <v-layout align-center justify-center row wrap fill-height style="position: relative;">
                  <v-flex xs12>
                    <span class="title-card">{{$t('message.avaliable')}}</span>
                    <v-tooltip class="customInfo" top>
                      <v-icon style="cursor:pointer" color="#c2d1df" small slot="activator">info</v-icon>
                      <span>{{$t('message.avaliableStatics')}}</span>
                    </v-tooltip>
                  </v-flex>
                </v-layout>
              </v-flex>
              <v-flex xs12>
                <span class="subtitle-Card">{{$t('message.ultimatePositionAvaliable')}}</span>
              </v-flex>
              <v-flex xs12>
                <span class="detail-card">{{lastPositionAvailable}}</span>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-flex>
      <v-flex xs12 sm12 md6 lg4 xl4 pb-3 :class="$vuetify.breakpoint.lgAndUp?'px-3':'pl-3'" >
        <v-layout row wrap style="border:1px #c2d1df solid">
          <v-flex xs6 class="pa-3">
            <v-layout
              align-center
              justify-center
              row
              wrap
              fill-height
              style="border:1px #c2d1df solid"
            >
              <v-flex xs12 text-xs-center>
                <span class="detail-card">0</span>
              </v-flex>
              <v-flex xs12 text-xs-center>
                <span
                  class="subtitle"
                  :style="'color:'+currentBussinesClub.theme.primary"
                >{{$t('message.currentAccumulated').toUpperCase() }}</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex xs6 class="pa-3">
            <v-layout
              align-center
              justify-center
              row
              wrap
              fill-height
              style="border:1px #c2d1df solid"
            >
              <v-flex xs12 text-xs-center>
                <span class="detail-card">0</span>
              </v-flex>
              <v-flex xs12 text-xs-center>
                <span
                  class="subtitle"
                  :style="'color:'+currentBussinesClub.theme.secondary"
                >{{$t('message.totalRevenue').toUpperCase()}}</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex xs6 class="pa-3">
            <v-layout
              align-center
              justify-center
              row
              wrap
              fill-height
              style="border:1px #c2d1df solid"
            >
              <v-flex xs12 text-xs-center>
                <span class="detail-card">0</span>
              </v-flex>
              <v-flex xs12 text-xs-center>
                <span
                  class="subtitle"
                  :style="'color:'+currentBussinesClub.theme.primary"
                >{{$t('message.timeToCollect').toUpperCase()}}</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex xs6 class="pa-3">
            <v-layout
              align-center
              justify-center
              row
              wrap
              fill-height
              style="border:1px #c2d1df solid"
            >
              <v-flex xs12 text-xs-center>
                <span class="detail-card">0</span>
              </v-flex>
              <v-flex xs12 text-xs-center>
                <span
                  class="subtitle"
                  :style="'color:'+currentBussinesClub.theme.secondary"
                >{{$t('message.activeUsers').toUpperCase()}}</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex xs6 class="pa-3">
            <v-layout
              align-center
              justify-center
              row
              wrap
              fill-height
              style="border:1px #c2d1df solid"
            >
              <v-flex xs12 text-xs-center>
                <span class="detail-card">0</span>
              </v-flex>
              <v-flex xs12 text-xs-center>
                <span
                  class="subtitle"
                  :style="'color:'+currentBussinesClub.theme.primary"
                >{{$t('message.lastPaymentMade').toUpperCase()}}</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex xs6 class="pa-3">
            <v-layout
              align-center
              justify-center
              row
              wrap
              fill-height
              style="border:1px #c2d1df solid"
            >
              <v-flex xs12 text-xs-center>
                <span class="detail-card">0</span>
              </v-flex>
              <v-flex xs12 text-xs-center>
                <span
                  class="subtitle"
                  :style="'color:'+currentBussinesClub.theme.secondary"
                >{{$t('message.inactiveUsers').toUpperCase()}}</span>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-flex>
      <v-flex xs12 sm12 md12 lg5 xl5 pb-3 style="z-index: 1!important;">
        <v-layout
        style="height: 100%;"
          row
          wrap
          :style="'border:1px '+currentBussinesClub.theme.primary+' solid'"
        >
          <v-container grid-list-xs class="no-padding" style="height: 100%;">
              <v-yovirt-map style="height: 100%;" :keyMap="keyMap"></v-yovirt-map>
          </v-container>
        </v-layout>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import { mapGetters } from "vuex";
import { EventBus } from "../../../eventBus.js";
import YovirtWorld from "./yovirtWorld";

export default {
    props:{
        keyMap: Number,
    },
  computed: {
    ...mapGetters(["userProfile", "currentBussinesClub"])
  },
  created() {
    EventBus.$on("resize", event => {
      this.resize();
    });
    this.dataLineChart = [
      [new Date(), 5],
      [1368174456, 4],
      ["2017-01-01 00:00:00 UTC", 7]
    ];
  },
  components: {
      'v-yovirt-map': YovirtWorld,
  },
  mounted() {
    this.$store
      .dispatch("getStaticsBtree")
      .then(res => {
        this.currentPosition =
          res.data.data.position == -1 ? "-" : res.data.data.position;
        this.expirationDate =
          res.data.data.expiration == -1 ? "0" : res.data.data.expiration;
        this.lastPositionAvailable =
          res.data.data.nextPosition == -1 ? "-" : res.data.data.nextPosition;
      })
      .catch(err => {
        console.error(err);
      });
  },
  data() {
    return {
      currentPosition: 0,
      expirationDate: 0,
      lastPositionAvailable: 0,
      dataMap: [
        ["United States", 44],
        ["Canada", 23],
        ["Mexico", 18],
        ["Guatemala", 12],
        ["Spain", 21]
      ],
      dataLineChart: "",
      items: [
        {
          title: "Semanal"
        },
        {
          title: "Mensual"
        },
        {
          title: "Anual"
        }
      ],
      search: "",
      headers: [
        {
          text: "Dessert (100g serving)",
          align: "left",
          sortable: false,
          value: "name"
        },
        {
          text: "Calories",
          value: "calories"
        },
        {
          text: "Fat (g)",
          value: "fat"
        },
        {
          text: "Carbs (g)",
          value: "carbs"
        },
        {
          text: "Protein (g)",
          value: "protein"
        },
        {
          text: "Iron (%)",
          value: "iron"
        }
      ],
      options: {
        width: 600,
        height: 600,
        chartArea: {
          left: 10,
          top: 10,
          bottom: 0,
          height: "100%"
        },
        colorAxis: {
          colors: ["red", "blue", "green", "yellow", "grey"]
        },
        displayMode: "regions"
      },
      desserts: [
        {
          value: false,
          name: "Frozen Yogurt",
          calories: 159,
          fat: 6.0,
          carbs: 24,
          protein: 4.0,
          iron: "1%"
        },
        {
          value: false,
          name: "Ice cream sandwich",
          calories: 237,
          fat: 9.0,
          carbs: 37,
          protein: 4.3,
          iron: "1%"
        },
        {
          value: false,
          name: "Eclair",
          calories: 262,
          fat: 16.0,
          carbs: 23,
          protein: 6.0,
          iron: "7%"
        },
        {
          value: false,
          name: "Cupcake",
          calories: 305,
          fat: 3.7,
          carbs: 67,
          protein: 4.3,
          iron: "8%"
        },
        {
          value: false,
          name: "Gingerbread",
          calories: 356,
          fat: 16.0,
          carbs: 49,
          protein: 3.9,
          iron: "16%"
        },
        {
          value: false,
          name: "Jelly bean",
          calories: 375,
          fat: 0.0,
          carbs: 94,
          protein: 0.0,
          iron: "0%"
        },
        {
          value: false,
          name: "Lollipop",
          calories: 392,
          fat: 0.2,
          carbs: 98,
          protein: 0,
          iron: "2%"
        },
        {
          value: false,
          name: "Honeycomb",
          calories: 408,
          fat: 3.2,
          carbs: 87,
          protein: 6.5,
          iron: "45%"
        },
        {
          value: false,
          name: "Donut",
          calories: 452,
          fat: 25.0,
          carbs: 51,
          protein: 4.9,
          iron: "22%"
        },
        {
          value: false,
          name: "KitKat",
          calories: 518,
          fat: 26.0,
          carbs: 65,
          protein: 7,
          iron: "6%"
        }
      ]
    };
  },
  methods: {
    dateConvert() {
      return parseInt(this.expirationDate);
    },
    resize() {
      this.dataMap = [
        ["United States", 44],
        ["Canada", 23],
        ["Mexico", 18],
        ["Guatemala", 12],
        ["Spain", 21]
      ];
    },
    changeColor(data) {
      if (data === "Semanal") {
        this.dataLineChart = [
          [new Date(), 5],
          [1368174456, 4],
          ["2017-01-01 00:00:00 UTC", 6]
        ];
      }
      if (data === "Mensual") {
        this.dataLineChart = [
          [new Date(), 3],
          [1368174456, 4],
          ["2017-01-01 00:00:00 UTC", 4]
        ];
        /*       this.Semanal='gray'
        this.Mensual= this.currentBussinesClub.theme.primary
        this.Anual= 'gray'

        this.SemanalText='grey'
        this.MensualText= 'white'
        this.AnualText= 'grey' */
      }
      if (data === "Anual") {
        this.dataLineChart = [
          [new Date(), 1],
          [1368174456, 4],
          ["2017-01-01 00:00:00 UTC", 8]
        ];
        /*       this.Semanal='gray'
        this.Mensual= 'gray'
        this.Anual= this.currentBussinesClub.theme.primary

        this.SemanalText='grey'
        this.MensualText='grey'
        this.AnualText='white' */
      }
    }
  }
};
</script>

<style scoped>
.detail-card {
  color: #22303c;
  font-size: 18pt;
}

.padding-left-title {
  padding-left: 0px !important;
}

.cardLeft {
  padding-left: 10px;
}

.cards-padding-x {
  padding-left: 10px;
  padding-right: 10px;
}

.statics-flex-padding {
  padding: 0px 0px 0px 0px;
}

.title-card {
  color: #2c3e52;
  font-size: 14pt;
}
.subtitle {
  font-size: 9pt;
}
.subtitle-Card {
  color: #869baf;
  font-size: 10pt;
  font-weight: bold;
}
.padding-5-r {
  padding-right: 0px !important;
  padding-left: 5px !important;
}

/* Landscape phones and portrait tablets */
.padding-2-x {
  padding-right: 5px !important;
  padding-left: 2px !important;
}

.padding-2-left {
  padding-left: 15px !important;
}

.padding-2-right {
  padding-right: 0px !important;
}

.padding-10-top {
  padding-top: 0px !important;
}

.border-1px {
  border-style: solid;
  border-width: 1px;
  border-color: #cfd8dc;
}

.bg-color-grey {
  background-color: #f4f7fa !important;
}

.borderCards {
  border-style: solid;
  border-width: 1px;
  border-color: #cfd8dc;
}

.imgWorld {
  opacity: 0.6;
  filter: alpha(opacity=50);
  /* For IE8 and earlier */
}

@media screen and (max-width: 1265px) {
  .padding-left-title {
    padding-left: 10px !important;
  }

  .cards-padding-x {
    padding-left: 0px;
    padding-right: 0px;
  }
}

@media screen and (max-width: 992px) {
  .cards-padding-x {
    padding-left: 0px;
    padding-right: 0px;
  }

  .profile-flex-padding {
    padding: 0px 0px 10px 0px !important;
  }

  .padding-5-r {
    padding-right: 0px !important;
    padding-left: 0px !important;
  }

  .padding-2-x {
    padding-right: 0px !important;
    padding-left: 0px !important;
  }

  .padding-2-left {
    padding-left: 2px !important;
  }

  .padding-2-right {
    padding-right: 10px !important;
  }

  .padding-10-top {
    padding-top: 10px !important;
  }
}

/* Portrait phones and smaller */
@media screen and (max-width: 600px) {
  .profile-flex-padding {
    padding: 0px 0px 10px 0px !important;
  }

  .padding-5-r {
    padding-right: 0px !important;
    padding-left: 0px !important;
  }

  .padding-10-top {
    padding-top: 10px !important;
  }

  .padding-2-left {
    padding-left: 2px !important;
  }

  .padding-2-right {
    padding-right: 0px !important;
  }
}

.customBadge {
  width: auto;
  min-height: 25px;
  padding-top: 1.5px;
  padding-right: 10px;
  padding-left: 10px;
  border-radius: 5px 5px 5px 5px;
  font-size: 13px;
  font-weight: 400;
  color: white;
  position: absolute;
  right: 25px;
}

.customInfo{
    position: absolute;
    right: 0;
}

.countries{
  font-size: 16pt;
  color: #869baf;
}
</style>
