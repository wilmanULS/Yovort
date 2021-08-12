 <template>
  <v-container fluid grid-list-xs class="no-padding">
    <v-layout row wrap>
      <v-flex class="pb-1" v-for="i in 12" :key="i" lg3 md3 xs12>
        <v-hot-deal-item :isNew="true" :showcounter="false"></v-hot-deal-item>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import hotDealItem from "./hotDealItem";
export default {
  components: {
    "v-hot-deal-item": hotDealItem
  },
  data() {
    return {
      image: "https://cdn.vuetifyjs.com/images/cards/halcyon.png"
    };
  }
};
</script>