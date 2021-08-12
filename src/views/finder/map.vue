<template>
  <div id="mapFinder" @touchmove="prevent" :style="$vuetify.breakpoint.smAndDown?'height: 100%; min-height: calc(100vh - 270px)':'height: 100%; min-height: calc(100vh - 470px)'">
    <l-map
       ref="mapFinder"
       id="mapFinder"
       st
      :key="keyId"
      :zoom="zoom"
      :center="center"
      :options="mapOptions"
      :style="$vuetify.breakpoint.smAndDown?'height: 100%; min-height: calc(100vh - 270px);z-index: 0':'min-height: calc(100vh - 470px);height: 100%;z-index: 0;'"
      @update:center="centerUpdate"
      @update:zoom="zoomUpdate"
    >
      <l-tile-layer :url="url" :attribution="attribution"/>
      <!--  <div>
            <l-marker >
                <l-tooltip>
                    <div>
                        {{$t('message.currentPosition')}}<br>
                        {{myPositionSource}}
                    </div>
                </l-tooltip>
                 <l-popup :content="text"></l-popup>
            </l-marker>
            <l-circle :lat-lng="myPosition" :radius="50" />
      </div>-->
      <div v-if="locations.length > 0">
        <!--  <l-marker-cluster> -->
        <l-marker
          v-for="l in locations"
          :key="l.businessName"
          :lat-lng="getCoordenates(l.lat, l.long)"
          :icon="setCustomizeMarker()"
          @click="showDetails(l)"
          @popupclose="closeDetails()"
        >
          <l-popup
            :content="setCustomizePopUp( l.businessName,l.latLng,l.long,l.imgUrl,l.address)"
            :options="customOptions"
          ></l-popup>
        </l-marker>
        <!-- </l-marker-cluster> -->
      </div>
    </l-map>
  </div>
</template>

<script>
import {
  LMap,
  LTileLayer,
  LMarker,
  LPopup,
  LCircle,
  LTooltip
} from "vue2-leaflet";
import * as Vue2Leaflet from "vue2-leaflet";
import { latLng, Icon, icon } from "leaflet";
import * as Vue2LeafletMarkercluster from "vue2-leaflet-markercluster";

import Vue from "vue";

import { mapGetters } from "vuex";
import iconUrl from "leaflet/dist/images/marker-icon.png";
import shadowUrl from "leaflet/dist/images/marker-shadow.png";
import LocationService from "Services/LocationService";
import { EventBus } from '../../eventBus';

