<template>
  <v-form data-vv-scope="step2">
    <v-layout row wrap>
      <v-flex xs12 md6>        
        <v-text-field
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.idNavigation')"
        ></v-text-field>
      </v-flex>
      <v-flex xs12 md6>
        <v-select
          :color="currentBussinesClub.theme.primary"
          :items="businessModel"
          :label="$t('message.businessModel')"
          v-model="selectBusinessModel"
          :no-data-text="$t('message.nodatafound')"
        ></v-select>
      </v-flex>

    <!--   <v-flex xs12 md6>
        <v-autocomplete
          :color="currentBussinesClub.theme.primary"
          :items="subcategoryBusinessModel"
          v-model="selectSubcategory"
          clearable
          :label="$t('message.subcategories')"
          :no-data-text="$t('message.nodatafound')"
          multiple
          chips
        >
          <template v-slot:selection="data">
            <v-chip
              outline
              :color="currentBussinesClub.theme.primary"
              :selected="data.selected"
              close
              @input="removeSubcategory(data.item)"
            >
              <strong>{{ data.item }}</strong>
            </v-chip>
          </template>
        </v-autocomplete>
      </v-flex>
      <v-flex xs12 md6>
        <v-autocomplete
          :color="currentBussinesClub.theme.primary"
          :items="languageList"
          v-model="selectLanguageModel"
          :no-data-text="$t('message.nodatafound')"
          clearable
          :label="$t('message.languagesList')"
          multiple
          chips
        >
          <template v-slot:selection="data">
            <v-chip
              outline
              :color="currentBussinesClub.theme.primary"
              :selected="data.selected"
              close
              @input="remove(data.item)"
            >
              <strong>{{ data.item }}</strong>
            </v-chip>
          </template>
        </v-autocomplete>
      </v-flex> -->
    </v-layout>

    <v-category-list></v-category-list> 
    <v-address-dialog ref="dialogAddress"></v-address-dialog>
            <v-confirm-dialog v-bind:titleToolbar="$t('message.delete')" ref="confirmDelete" type="question" 
             :heading="$t('message.deleteAddress')"
             v-bind:message="$t('message.deleteAddressConfirmation',{address:  this.addressDeleting})"
             @onConfirm="DeleteAddress()">
        </v-confirm-dialog>
  </v-form>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import DialogAddress from "./dialogAddAddress";
import Swiper from 'swiper';
import confirmDialog from "Components/ySocialHome/confirmDialog";
import categoryList from "./businessCategory";
export default {
  components: {
    "v-address-dialog": DialogAddress,
    "v-confirm-dialog": confirmDialog,
    "v-category-list" : categoryList
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
  created(){    
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
  
    this.view=true
  },
  methods: {
    OpenConfirmDeleteDialog(id,address){
      this.addressSelected=id;
      this.addressDeleting=address;     
      this.$refs.confirmDelete.openDialog();
    },
    DeleteAddress(){
      this.$store.dispatch("doDeleteBusinessAddress",{businessId: this.currentUserBusiness, addressId:this.addressSelected})
      .then(()=>{
          this.$store.dispatch("doNotification", {
            msg: this.$t('message.businessAddressDeletedSuccess'),
            type: "success"
          });
      })
      .catch(()=>{
          this.$store.dispatch("doNotification", {
            msg: this.$t('message.couldNotDeleteBusinessAddress'),
            type: "error"
          });
      })
      .finally(()=>{
        this.addressSelected=-1;
        this.addressDeleting='';
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
    chargeInfoStepTwo(){
        this.addressesList=this.wizardStepTwo.addresses
    }
    
  },
  data() {
    return {
      addressSelected:-1,
      businessModel: ["Productos", "Servicios", "Ambos"],
      languageList: ["Español", "Inglés", "Chino"],
      selectLanguage: "",
      selectLanguageModel: [],
      subcategoryBusinessModel: [],
      selectSubcategory: "",
      selectBusinessModel: "",
      swiperOption: {},
      view:false,
      backgroundSwiper: "#3a5584",
      swiperSlides: [1, 2, 3, 4, 5],
      addressDeleting:'',
     /* Data use as fields for v-model in step two*/
      addressesList:[]
    };
  }
};
</script>
<style>

@import '~swiper/dist/css/swiper.css';

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
