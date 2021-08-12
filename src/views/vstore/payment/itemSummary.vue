<template>
  <v-container grid-list-xs py-3>
    <v-layout row wrap>
      <v-flex xs6 class="pa-1">
        <v-layout row wrap>
          <v-flex xs4 class="mx-2">
            <v-img contain :src="item.product.media[0].url" alt="product img"></v-img>
          </v-flex>
          <v-flex xs6>
            <v-layout row wrap justify-center align-center>
              <span
                style="font-size:0.9rem;font-weight:700;color:grey"
              >{{item.product.names[0].name.toUpperCase()}}</span>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-flex>
      <v-flex xs3>
        <v-layout row wrap justify-center align-center>
          <span style="font-weight:600;color:grey">{{item.quantity}}</span>
        </v-layout>
      </v-flex>
      <v-flex xs3>
        <v-layout row wrap justify-center align-center>
          <span style="font-weight:600;color:grey">${{item.price}}</span>
        </v-layout>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
export default {
  props: {
    item: { type: Object }
  },
  data() {
    return {};
  }
};
</script>
<style>
.mobileInformation {
  display: flex;
  align-items: center;
}
.desktopInformation {
  display: flex;
  align-items: center;
  justify-content: center;
}
.mobileButtons {
  display: flex;
  align-items: center;
  justify-content: flex-end;
}
.desktopButtons {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
