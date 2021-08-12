<template>
<div style="height: 100%; min-height: 300px;">
    <l-map :key="keyId" :zoom="zoom" :center="center" :options="mapOptions" style="min-height: 300px;height: 100%" @update:center="centerUpdate" @update:zoom="zoomUpdate">
        <l-tile-layer :url="url" :attribution="attribution" />
        <div v-if="showMyPosition">
            <l-marker :lat-lng="myPosition">
                <l-tooltip>
                    <div>
                        {{$t('message.currentPosition')}}<br>
                        {{myPositionSource}}
                    </div>
                </l-tooltip>
            </l-marker>
            <l-circle :lat-lng="myPosition" :radius="50" />
        </div>
        <div v-if="locations.length > 0">
            <l-marker-cluster>
                <l-marker v-for="l in locations" :key="l.id" :lat-lng="l.pos">
                </l-marker>
            </l-marker-cluster>
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
} from 'vue2-leaflet';
import * as Vue2Leaflet from 'vue2-leaflet';
import {
    latLng,
    Icon,
    icon
} from 'leaflet';
import * as Vue2LeafletMarkercluster from 'vue2-leaflet-markercluster';
import Vue from 'vue';
import iconUrl from 'leaflet/dist/images/marker-icon.png';
import shadowUrl from 'leaflet/dist/images/marker-shadow.png';
import LocationService from 'Services/LocationService';

export default {
    name: 'Example',
    props: {
        keyMap: Number,
    },
    watch: {
        keyMap(value) {
            Vue.nextTick(() => {
                this.keyId = 'key' + value;
            });
        }
    },
    components: {
        LMap,
        LTileLayer,
        LMarker,
        LCircle,
        LPopup,
        LTooltip,
        'l-marker-cluster': Vue2LeafletMarkercluster,
    },
    data() {
        return {
            keyId: 'key',
            zoom: 11,
            center: L.latLng(47.413220, -1.219482),
            url: 'https://{s}.tile.osm.org/{z}/{x}/{y}.png',
            attribution: '&copy; <a href="https://www.vexpot.com">VEXPOT</a>, <a href="https://osm.org/copyright">OpenStreetMap</a> contributors',
            currentZoom: 11.5,
            currentCenter: L.latLng(47.413220, -1.219482),
            showParagraph: false,
            mapOptions: {
                zoomSnap: 0.5
            },

            /* Handle my position information */
            myPosition: L.latLng(47.413220, -1.219482),
            showMyPosition: false,
            myPositionSource: '',

            /* Handle other locations information */
            locations: [],
        };
    },
    mounted() {
        const that = this;
        /* Fetch the geolocalization from web browser */
        if (navigator.geolocation) {
            navigator.geolocation.watchPosition((position) => {
                that.center = L.latLng(position.coords.latitude, position.coords.longitude);
                that.myPosition = L.latLng(position.coords.latitude, position.coords.longitude);
                that.myPositionSource = that.$t('message.positionFromBrowser');
                that.showMyPosition = true;
            }, () => {
                that.fetchPosition();
            });
        } else {
            that.fetchPosition();
        }

        /* Fetch the remain localization information */
        LocationService.fetchLocations()
            .then(response => {
                that.locations = response.data.data.locations.filter(value => {
                    return value.lat && value.lon;
                }).map(value => {
                    return {
                        id: value.locId,
                        pos: L.latLng(value.lat, value.lon),
                    }
                });
            }).catch(err => {
                that.$store.dispatch("doNotification", {
                    msg: that.$t('message.errorLoadingPosition'),
                    type: 'error'
                });
            });
    },
    methods: {
        fetchPosition() {
            /* Try to fetch localization from service request */
            LocationService.fetchCurrentLocation()
                .then(response => {
                    if (response.data.data) {
                        this.center = L.latLng(response.data.data.lat, response.data.data.lon);
                        this.myPosition = L.latLng(response.data.data.lat, response.data.data.lon);
                        this.myPositionSource = this.$t('message.positionFromProvider');
                        this.showMyPosition = true;
                        return;
                    }
                    console.error("Geolocation is not supported.");
                    this.showMyPosition = false;
                    this.$store.dispatch("doNotification", {
                        msg: this.$t('message.errorLoadingCurrentPosition'),
                        type: 'warning'
                    });
                }).catch(() => {
                    console.error("Geolocation is not supported.");
                    this.showMyPosition = false;
                    this.$store.dispatch("doNotification", {
                        msg: this.$t('message.errorLoadingCurrentPosition'),
                        type: 'warning'
                    });
                });
        },
        zoomUpdate(zoom) {
            this.currentZoom = zoom;
        },
        centerUpdate(center) {
            this.currentCenter = center;
        },
    }
};
</script>
<style>
@import "~leaflet/dist/leaflet.css";
@import "~leaflet.markercluster/dist/MarkerCluster.css";
@import "~leaflet.markercluster/dist/MarkerCluster.Default.css";
</style>
