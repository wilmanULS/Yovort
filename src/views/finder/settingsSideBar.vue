<template>
  <v-flex
    id="mySidenav"
    class="sidenav"
    :style="$vuetify.breakpoint.smAndDown?'height: calc(100vh + 70vh)':'height: 100%;'"
  >
    <v-layout wrap row>
      <v-flex
        md12
        lg12
        xs12
        sm12
        @click="openNav()"
        :style="'cursor: pointer;height:44px;font-size: 22px;background-color:'+currentBussinesClub.theme.secondary"
      >
        <span>
          <i
            class="fas fa-sliders-h centerElement"
            style="padding-top: 2px;color:white; cursor: pointer"
          ></i>
        </span>
      </v-flex>
      <v-layout wrap row style="margin-right: 30px;margin-left: 30px;">
        <v-flex md12 lg12 xs12 sm12>
          <v-text-field class="wrapperClass" color="white" :label="$t('message.search')"></v-text-field>
        </v-flex>
        <v-flex md12 lg12 xs12 sm12>
          <v-select
            attach
            class="wrapperSelect"
            v-model="category"
            :items="items"
            item-text="category"
            item-value="id"
            :menu-props="{ maxHeight: '400' }"
            :color="currentBussinesClub.theme.secondary"
            :label="$t('message.category')"
          >
            <template slot="append">
              <div class="selectDivider"></div>
              <v-icon
                style="padding-right: 2px;"
                :color="white"
              >expand_more</v-icon>
              <!-- <v-icon>search</v-icon> -->
            </template>
          </v-select>
        </v-flex>
        <v-flex md12 lg12 xs12 sm12>
          <v-select
            attach
            class="wrapperSelect"
            v-model="location"
            :items="itemsLocation"
            item-text="location"
            item-value="id"
            :menu-props="{ maxHeight: '400' }"
            :color="currentBussinesClub.theme.secondary"
            :label="$t('message.location')"
          >
            <template slot="append">
              <div class="selectDivider"></div>
              <v-icon
                style="padding-right: 2px;"
                :color="white"
              >gps_fixed</v-icon>
              <!-- <v-icon>search</v-icon> -->
            </template>
          </v-select>
        </v-flex>
        <v-flex md12 lg12 xs12 sm12>
          <v-subheader
            style="text-transform: uppercase;height: 17px;color:#596972;font-size: 12px;"
            class="pl-0"
          >{{$t('message.radio')}}</v-subheader>
          <v-flex
            :style="sliderRadio==0?'padding-left: 7px !important;':'padding-left: 0px !important;'"
            pl-1
            md12
            lg12
            xs12
            sm12
          >
            <v-slider
              track-color="#596972"
              :class="sliderRadio==0?'wrapperSlider':'wrapperSliderActive'"
              v-model="sliderRadio"
              :thumb-label="sliderRadio==0?'':'always'"
              :color="currentBussinesClub.theme.primary"
            >
              <template slot="thumb-label">
                <span small>
                  <label style="font-size: 14px;">{{sliderRadio}}</label>
                  <label
                    :style="'font-size: 14px;font-weight: 1000;color:'+currentBussinesClub.theme.primary"
                    small
                  >{{' '+'Km'}}</label>
                </span>
              </template>
            </v-slider>
          </v-flex>
        </v-flex>
        <v-flex md12 lg12 xs12 sm12>
          <v-subheader
            style="text-transform: uppercase;height: 17px;color:#596972;font-size: 12px;"
            class="pl-0"
          >{{$t('message.tags')}}</v-subheader>
          <v-flex md12 lg12 xs12 sm12>
            <v-layout wrap row>
              <v-flex md9 lg9 xs9 sm9>
                <v-checkbox
                  :color="currentBussinesClub.theme.secondary"
                  label="Parqueo"
                  style="height: 30px;"
                  :class="parking?'wrapperCheckBox':'wrapperCheckBox wrapperCheckBoxInactive'"
                  on-icon="fas fa-square"
                  v-model="parking"
                  value
                ></v-checkbox>
              </v-flex>
              <v-flex style="right:0;text-align: right;margin-top: 2.6vh;" md3 lg3 xs3 sm3>
                <span :style="parking?'color:white':'color:#899ca7'">12</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex md12 lg12 xs12 sm12>
            <v-layout wrap row>
              <v-flex md9 lg9 xs9 sm9>
                <v-checkbox
                  :color="currentBussinesClub.theme.secondary"
                  label="Wifi"
                  style="height: 30px;"
                  :class="wifi?'wrapperCheckBox':'wrapperCheckBox wrapperCheckBoxInactive'"
                  on-icon="fas fa-square"
                  v-model="wifi"
                  value
                ></v-checkbox>
              </v-flex>
              <v-flex style="right:0;text-align: right;margin-top: 2.6vh;" md3 lg3 xs3 sm3>
                <span :style="wifi?'color:white':'color:#899ca7'">36</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex md12 lg12 xs12 sm12>
            <v-layout wrap row>
              <v-flex md9 lg9 xs9 sm9>
                <v-checkbox
                  :color="currentBussinesClub.theme.secondary"
                  label="Pets Friendly"
                  style="height: 30px;"
                  :class="petsF?'wrapperCheckBox':'wrapperCheckBox wrapperCheckBoxInactive'"
                  on-icon="fas fa-square"
                  v-model="petsF"
                  value
                ></v-checkbox>
              </v-flex>
              <v-flex style="right:0;text-align: right;margin-top: 2.6vh;" md3 lg3 xs3 sm3>
                <span  :style="petsF?'color:white':'color:#899ca7'">2</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex md12 lg12 xs12 sm12>
            <v-layout wrap row>
              <v-flex md9 lg9 xs9 sm9>
                <v-checkbox
                  :color="currentBussinesClub.theme.secondary"
                  label="Valet Parking"
                  style="height: 30px;"
                  :class="valetParking?'wrapperCheckBox':'wrapperCheckBox wrapperCheckBoxInactive'"
                  on-icon="fas fa-square"
                  v-model="valetParking"
                  value
                ></v-checkbox>
              </v-flex>
              <v-flex style="right:0;text-align: right;margin-top: 2.6vh;" md3 lg3 xs3 sm3>
                <span :style="valetParking?'color:white':'color:#899ca7'">8</span>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex md12 lg12 xs12 sm12>
            <v-layout wrap row>
              <v-flex md9 lg9 xs9 sm9>
                <v-checkbox
                  :color="currentBussinesClub.theme.secondary"
                  label="Aire Acondicionado"
                  style="height: 30px;"
                  :class="air?'wrapperCheckBox':'wrapperCheckBox wrapperCheckBoxInactive'"
                  on-icon="fas fa-square"
                  v-model="air"
                  value
                ></v-checkbox>
              </v-flex>
              <v-flex style="right:0;text-align: right;margin-top: 2.6vh;" md3 lg3 xs3 sm3>
                <span :style="air?'color:white':'color:#899ca7'">3</span>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-flex>
        <v-flex class="clearFilter" md12 lg12 xs12 sm12>
          <span style="cursor:pointer" @click="clearFilter()" :style="'color:'+currentBussinesClub.theme.secondary"><i style="padding-right:10px" class="fas fa-redo-alt"></i>Limpiar Filtros</span>
        </v-flex>
      </v-layout>
    </v-layout>
  </v-flex>
