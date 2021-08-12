<template>
  <v-flex  :style="'background-color:'+backgroundColor">
    <v-flex md12 lg12 style="padding-bottom: 20px;padding-top: 5px;">
      <span :style="'padding-left: 20px;color:'+colorTittle">{{tittle}}
        
      </span>
    </v-flex>

    <carousel  @touchmove="prevent"  autoWidth :dots="false" :margin="20" :items="1" :navText="['&larr;','&rarr;']">
      <v-flex
        v-for="(vector,index) in data"
        :key="index"
        style="height:250px;width: 250px; margin-right:0px;display:inline-block;"
      >
        <v-flex class="hover-card">
          <v-img style="border-radius: 4%;" :src="vector.imgUrl" height="180px" width="250px"></v-img>
          <div class="card">
            <div class="card-content">
              <div class="content">
                <div class="centerContent" style="margin-top:10px">
                  <div class="circleBase centerContent">
                    <i class="fas fa-share" style="color: white;margin-top: 30%"></i>
                  </div>
                </div>
                <div class="centerContent" style="margin-top:20px;">
                  <div style="display:inline-block;">
                    <i class="far fa-eye" style="color: #3aadad"></i>
                    <span style="color: white">{{' '+vector.views}}</span>
                  </div>
                  <div style="display:inline-block;margin-left:10px">
                    <i class="far fa-heart" style="color: #f26d37"></i>
                    <span style="color: white">{{' '+vector.likes}}</span>
                  </div>
                </div>
                <div class="centerContent" style="margin-top:15px;">
                  <v-chip @click="changePosition(data)" color="#2c353a" text-color="#72848d" style="height: 38px;">
                    <i class="fas fa-crosshairs" style="cursor: pointer;"></i>
                    <span style="margin-left: 6px;cursor: pointer;">{{$t('message.onMap')}}</span>
                  </v-chip>
                </div>
              </div>
            </div>
          </div>
          <v-flex class="post-container-results">
            <v-flex class="post-thumb-results">
              <img src="/static/img/finder_icon_test.svg" class="imgPopUp-results">
            </v-flex>
            <v-flex class="post-content-results">
              <h3
                class="post-title-results"
                :style="'color:'+colorTittle+''"
              >{{vector.businessName}}</h3>
              <div style="margin-top: -3px;">
                <i
                  class="far fa-clock"
                  :style="'margin-right: 5px;color:'+currentBussinesClub.theme.secondary+''"
                ></i>
                <span
                  :style="'font-size: 13px;color:'+currentBussinesClub.theme.secondary+''"
                >Abierto ahora</span>
              </div>
              <div style="    margin-top: -4px;margin-left: 1px;">
                <i class="fas fa-map-marker-alt" :style="'margin-right: 5px;color:'+colorTittle+''"></i>
                <span :style="'font-size: 13px;color:'+colorTittle+''">{{vector.address}}</span>
              </div>
            </v-flex>
          </v-flex>
        </v-flex>
      </v-flex>
    </carousel>
  </v-flex>
</template>
<script>
import Vue from "vue";

import { mapGetters } from "vuex";

import carousel from "vue-owl-carousel";


import { EventBus } from "../../eventBus";

