 <template>
  <v-form data-vv-scope="step2">
    <v-flex xs12 md6 pb-3 style="text-align: left;padding-right:2px">
      <span class="titleCard darker-titles">{{$t('message.addresses')}}</span>
      <v-btn
        small
        style="margin-right: 9px"
        :color="currentBussinesClub.theme.primary"
        fab
        dark
        absolute
        right
        @click="openAddressDialog(-1)"
      >
        <v-icon>add</v-icon>
      </v-btn>
    </v-flex>
    <v-flex pb-3></v-flex>

    <v-container grid-list-xs class="no-padding">
      <v-layout row wrap>
        <v-flex xs12 sm6 md3 v-for="(address,index) in businessAddresses" :key="address">
          <v-card
            class="flexcard"
            style="margin: .5rem!important;"
            :title="address.address.fullAddress"
          >
            <v-img
              style="background: #2C353A!important;"
              max-height="250px"
              min-height="250px"
              height="250px"
              contain
            >
              <span
                style="position: absolute!important;left: 5px!important; top:5px!important; z-index: 1000!important;color: white;font-size: 11px;"
              ></span>
              <div
                style="position: absolute!important;right: 5px!important; bottom:5px!important; z-index: 1000!important;"
              >
                <v-speed-dial
                  v-model="fab"
                  direction="left"
                  open-on-hover
                  transition="slide-x-reverse-transition"
                >
                  <template v-slot:activator>
                    <v-btn
                      v-model="fab"
                      :color="currentBussinesClub.theme.secondary"
                      dark
                      fab
                      small
                    >
                      <v-icon>mdi mdi-plus</v-icon>
                      <v-icon>mdi mdi-close</v-icon>
                    </v-btn>
                  </template>
                  <v-btn
                    :title="$t('message.clubRules')"
                    fab
                    dark
                    small
                    @click="openAddressDialog(index)"
                    :color="currentBussinesClub.theme.primary"
                  >
                    <v-icon>mdi mdi-format-list-numbered</v-icon>
                  </v-btn>
                  <v-btn
                    :title="$t('message.clubMoreInformation')"
                    fab
                    dark
                    small
                    @click="OpenConfirmDeleteDialog(address._id,address.address.fullAddress)"
                    :color="currentBussinesClub.theme.secondary"
                  >
                    <v-icon>mdi mdi-delete</v-icon>
                  </v-btn>
                </v-speed-dial>
              </div>
            </v-img>
            <v-card-text class="grow p8" primary-title>
              <span
                style="color:black!important; overflow: hidden;text-overflow: ellipsis;white-space: nowrap;"
                class="headline mb-0"
              >{{address.address.fullAddress}}</span>
            </v-card-text>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>

    <v-address-dialog ref="dialogAddress"></v-address-dialog>
    <v-confirm-dialog
      v-bind:titleToolbar="$t('message.delete')"
      ref="confirmDelete"
      type="question"
      :heading="$t('message.deleteAddress')"
      v-bind:message="$t('message.deleteAddressConfirmation',{address:  this.addressDeleting})"
      @onConfirm="DeleteAddress()"
    ></v-confirm-dialog>
  </v-form>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import DialogAddress from "./dialogAddAddress";
import Swiper from "swiper";
import confirmDialog from "Components/ySocialHome/confirmDialog";
export default {
  components: {
    "v-address-dialog": DialogAddress,
    "v-confirm-dialog": confirmDialog
  },
  watch: {
    selectBusinessModel(val) {
      switch (val) {
        case "Productos":
          this.subcategoryBusinessModel = ["Gaseosas", "Autos"];
          this.selectSubcategory = "";
          break;
        case "Servicios":
          this.subcategoryBusinessModel = ["Limpieza", "Funerarios"];
          this.selectSubcategory = "";
          break;
          break;
        case "Ambos":
          this.subcategoryBusinessModel = [
            "Limpieza",
            "Funerarios",
            "Gaseosas",
            "Autos"
          ];
          this.selectSubcategory = "";
          break;
          break;
        default:
          this.subcategoryBusinessModel = [];
          break;
      }
    }
  },
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "wizardStepTwo",
      "businessAddresses"
    ]),
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
  created() {
    var el = this;
    this.swiperOption = {
      slidesPerView: 1,
      centeredSlides: true,
      spaceBetween: 30,
      pagination: {
        el: ".swiper-pagination",
        clickable: true,
        renderBullet: function(index, className) {
          return (
            `<span style="background:` +
            el.currentBussinesClub.theme.primary +
            `" class="${className} swiper-pagination-bullet-custom"></span>`
          );
        }
      },
      navigation: {
        nextEl: ".swiper-button-next",
        prevEl: ".swiper-button-prev"
      }
    };

    this.$store.commit("mGetCurrentUserBusiness");
    /**
     * Get info of wizard step one by current business id
     */
    this.$store
      .dispatch("doGetInfoBusinessWizardStep", {
        businessId: this.currentUserBusiness,
        step: "businessData"
      })
      .then(res => {
        this.chargeInfoStepTwo();
      })
      .catch()
      .finally(() => {
        (this.isLoading = false), (this.viewReady = true);
        this.$root.$children[0].$emit("hideLoading");
      });

    this.view = true;
  },
  methods: {
    OpenConfirmDeleteDialog(id, address) {
      this.addressSelected = id;
      this.addressDeleting = address;
      this.$refs.confirmDelete.openDialog();
    },
    DeleteAddress() {
      this.$store
        .dispatch("doDeleteBusinessAddress", {
          businessId: this.currentUserBusiness,
          addressId: this.addressSelected
        })
        .then(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.businessAddressDeletedSuccess"),
            type: "success"
          });
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.couldNotDeleteBusinessAddress"),
            type: "error"
          });
        })
        .finally(() => {
          this.addressSelected = -1;
          this.addressDeleting = "";
          this.$refs.confirmDelete.close();
        });
    },
    openAddressDialog(index) {
      this.$refs.dialogAddress.openAddressDialog(index);
    },
    removeSubcategory(item) {
      this.selectSubcategory.splice(this.selectSubcategory.indexOf(item), 1);
      this.selectSubcategory = [...this.selectSubcategory];
    },
    remove(item) {
      this.selectLanguageModel.splice(
        this.selectLanguageModel.indexOf(item),
        1
      );
      this.selectLanguageModel = [...this.selectLanguageModel];
    },
    chargeInfoStepTwo() {
      this.addressesList = this.wizardStepTwo.addresses;
    }
  },
  data() {
    return {
      getObjectPath: Vue.getObjectPath,
      addressSelected: -1,
      businessModel: ["Productos", "Servicios", "Ambos"],
      languageList: ["Español", "Inglés", "Chino"],
      selectLanguage: "",
      selectLanguageModel: [],
      subcategoryBusinessModel: [],
      selectSubcategory: "",
      selectBusinessModel: "",
      swiperOption: {},
      view: false,
      backgroundSwiper: "#3a5584",
      swiperSlides: [1, 2, 3, 4, 5],
      addressDeleting: "",
      /* Data use as fields for v-model in step two*/
      addressesList: []
    };
  }
};
</script>
<style>
@import "~swiper/dist/css/swiper.css";

.swiper-pagination-bullet-custom {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  margin: 3px!;
}

.mobileEditor {
  height: 60vh !important;
}

.desktopEditor {
  height: 30vh !important;
}
.borderCards {
  border-style: solid;
  border-width: 1px;
  border-color: #cfd8dc;
}
</style>
