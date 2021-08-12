<template>
  <v-container class="px-2" pt-3 mt-0 style="height:100%">
    <v-card class="mt-0 mb-0 pb-2 filter-card" flat>
      <v-toolbar flat color="transparent" style="border:1px solid #cfd8dc !important;">
        <v-btn icon v-bind:class="{ 'active': layout == 'list'}" v-on:click="layout = 'list'">
          <v-icon style="font-size: 24pt" v-bind:class="{ 'activeRed': layout == 'list'}">list</v-icon>
        </v-btn>
        <v-btn
          class="px-0"
          icon
          v-bind:class="{ 'active': layout == 'grid'}"
          v-on:click="layout = 'grid'">
          <v-icon v-bind:class="{ 'activeRed': layout == 'grid'}">grid_on</v-icon>
        </v-btn>
        <v-spacer></v-spacer>
        <span
          v-if="!$vuetify.breakpoint.xsOnly"
          style="padding-right:10px;"
        >{{$t('message.orderBy')}}</span>
        <v-combobox class="filter-card" flat style=" max-width: 100px;" solo></v-combobox>
        <v-spacer></v-spacer>
        <span
          v-if="!$vuetify.breakpoint.xsOnly"
          style="padding-right:10px;"
        >{{$t('message.quantity')}}</span>
        <v-combobox class="filter-card" flat style=" max-width: 70px;" solo></v-combobox>
      </v-toolbar>
    </v-card>
    <ygrid class="pt-3" v-if="layout == 'grid'"></ygrid>
    <v-product-detail v-if="mountedReady" ref="productDetail"></v-product-detail>
    <div v-if="layout == 'list'">
      <v-layout
        style="padding-y:50px!important"
        flat
        v-for="(product, i) in productStoreList"
        :key="i"
        color="white"
      >
        <v-product-card :product="product[0]"></v-product-card>
        <!--  <list-item ></list-item> -->
      </v-layout>
    </div>
  </v-container>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import itemGrid from "./itemsGrid";
import productCard from "./productCard";
export default {
  mounted() {
    this.$root.$children[0].$emit(
      "showLoading",
      this.$t("message.loadingProducts")
    );
    this.$store
      .dispatch("getStoreProducts")
      .then(res => {
        this.$root.$children[0].$emit("hideLoading");
        this.mountedReady = true;
      })
      .catch(err => {
        console.log(err)
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.noItemsCartMessage"),
          type: "error"
        });
        this.$root.$children[0].$emit("hideLoading");
      });
  },
  computed: {
    ...mapGetters(["currentBussinesClub", "productStoreList"])
  },
  components: {
    ygrid: itemGrid,
    "v-product-card": productCard
  },
  data() {
    return {
      layout: "list",
      mountedReady: false
    };
  }
};
</script>

<style scoped>
.activeRed {
  color: #82459a !important;
}
.v-toolbar__content {
  width: 65px !important;
}
[v-cloak] {
  display: none;
}

* {
  margin: 0;
  padding: 0;
}

body {
  font: 15px/1.3 "Open Sans", sans-serif;
  color: #5e5b64;
  text-align: center;
}

a,
a:visited {
  outline: none;
  color: #389dc1;
}

a:hover {
  text-decoration: none;
}

section,
footer,
header,
aside,
nav {
  display: block;
}

/*-------------------------
	The search input
--------------------------*/

.bar {
  background-color: #fff;

  box-shadow: 0 1px 1px #ccc;
  border-radius: 2px;
  width: 100%;
  padding: 10px;
  margin: 45px auto 25px;
  position: relative;
  text-align: right;
  line-height: 1;
}

.bar a {
  background: white center center no-repeat;
  width: 32px;
  height: 32px;
  display: inline-block;
  text-decoration: none !important;
  margin-right: 5px;
  border-radius: 2px;
  cursor: pointer;
}
.bar a.active {
  background-color: red;
}

.bar input {
  background: #fff no-repeat 13px 13px;

  border: none;
  width: 100%;
  line-height: 19px;
  padding: 11px 0;

  border-radius: 2px;
  box-shadow: 0 2px 8px #c4c4c4 inset;
  text-align: left;
  font-size: 14px;
  font-family: inherit;
  color: #738289;
  font-weight: bold;
  outline: none;
  text-indent: 40px;
}

/*-------------------------
	List layout
--------------------------*/

ul.list {
  list-style: none;
  width: 100%;
  margin: 0 auto;
  text-align: left;
}

ul.list li {
  border-bottom: 1px solid #ddd;
  padding: 10px;
  overflow: hidden;
}

ul.list li img {
  width: 120px;
  height: 120px;
  float: left;
  border: none;
}

ul.list li p {
  margin-left: 135px;
  font-weight: bold;
  color: #6e7a7f;
}

/*-------------------------
	Grid layout
--------------------------*/

ul.grid {
  list-style: none;

  margin: 0 auto;
  text-align: left;
}

ul.grid li {
  padding-left: 24px;
  float: left;
}

ul.grid li img {
  width: 280px;
  height: 280px;
  object-fit: cover;
  display: block;
  border: none;
}
</style>

