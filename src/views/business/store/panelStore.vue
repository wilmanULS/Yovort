<template>
  <v-layout row :class="$vuetify.breakpoint.mdAndUp?'ml-3 mt-5 mr-5':' mt-4'">
    <v-flex xs3 class="profile-flex-padding" v-if="$vuetify.breakpoint.mdAndUp">
      <v-card>
        <v-card-actions class="dialog-title linear-gradient" card dark>
          <v-btn
            flat
            icon
            dark
            @click="$emit('onClose')"
            style="margin-right: 2%!important;margin-left:4%!important"
          >
            <v-icon>mdi-home-circle</v-icon>
          </v-btn>

          <v-card-title style="padding-left: 0!important">Ir a la lista de negocios</v-card-title>
        </v-card-actions>
        <v-list>
          <v-layout row wrap style="margin-left: 22px;">
            <v-flex xs12>
              <span>{{"Store name"}}</span>
            </v-flex>
            <v-flex xs12>
              <span>Quito</span>
            </v-flex>
            <v-flex xs12>
              <span>Modificado: {{"Businnes date update"}}</span>
            </v-flex>
          </v-layout>
          <v-list-tile class="sidebarItem" avatar @click="SwitchView(0)">
            <v-list-tile-avatar>
              <v-icon class="sidebar-icon">mdi mdi-apps</v-icon>
            </v-list-tile-avatar>
            <v-list-tile-content>
              <v-list-tile-title class="sidebar-title">Detalle de la Tienda</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-list-tile class="sidebarItem" avatar @click="SwitchView(1)">
            <v-list-tile-avatar>
              <v-icon class="sidebar-icon">mdi mdi-settings</v-icon>
            </v-list-tile-avatar>
            <v-list-tile-content>
              <v-list-tile-title class="sidebar-title">Personalización</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-list-tile class="sidebarItem" avatar @click="SwitchView(2)">
            <v-list-tile-avatar>
              <v-icon class="sidebar-icon">mdi mdi-image-multiple</v-icon>
            </v-list-tile-avatar>
            <v-list-tile-content>
              <v-list-tile-title class="sidebar-title">Banner</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-list-tile class="sidebarItem" avatar @click="SwitchView(3)">
            <v-list-tile-avatar>
              <v-icon class="sidebar-icon">mdi mdi-store</v-icon>
            </v-list-tile-avatar>
            <v-list-tile-content>
              <v-list-tile-title class="sidebar-title">Producto</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </v-list>
      </v-card>
    </v-flex>
    <v-flex :xs9="$vuetify.breakpoint.mdAndUp" class="profile-flex-padding">
      <v-card>
        <v-card-actions
          class="dialog-title linear-gradient"
          card
          dark
          v-if="$vuetify.breakpoint.smAndDown"
        >
          <v-spacer></v-spacer>
          <div style="padding-right:10px;">
            <v-menu transition="slide-y-transition" bottom>
              <template v-slot:activator="{ on }">
                <v-btn flat dark v-on="on">
                  Opciones
                  <v-icon right>mdi mdi-menu</v-icon>
                </v-btn>
              </template>
              <v-list>
                <v-list-tile class="sidebarItem" avatar @click="doAction('club')">
                  <v-list-tile-avatar>
                    <v-icon class="sidebar-icon">mdi mdi-apps</v-icon>
                  </v-list-tile-avatar>
                  <v-list-tile-content>
                    <v-list-tile-title class="sidebar-title">Datos del negocio</v-list-tile-title>
                  </v-list-tile-content>
                </v-list-tile>
                <v-list-tile class="sidebarItem" avatar @click="doAction('store')">
                  <v-list-tile-avatar>
                    <v-icon class="sidebar-icon">mdi mdi-store</v-icon>
                  </v-list-tile-avatar>
                  <v-list-tile-content>
                    <v-list-tile-title class="sidebar-title">Datos de contacto</v-list-tile-title>
                  </v-list-tile-content>
                </v-list-tile>
              </v-list>
            </v-menu>
          </div>
        </v-card-actions>
        <v-flex
          :class="{'vb-padding-cp': $vuetify.breakpoint.mdAndUp, 'vb-padding-cp-sm':$vuetify.breakpoint.smAndDown }"
        >
          <div v-if="activeView == 0">
            <v-store-panel-detail></v-store-panel-detail>
          </div>
          <div v-if="activeView == 1">
            <v-store-panel-customization></v-store-panel-customization>
          </div>
          <div v-if="activeView == 2">
            <v-store-panel-banner></v-store-panel-banner>
          </div>
          <div v-if="activeView == 3">
            <v-store-panel-products></v-store-panel-products>
          </div>
        </v-flex>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import { mapGetters } from "vuex";
import StoreDetail from "./storeDetail";
import StoreCustom from "./storeCustom";
import StoreBanner from "./storeBanner";
export default {
  data() {
    return {
      activeView: 0,
      storeId: ""
    };
  },
  computed: {
    ...mapGetters([
      "prefixes",
      "currentBussinesClub",
      "businessSelected",
      "businessId",
      "getStoreById"
    ])
  },
  components: {
    "v-store-panel-detail": StoreDetail,
    "v-store-panel-customization": StoreCustom,
    "v-store-panel-banner": StoreBanner
  },
  created() {
    /*  this.$store
      .dispatch("doGetUserBusinessById", { businessId: this.businessId })
      .then(() => {
        this.viewIsReady = true;
        this.$root.$children[0].$emit("hideLoading");
      }); */
    if (this.$route.query.vStore) {
      this.storeId = this.$route.query.vStore;
    } else {
     
    }
  },
  mounted() {
    this.$store.dispatch("getStoreById", { store_id: this.storeId });
  },
  methods: {
    SwitchView(view) {
      this.activeView = view;
    }
  }
};
</script>

<style>
</style>