export default {
  methods:{
     prevent (event) {
        event.preventDefault()
        event.stopPropagation()
    },
    changePosition(location){
      EventBus.$emit("changeLocationMap",location)
    }
  },
  components: {
    carousel
  },
  mounted() {
    this.$el.style.setProperty("--colorButtom", this.colorTittle);
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
  props: {
    data: Array,
    backgroundColor: String,
    tittle: String,
    colorTittle: String
  },
  data() {
    return {
      arrows: ["ss", "dds"],
      showPrev: false,
      showNext: true
    };
  }
};
</script>
<style>
:root {
  --colorButtom: #ffffff;
}

.centerContent {
  text-align: center;
  margin: auto;
}
.circleBase {
  border-radius: 50%;
  width: 50px;
  height: 50px;
  border: 3px solid #ffffff;
  vertical-align: middle;
}

.centerHV {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.post-container-results {
  margin: 5px 20px 0 0;
  overflow: hidden;
  width: 280px;
}
.post-thumb-results {
  float: left;
  width: 50px;
  margin-left: -5px;
}
.post-thumb-results img {
  display: block;
}
.post-content-results {
  margin-left: 50px;
}
.post-title-results {
  font-weight: 400;
  font-size: 20px;
  margin-top: 0px;
  margin-bottom: 0px;
  color: #55646d;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  max-width: 169px;
}
.imgPopUp-results {
  width: 50px;
  height: 50px;
  border-radius: 50%;
}

/* HoverStyle */

.hover-card {
  width: 250px;
  margin-bottom: 30px;
}
.hover-card .card {
  position: relative;
  text-align: left;
}
.hover-card .card .cat-flag {
  position: absolute;
  top: 8px;
  left: -5px;
  z-index: 2;
  font-size: 13px;
  font-weight: 400;
  padding: 3px 5px 3px 5px;
  color: #fff;
  border-top-right-radius: 3px;
  border-bottom-right-radius: 3px;
  background-color: #2266aa;
}
.hover-card .card .cat-flag:before,
.hover-card .card .cat-flag:after {
  content: "";
  display: block;
  position: absolute;
  right: 100%;
  border-right: 10px solid #2266aa;
}
.hover-card .card .cat-flag:before {
  top: 0;
  border-bottom: 13px solid transparent;
  border-top: 0 solid transparent;
}
.hover-card .card .cat-flag:after {
  bottom: 0;
  border-top: 13px solid transparent;
  border-bottom: 0 solid transparent;
}
.hover-card .card .card-img {
  text-align: center;
  background-color: #000;
}
.hover-card .card .card-img img {
  transition: transform 0.7s;
  opacity: 0.8;
}
.hover-card .card:before {
  content: "";
  position: absolute;
  bottom: 0;
  right: 0;
  left: 0;
  z-index: 1;
  height: 70px;
  display: block;
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr="#00000000", endColorstr="#000000", GradientType=0 );
  /* IE6-9 */
}
.hover-card .card .card-content {
  border-radius: 5px;
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 3;
  overflow: hidden;
}
.hover-card .card .card-content .content {
  transition: transform 0.4s;
  transform: translateY(100%);
  /* Permalink - use to edit and share this gradient: http://colorzilla.com/gradient-editor/#000000+0,000000+100&0+0,1+100 */
  background: -moz-linear-gradient(top, #000000ab, rgba(0, 0, 0, 0.781) 100%);
  /* FF3.6-15 */
  background: -webkit-linear-gradient(
    top,
    #000000ab,
    rgba(0, 0, 0, 0.781) 100%
  );
  /* Chrome10-25,Safari5.1-6 */
  background: linear-gradient(to bottom, #000000ab, rgba(0, 0, 0, 0.781) 100%);
  /* W3C, IE10+, FF16+, Chrome26+, Opera12+, Safari7+ */
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr="#00000000", endColorstr="#000000", GradientType=0 );
  /* IE6-9 */
  height: 180px;
}
.hover-card .card .card-content .title {
  color: #fff;
  font-weight: 600;
  font-size: 24px;
  transform: translateY(-100%);
  transition: transform 0.4s;
  margin-top: 0;
  margin-bottom: 0;
  padding: 0 15px 15px;
}
.hover-card .card .card-content .description {
  padding: 0 15px;
  color: #fff;
  font-size: 13px;
  margin: 0;
}
.hover-card .card:hover .card-content .content {
  padding-bottom: 15px;
  transform: translateY(0);
}
.hover-card .card:hover .card-content .title {
  transform: translateY(0);
}
/* CarouselStyle */
.owl-carousel .owl-stage-outer {
  margin-left: 22px;
  margin-right: 8px;
}
.owl-prev {
  background-color: transparent !important;
  border-radius: 50% !important;
  width: 30px !important;
  height: 30px !important;
  border: 2px solid var(--colorButtom) !important;
  padding-top: 1px !important;
  padding-right: 8px !important;
  padding-left: 3px !important;
  position: absolute;
  top: -45px;
  right: 70px;
  color: var(--colorButtom) !important;
  line-height: 19px;
  font-size: 24px !important;
}
.owl-next {
  background-color: transparent !important;
  border-radius: 50% !important;
  width: 30px !important;
  height: 30px !important;
  border: 2px solid var(--colorButtom) !important;
  padding-top: 1px !important;
  padding-left: 3px !important;
  position: absolute;
  top: -45px;
  right: 20px;
  color: var(--colorButtom) !important;
  line-height: 19px;
  font-size: 24px !important;
}
.owl-carousel 
{
-ms-touch-action: pan-y !important;
touch-action: pan-y !important;
}
</style>