</template>
<script>
import Vue from "vue";

import { mapGetters } from "vuex";

import { EventBus } from "../../eventBus";
import { icon } from "leaflet";

export default {
  beforeDestroy() {
    EventBus.$off("showSettingsButton");
  },
  computed: {
    ...mapGetters(["currentBussinesClub"]),
    currentUserBusiness: {
      get() {
        if (this.$store.getters.currentUserBusiness != "")
          return this.$store.getters.currentUserBusiness;
      }
    }
  },
  mounted() {
    let el = this;
    EventBus.$on("openSettingsSideBar", () => {
      el.openNav();
    });
  },
  methods: {
    clearFilter(){
    },
    openNav() {
      var e = document.getElementById("mySidenav");
      if (e.style.width == "250px") {
        e.style.width = "0px";
        EventBus.$emit("showSettingsButton");
      } else {
        e.style.width = "250px";
      }
    }
  },
  data() {
    return {
      parking: true,
      wifi: false,
      petsF: false,
      valetParking: false,
      air: false,
      sliderRadio: 0,
      category: {},
      items: [
        { category: "Hoteles", id: "1" },
        { category: "Centros Nocturnos", id: "2" },
        { category: "Restaurantes", id: "3" }
      ],
      location: {},
      itemsLocation: [
        { location: "Prueba 1", id: "1" },
        { location: "Prueba 12", id: "2" },
        { location: "Prueba 1221asdasdasdasdas", id: "3" }
      ]
    };
  }
};
</script>
<style less>
:root {
  --colorButtom: #ffffff;
}

