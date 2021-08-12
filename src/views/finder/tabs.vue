<template>
  <v-layout row wrap style="height:0px">
    <v-flex md12>
      <v-tabs class="wrapperTabs" v-model="tabs" light color="white" show-arrows grow>
        <v-flex md2 class="centerContent">
          <span>60 negocios encontrados</span>
        </v-flex>
        <v-tabs-slider :color="currentBussinesClub.theme.primary"></v-tabs-slider>
        <v-tab style="color: #94a8b4" v-for="i in categories" :key="i" :href="'#tab-' + i">{{i}}</v-tab>

        <v-tabs-items touchless>
          <v-tab-item :transition="false" :reverse-transition="false" v-for="i in categories" :key="i" :value="'tab-' + i">
            <v-layout row wrap>
              <v-flex style="width:25vh">
                <v-results :data="bussinessPremium" v-bind:backgroundColor="'#2c353a'" v-bind:tittle="$t('message.businessPremium')" v-bind:colorTittle="'white'"></v-results>
              </v-flex>
              <v-flex style="width:75vh"> 
                <v-results :data="bestResults" v-bind:backgroundColor="'#e9edf2'" v-bind:tittle="$t('message.bestResults')" v-bind:colorTittle="'#525556'" ></v-results>
              </v-flex>
            </v-layout>
          </v-tab-item>
        </v-tabs-items>
      </v-tabs>
    </v-flex>
  </v-layout>
</template>
<script>
import Vue from "vue";
import { getCurrentAppLayout } from "Helpers/helpers";
import { mapGetters } from "vuex";
import "swiper/dist/css/swiper.css";

import { swiper, swiperSlide } from "vue-awesome-swiper";

import Slick from "vue-slick";

import Results from "./results";

export default {
  watch:{
    tabs(val){

    }

  },
  components: {
    Slick,
    swiper,
    swiperSlide,
    "v-results": Results
  },
  mounted() {
    this.$el.style.setProperty(
      "--colorPrimary",
      this.currentBussinesClub.theme.primary
    );
    this.$el.style.setProperty(
      "--colorSecondary",
      this.currentBussinesClub.theme.secondary
    );
  },
  data() {
    return {
      bussinessPremium:[
        {
          businessName: "La casa del lobo",
          lat: "",
          long: "",
          imgUrl: "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views:"10234",
          likes:"1034"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views:"26",
          likes:"1023"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views:"26",
          likes:"1023"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views:"26",
          likes:"1023"
        }
      ],
      bestResults:[
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views:"56",
          likes:"1234"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views:"56",
          likes:"134"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views:"26",
          likes:"104"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://img.peru21.pe/files/ec_article_multimedia_gallery/uploads/2017/10/25/59f0fad00f10a.jpeg",
          address: "Quito, Ecuador",
          icon: "",
          views:"6",
          likes:"104"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://img.peru21.pe/files/ec_article_multimedia_gallery/uploads/2017/10/25/59f0fad00f10a.jpeg",
          address: "Quito, Ecuador",
          icon: "",
          views:"26",
          likes:"234"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views:"25",
          likes:"104"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl: "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views:"256",
          likes:"1004"
        }],
      tabs:'tab-0',
      slickOptions: {
        slidesToShow: 3
      },
      categories:["Todos","Recientes","Hoteles","Centros Nocturnos","Patios de Autos","Academias de Baile", "Ropa y Accesorios", "Hospitales"],
      currentView: "",
      auxTab: 0,
      canSave: true,
      nameUser: "",
      email: "",
      tabs: 0,
      tabsButtons: 0,
      nextTab: 0,
      searchPanel: true,
      drawer: true,
      mini: true,
      right: true
    };
  },
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  methods: {
    }
}
</script>
<style less>
:root {
  --colorSecondary: #ffffff;
  --colorPrimary: #ffffff;
  --bottomVar: 0;
}

.wrapperTabs .v-tabs__item {
  font-size: 12px;
}
/* .v-tabs__div {
    color: #94a8b4 !important;
} */
.wrapperTabs .v-tabs__item--active {
  color: white;
  position: relative;
  z-index: 100;
}
.wrapperTabs .v-tabs__slider {
  height: 23px;
  border-radius: 5px;
  top: 0;
  left: 0;
  z-index: -100;
  width: 90%;
  margin: 0 auto;
  position: relative;
  top: 50%;
  transform: perspective(1px) translateY(-50%);
  margin-bottom: 2px;
}
.centerContent {
  color: #67757d;
  margin: auto;
  text-align: center;
  vertical-align: middle;
}
</style>