export default {
  name: "Example",
  beforeDestroy(){
    EventBus.$off("showDetailsLocation")
    EventBus.$off("hideDetailsLocation")

  },
  props: {
    keyMap: Number
  },
  watch: {
    keyMap(value) {
      Vue.nextTick(() => {
        this.keyId = "key" + value;
      });
    }
  },
  computed: {
    ...mapGetters(["currentBussinesClub"]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    },
    currentUserBusiness: {
      get() {
        if (this.$store.getters.currentUserBusiness != "")
          return this.$store.getters.currentUserBusiness;
      }
    }
  },
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LCircle,
    LPopup,
    LTooltip,
    "l-marker-cluster": Vue2LeafletMarkercluster
  },
  data() {
    return {
      customOptions: {
        maxWidth: "500",
        maxHeight: "120",
        className: "custom"
      },
      text:
        "Mozilla Toronto Offices<br/><img src='http://joshuafrazier.info/images/maptime.gif' alt='maptime logo gif' width='350px'/>",
      keyId: "key",
      zoom: 8,
      center: L.latLng(-41.5183, 174.78081),
      url: "https://{s}.tile.osm.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="https://www.vexpot.com">VEXPOT</a>, <a href="https://osm.org/copyright">OpenStreetMap</a> contributors',
      currentZoom: 11.5,
      currentCenter: L.latLng(-40.99497, 174.50808),
      showParagraph: false,
      mapOptions: {
        zoomSnap: 0.5,
        zoomControl: true
      },

      /* Handle my position information */
      myPosition: L.latLng(47.41322, -1.219482),
      showMyPosition: false,
      myPositionSource: "",

      /* Handle other locations information */
      locations: [
        {
          businessName: "7C6B07",
          lat: "-40.99497",
          long: "174.50808",
          imgUrl: "",
          address: "",
          icon: ""
        },
        {
          businessName: "7C6B38",
          lat: "-41.30269",
          long: "173.63696",
          imgUrl: "",
          address: "",
          icon: ""
        },
        {
          businessName: "7C6CA1",
          lat: "-41.49413",
          long: "173.5421",
          imgUrl: "",
          address: "",
          icon: ""
        },
        {
          businessName: "7C6CA2",
          lat: "-40.98585",
          long: "174.50659",
          imgUrl: "",
          address: "",
          icon: ""
        },
        {
          businessName: "C81D9D",
          lat: "-40.93163",
          long: "173.81726",
          imgUrl: "",
          address: "",
          icon: ""
        },
        {
          businessName: "C82009",
          lat: "-41.5183",
          long: "174.78081",
          imgUrl: "",
          address: "",
          icon: ""
        },
        {
          businessName: "C82081",
          lat: "-41.42079",
          long: "173.5783",
          imgUrl: "",
          address: "",
          icon: ""
        },
        {
          businessName: "Don David Internacional",
          lat: "-42.08414",
          long: "173.96632",
          imgUrl: "",
          address: "",
          icon: ""
        },
        {
          businessName: "C820B6",
          lat: "-41.51285",
          long: "173.53274",
          imgUrl: "",
          address: "",
          icon: ""
        }
      ]
    };
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
    let el=this;
    EventBus.$on("changeLocationMap",(location)=>{
         /* this.$refs.mapFinder.setCenter(L.latLng(-0.180653, -78.467834)) */

         this.center=L.latLng(-0.180653, -78.467834)
         this.zoom=8;
      /*    mymap.panTo(L.LatLng(45.1190042, 25.740205)); */
         this.$refs.mapFinder.mapObject.options.crs.latLngToPoint(L.latLng(-0.180653, -78.467834),11)
    })
    /* Fetch the geolocalization from web browser */
    /* if (navigator.geolocation) {
            navigator.geolocation.watchPosition(function(position) {
                this.center = L.latLng(position.coords.latitude, position.coords.longitude);
                this.myPosition = L.latLng(position.coords.latitude, position.coords.longitude);
                this.myPositionSource = this.$t('message.positionFromBrowser');
                this.showMyPosition = true;
            }, () => {
                this.fetchPosition();
            });
        } else {
            this.fetchPosition();
        } */

    /* Fetch the remain localization information */
    /* LocationService.fetchLocations()
            .then(response => {
                this.locations = response.data.data.locations.filter(value => {
                    return value.lat && value.lon;
                }).map(value => {
                    return {
                        id: value.locId,
                        pos: L.latLng(value.lat, value.lon),
                    }
                });
            }).catch(err => {
                this.$store.dispatch("doNotification", {
                    msg: this.$t('message.errorLoadingPosition'),
                    type: 'error'
                });
            }); */
  },
  methods: {
     prevent (event) {
        event.preventDefault()
        event.stopPropagation()
    },
    showDetails(location){
      EventBus.$emit("showDetailsLocation",location)
    },
    closeDetails(){
      EventBus.$emit("hideDetailsLocation")
    },
    setCustomizeMarker(){
        return L.icon({
                        iconUrl: '/static/img/finder_marker_house.png',
                        iconSize:     [50, 50], // size of the icon

                    });
    },
    setCustomizePopUp(businessName, latLng, long, imgUrl, address) {
      //var text=businessName+"<br/><img src='http://joshuafrazier.info/images/maptime.gif' alt='maptime logo gif' width='350px'/>";
      var text =
        "<div class='post-container'><div class='post-thumb'>"+
        "<img src='https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg' class='imgPopUp'/>"+
        "</div><div class='post-content'><h3 class='post-title'>" +
        businessName +
        "</h3><div ><div class='inlineDiv'><i class='far fa-clock' style='margin-right: 5px;color:" +
        this.currentBussinesClub.theme.secondary +
        "'></i><span style='color:" +
        this.currentBussinesClub.theme.secondary +
        "'>Abierto ahora</span></div>"+
        "<div class='inlineDiv'><i  style='margin-right: 5px;color:#89959c;' class='fas fa-map-marker-alt'></i><span style='color:#89959c'>Quito, Ecuador</span></div>"+
        "</div><div style='margin-top:5px' ><div class='circleDiv'></div><div class='circleDiv marginsCircleDiv'></div><div class='circleDiv marginsCircleDiv'></div>"+
        "<div class='circleDiv marginsCircleDiv'><a href=''><i class='fas fa-share-alt' style='color:#38a6a6'></i></a></div>"+
        "</div></div></div>";
      return text;
    },
    getCoordenates(lat, lon) {
      return L.latLng(lat, lon);
    },
    fetchPosition() {
      /* Try to fetch localization from service request */
      LocationService.fetchCurrentLocation()
        .then(response => {
          if (response.data.data) {
            this.center = L.latLng(
              response.data.data.lat,
              response.data.data.lon
            );
            this.myPosition = L.latLng(
              response.data.data.lat,
              response.data.data.lon
            );
            this.myPositionSource = this.$t("message.positionFromProvider");
            this.showMyPosition = true;
            return;
          }
          console.error("Geolocation is not supported.");
          this.showMyPosition = false;
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.errorLoadingCurrentPosition"),
            type: "warning"
          });
        })
        .catch(() => {
          console.error("Geolocation is not supported.");
          this.showMyPosition = false;
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.errorLoadingCurrentPosition"),
            type: "warning"
          });
        });
    },
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
      this.zoom=zoom
    },
    centerUpdate(center) {
      this.currentCenter = center;
      this.center=center
    }
  }
};
</script>
<style less>
:root {
  --colorSecondary: #ffffff;
  --colorPrimary: #ffffff;
  --bottomVar: 0;
}