.clearFilter{
  padding-top: 10px;
  padding-right: 32px;
  text-align: right;
  position: absolute;
  bottom: 0;
  width: 100%;
}

.wrapperSlider .v-slider__thumb-label {
  background-color: transparent !important;
  height: 12px !important;
  width: 50px !important;
}
.wrapperSlider .v-slider__thumb {
  border: 3px solid #596972 !important;
}

.wrapperSliderActive .v-slider__thumb {
  border: none;
}

.wrapperSliderActive .v-slider__thumb-label {
  background-color: transparent !important;
  height: 12px !important;
  width: 50px !important;
}

.wrapperCheckBox .v-input__control i {
  color: #596972;
}
.wrapperCheckBox .v-input__control label {
  color: white;
}
.wrapperCheckBox .v-input__control .v-input__slot {
  width: 19vh;
}

.wrapperCheckBoxInactive .v-input__control label {
  color: #899ca7;
}

/* .wrapperClass .v-input__control{
  padding-left: 7px !important;
} */
.selectDivider {
  border-left: 1px solid #495358;
  border-right: 1px solid #3e484e;
  height: 3vh;
  position: absolute;
  right: 30px;
  top: 5px;
}
.wrapperClass .v-input__slot .v-label {
  color: white !important;
}

.wrapperClass .v-text-field__slot input {
  color: white !important;
}

.wrapperClass .v-input__slot:before {
  border-style: solid;
  border-width: 1.5px !important;
  border-color: #404a52 !important;
}

.wrapperSelect .v-select__slot {
  background: #353f45 !important;
  border-radius: 7px !important;
  border: 1px solid #353f45 !important;
}
.wrapperSelect .v-input__slot:before {
  background: none !important;
  border-radius: 0px !important;
  border-color: transparent !important;
}
.wrapperSelect .v-input__slot:after {
  background: none !important;
  border-radius: 0px !important;
  border-color: transparent !important;
}

.wrapperSelect .v-input__slot .v-label {
  color: white !important;
  padding-left: 10px;
}

.wrapperSelect .v-input__slot .v-label--active {
  top: 1px !important;
  padding-left: 0px;
}

.wrapperSelect .v-input__slot .v-select__slot .v-select__selections {
  color: white !important;
  padding-left: 10px;
}
.sidenav {
  height: 100%;
  width: 0;
  position: absolute;
  z-index: 1;
  top: 0px;
  right: 0px;
  background-color: #2c353a;
  overflow-x: hidden;
  transition: 0.5s;
}/*
.sidenav a {
  padding: 8px 8px 8px 32px;
  text-decoration: none;
  font-size: 25px;
  color: #818181;
  display: block;
  transition: 0.3s;
}
.sidenav a:hover,
.offcanvas a:focus {
  color: #f1f1f1;
} */
.closebtn {
  position: absolute;
  top: 0;
  right: 25px;
  font-size: 36px !important;
  margin-left: 50px;
}
@media screen and (max-height: 450px) {
  .sidenav {
    padding-top: 15px;
  }
  .sidenav a {
    font-size: 18px;
  }
}
</style>