@import "~leaflet/dist/leaflet.css";
@import "~leaflet.markercluster/dist/MarkerCluster.css";
@import "~leaflet.markercluster/dist/MarkerCluster.Default.css";

.custom .leaflet-popup-tip,
.custom .leaflet-popup-content-wrapper {
  background: #ffffff;
  color: black;
}
.custom .leaflet-popup-content {
  margin-left: 5px;
  margin-bottom: 5px;
  margin-right: 0px;
  margin-top: 0px;
  height: 120px;
}
.post-container {
  margin: 5px 20px 0 0;
  overflow: auto;
}
.post-thumb {
  float: left;
}
.post-thumb img {
  display: block;
}
.post-content {
  margin-left: 160px;
}
.post-title {
  font-weight: bold;
  font-size: 200%;
  margin-top: 10px;
  color: #55646d;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  max-width: 250px;
}
.imgPopUp {
  width: 140px;
  height: 120px;
  border-radius: 4%;
}
.circleDiv {
  background: #e9f0f3;
  width: 50px;
  height: 50px;
  border-radius: 64px;
  display: inline-block;
  text-align: center;
}
.marginsCircleDiv {
  margin-left: 10px;
}
.inlineDiv {
  display: inline-block;
  width: calc(50% - 2px);
}
.leaflet-popup-close-button {
  color: var(--colorSecondary) !important;
}
.circleDiv:before {
  content: "";
  display: inline-block;
  height: 100%;
  vertical-align: middle;
  margin-right: -0.25em; /* Adjusts for spacing */
}
.circleDiv a {
  color: white;
  font-size: 2em;
  margin: auto;
  text-align: center;
  vertical-align: middle;
}
</style>
