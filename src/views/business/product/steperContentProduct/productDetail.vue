<template>
  <div>
    <v-container grid-list-xl class="pl-1 pt-0">
      <v-form ref="formDetail" lazy-validation v-model="validateDetail">
        <v-layout row wrap>
          <v-flex xs12 md6>
            <v-flex xs12 md12>
              <v-text-field
                :color="currentBussinesClub.theme.primary"
                v-bind:label="$t('message.vProductModel')"
                v-model="model"
              ></v-text-field>
              <v-text-field
                :color="currentBussinesClub.theme.primary"
                v-bind:label="$t('message.vProductMark')"
                v-model="brand"
              ></v-text-field>
              <v-select
                v-model="categoriesSelected"
                :items="firstCategory"
                item-text="name"
                item-value="code"
                :label="$t('message.vBusinessStepAdditionalInfoCategories')"
                :color="currentBussinesClub.theme.primary"
                :no-data-text="$t('message.nodatafound')"
              ></v-select>
              <v-select
                v-if="visibleCat1==true && productSubCategoriesList.l1.length>0"
                :no-data-text="$t('message.nodatafound')"
                v-model="subCategorie1"
                :items="productSubCategoriesList.l1"
                item-text="name"
                item-value="code"
                :label="$t('message.vBusinessStepAdditionalInfoSubCategories')"
                :color="currentBussinesClub.theme.primary"
              ></v-select>
              <v-select
                v-if="visibleCat2==true &&productSubCategoriesList.l2.length>0"
                :no-data-text="$t('message.nodatafound')"
                v-model="subCategorie2"
                :items="productSubCategoriesList.l2"
                item-text="name"
                item-value="code"
                :label="$t('message.vBusinessStepAdditionalInfoSubCategories')"
                :color="currentBussinesClub.theme.primary"
              ></v-select>
              <v-select
                v-if="visibleCat3==true &&productSubCategoriesList.l3.length>0"
                :no-data-text="$t('message.nodatafound')"
                v-model="subCategorie3"
                :items="productSubCategoriesList.l3"
                item-text="name"
                item-value="code"
                :label="$t('message.vBusinessStepAdditionalInfoSubCategories')"
                :color="currentBussinesClub.theme.primary"
              ></v-select>
              <v-select
                v-if="visibleCat4==true &&productSubCategoriesList.l4.length>0"
                :no-data-text="$t('message.nodatafound')"
                v-model="subCategorie4"
                :items="productSubCategoriesList.l4"
                item-text="name"
                item-value="code"
                :label="$t('message.vBusinessStepAdditionalInfoSubCategories')"
                :color="currentBussinesClub.theme.primary"
              ></v-select>
              <v-select
                v-if="visibleCat5==true && productSubCategoriesList.l5.length>0 "
                :no-data-text="$t('message.nodatafound')"
                v-model="subCategorie5"
                :items="productSubCategoriesList.l5"
                item-text="name"
                item-value="code"
                :label="$t('message.vBusinessStepAdditionalInfoSubCategories')"
                :color="currentBussinesClub.theme.primary"
              ></v-select>
              <v-select
                v-if="visibleCat6==true &&productSubCategoriesList.l6.length>0"
                :no-data-text="$t('message.nodatafound')"
                v-model="subCategorie6"
                :items="productSubCategoriesList.l6"
                item-text="name"
                item-value="code"
                :label="$t('message.vBusinessStepAdditionalInfoSubCategories')"
                :color="currentBussinesClub.theme.primary"
              ></v-select>
              <v-radio-group column v-model="radioDetail" :mandatory="false">
                <v-radio
                  :color="currentBussinesClub.theme.primary"
                  v-bind:label="$t('message.vProductAdult')"
                  value="adult"
                ></v-radio>
                <v-radio
                  :color="currentBussinesClub.theme.primary"
                  v-bind:label="$t('message.vProductBoy')"
                  value="boy"
                ></v-radio>
              </v-radio-group>
              <div class="pb-2">
                <span class="textLabelEditors">{{$t('message.vProductPrice')}}</span>
                <v-container grid-list-xs class="no-padding">
                  <v-layout row wrap>
                    <v-flex xs3>
                      <v-checkbox
                        :color="currentBussinesClub.theme.primary"
                        v-bind:label="$t('message.vProductMoney')"
                        v-model="moneyEnabled"
                        hide-details
                        class="shrink mr-2"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs9>
                      <v-text-field
                        :color="currentBussinesClub.theme.primary"
                        v-model="vMoney"
                        hide-details
                        :disabled="!moneyEnabled"
                        v-bind:label="$t('message.vProductMoney')"
                      ></v-text-field>
                    </v-flex>
                    <v-flex xs3>
                      <v-checkbox
                        :color="currentBussinesClub.theme.primary"
                        v-bind:label="$t('message.vProductPoints')"
                        v-model="pointsEnabled"
                        hide-details
                        class="shrink mr-2"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs9>
                      <v-text-field
                        :color="currentBussinesClub.theme.primary"
                        v-model="vPoints"
                        type="number"
                        hide-details
                        :disabled="!pointsEnabled"
                        v-bind:label="$t('message.vProductPoints')"
                      ></v-text-field>
                    </v-flex>
                    <v-flex xs3>
                      <v-checkbox
                        :color="currentBussinesClub.theme.primary"
                        v-bind:label="$t('message.vProductCredit')"
                        type="number"
                        v-model="creditEnabled"
                        hide-details
                        class="shrink mr-2"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs9>
                      <v-text-field
                        :color="currentBussinesClub.theme.primary"
                        v-model="vCredit"
                        type="number"
                        hide-details
                        :disabled="!creditEnabled"
                        v-bind:label="$t('message.vProductCredit')"
                      ></v-text-field>
                    </v-flex>
                  </v-layout>
                </v-container>
              </div>
              <v-flex text-xs-center>
                <div>
                  <v-btn
                    :color="currentBussinesClub.theme.primary"
                    dark
                    @click="openPriceDialog()"
                  >{{$t('message.vProductPriceAdd')}}</v-btn>
                </div>
              </v-flex>
              <v-container grid-list-md class="no-padding">
                 <v-layout row wrap>
                <v-flex xs12 md6>
                    <v-card style="border:1px solid grey">
                <v-card-text>
                   <v-flex text-xs-right class="no-padding">
                    <div>
                      <v-icon :color="currentBussinesClub.theme.primary">mdi-pencil-outline</v-icon>
                    </div>
                  </v-flex>
                  <v-layout row wrap>
                    <v-flex xs12>
                      <div style="display:inline-block">
                        <span style="color:black">Cantidad:</span>
                        <span style="color:black">1000</span>
                      </div>                       
                    </v-flex>
                      <v-flex xs12>
                      <div style="display:inline-block">
                        <span style="color:black">Precio:</span>
                        <span style="color:black">1000</span>
                      </div>                       
                    </v-flex>
                  </v-layout>
                </v-card-text>
              </v-card>
                </v-flex>
              </v-layout>  
              </v-container>
               
            </v-flex>
          </v-flex>
          <!--lado derecho -->
          <v-flex xs12 md6>
            <v-flex xs12 md6 style="height:450px !important">
              <span class="darkText pb-0">{{$t('message.vProductPreview')}}</span>
              <v-product-preview :name="userProducts.names[0].name"></v-product-preview>
              <v-popup-dialog
                @onSave="savePriceMultiple()"
                :title="$t('message.vProductPriceAdd')"
                :model="openPrice"
                @onClose="closePriceDialog()"
              >
                <v-form lazy-validation v-model="validPriceMultiple" ref="formPriceMultiple">
                  <v-container grid-list-xl>
                    <v-layout row wrap>
                      <v-flex xs12 md12>
                        <v-text-field
                          :color="currentBussinesClub.theme.primary"
                          v-bind:label="$t('message.vProductAmount')"
                          v-model="amount"
                          :rules="amountRules"
                          type="number"
                        ></v-text-field>
                      </v-flex>
                      <v-flex xs12 md12>
                        <v-container grid-list-xs class="no-padding">
                           <span class="textLabelEditors">{{$t('message.vProductPrice')}}</span>
                  <v-layout row wrap>   
                    <v-flex xs3>
                      <v-checkbox
                        :color="currentBussinesClub.theme.primary"
                        v-bind:label="$t('message.vProductMoney')"
                        v-model="moneyEnabledMulitple"
                        hide-details
                        class="shrink mr-2"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs9>
                      <v-text-field
                        :color="currentBussinesClub.theme.primary"
                        v-model="vMoneyMultiple"
                        hide-details
                        :disabled="!moneyEnabledMulitple"
                        v-bind:label="$t('message.vProductMoney')"
                      ></v-text-field>
                    </v-flex>
                    <v-flex xs3>
                      <v-checkbox
                        :color="currentBussinesClub.theme.primary"
                        v-bind:label="$t('message.vProductPoints')"
                        v-model="pointsEnabledMultiple"
                        hide-details
                        class="shrink mr-2"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs9>
                      <v-text-field
                        :color="currentBussinesClub.theme.primary"
                        v-model="vPointsMultiple"
                        type="number"
                        hide-details
                        :disabled="!pointsEnabledMultiple"
                        v-bind:label="$t('message.vProductPoints')"
                      ></v-text-field>
                    </v-flex>
                    <v-flex xs3>
                      <v-checkbox
                        :color="currentBussinesClub.theme.primary"
                        v-bind:label="$t('message.vProductCredit')"
                        type="number"
                        v-model="creditEnabledMultiple"
                        hide-details
                        class="shrink mr-2"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs9>
                      <v-text-field
                        :color="currentBussinesClub.theme.primary"
                        v-model="vCreditMultiple"
                        type="number"
                        hide-details
                        :disabled="!creditEnabledMultiple"
                        v-bind:label="$t('message.vProductCredit')"
                      ></v-text-field>
                    </v-flex>
                  </v-layout>
                </v-container>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-form>
              </v-popup-dialog>
            </v-flex>
          </v-flex>
        </v-layout>
        <v-container pl-0>
          <v-btn
            :color="currentBussinesClub.theme.primary"
            dark
            @click="SaveDetail()"
          >{{$t('message.vBusinessStepBusinessContinue')}}</v-btn>
          <v-btn
            @click="GoBack(2)"
            flat
            :color="currentBussinesClub.theme.secondary"
          >{{$t('message.vBusinessStepBusinessBack')}}</v-btn>
        </v-container>
      </v-form>
    </v-container>
  </div>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import PopupDialog from "../../../../components/PopupDialog/PopupDialog";
import productPreview from "../../../../components/Store/productPreview";
export default {
  watch: {
    categoriesSelected(val) {
      let level = 2;
      this.codel1 = val;
      this.getSubCategory(level);
      this.visibleCat1 = true;
      this.categoryData[0] = {
        level: level - 1,
        code: val
      };
      this.subCategorie1 = null;
    },
    subCategorie1(val) {
      let level = 3;
      this.codel2 = val;
      this.getSubCategory(level);
      this.visibleCat2 = true;
      this.categoryData[1] = {
        level: level - 1,
        code: val
      };
      this.subCategorie2 = null;
    },
    subCategorie2(val) {
      let level = 4;
      this.getSubCategory(level);
      this.visibleCat3 = true;
      this.categoryData[2] = {
        level: level - 1,
        code: val
      };
      this.subCategorie3 = null;
    },
    subCategorie3(val) {
      let level = 5;
      this.getSubCategory(level);
      this.visibleCat4 = true;
      this.categoryData[3] = {
        level: level - 1,
        code: val
      };
      this.subCategorie4 = null;
    },
    subCategorie4(val) {
      let level = 6;
      this.getSubCategory(level);
      this.visibleCat5 = true;
      this.categoryData[4] = {
        level: level - 1,
        code: val
      };
      this.subCategorie5 = null;
    },
    subCategorie5(val) {
      let level = 7;
      this.getSubCategory(level);
      this.visibleCat6 = true;
      this.categoryData[5] = {
        level: level - 1,
        code: val
      };
    }
  },
  computed: {
    ...mapGetters([
      "currentBussinesClub",
      "businessCategoriesList",
      "productSubCategoriesList",
      "businessId",
      "userProducts"
    ])
  },
  components: {
    "v-product-preview": productPreview,
    "v-popup-dialog": PopupDialog
  },

  data() {
    return {
      priceMultiple: [
        {
          amount: null,
          price: {
            vMoney:null,
            vPoint:null,
            vCredit:null
          }
        }
      ],
      openPrice: false,
      quantity: 1,
      level: 1,
      codel1: 0,
      codel2: 0,
      codel3: 0,
      codel4: 0,
      codel5: 0,
      codel6: 0,
      categoriesSelected: null,
      subCategorie1: null,
      subCategorie2: null,
      subCategorie3: null,
      subCategorie4: null,
      subCategorie5: null,
      subCategorie6: null,
      visibleCat1: false,
      visibleCat2: false,
      visibleCat3: false,
      visibleCat4: false,
      visibleCat5: false,
      visibleCat6: false,
      price: "",
      moneyEnabled: false,
      vMoney: null,
      vPoints: null,
      vCredit: null,
      pointsEnabled: false,
      creditEnabled: false,
      categoriesArray: [],
      firstCategory: [],
      categoryData: [],
      getObjectPath: Vue.getObjectPath,
      /**v-models */
      validPriceMultiple: true,
      amountRules: [],
      priceRules: [],
      amount: null,
      priceMultipleModel: null,
      model: "",
      brand: "",
      validateDetail: true,
      radioDetail: "",
      moneyEnabledMulitple:false,
      pointsEnabledMultiple:false,
      creditEnabledMultiple:false,
      vMoneyMultiple: null,
      vPointsMultiple: null,
      vCreditMultiple: null,
    };
  },
  created() {
    this.amountRules = [v => !!v || "Cantidad requerida"];
    this.priceRules = [v => !!v || "Precio requerido"];

    this.getCategories();

    this.$store
      .dispatch("doGetBusinessControlPanel", {
        businessId: this.businessId,
        menu: "businessInfo"
      })
      .then(res => {
        this.categoriesArray = res.data.data.businessModel.categories;
      })
      .finally(() => {
        this.filterByCategory();
      });
  },
  methods: {
    savePriceMultiple() {
      if (this.$refs.formPriceMultiple.validate()) {
        if(this.vPointsMultiple!=null){ 
          if(this.vMoneyMultiple==null && this.vCreditMultiple==null){
             alert("Al seleccionar vpoints debe ingresar vmoneys o vcredit")
          }else{
            let data = {
          amount: this.amount,
          price: {
            vMoney:this.vMoneyMultiple,
            vPoint:this.vPointsMultiple,
            vCredit:this.vCreditMultiple
          }
        };
        this.priceMultiple.push(data);
        this.closePriceDialog();
          }  
        }
      }
    },
    openPriceDialog() {
      this.openPrice = true;
      this.$refs.formPriceMultiple.resetValidation();
      this.$refs.formPriceMultiple.reset();
    },
    closePriceDialog() {
      this.openPrice = false;
      this.$refs.formPriceMultiple.resetValidation();
    },
    increment() {
      this.quantity++;
    },
    decrement() {
      if (this.quantity === 1) {
        alert("Negative quantity not allowed");
      } else {
        this.quantity--;
      }
    },
    filterByCategory() {
      this.categoriesArray.forEach(element => {
        const result = this.businessCategoriesList.filter(
          category => category.code == element
        );
        if (result.length > 0) {
          this.firstCategory.push({
            name: this.getObjectPath(result, "0.name", null),
            code: this.getObjectPath(result, "0.code", null)
          });
        }
      });
    },
    getCategories() {
      let categoryData = {
        lang: 7,
        category: "products",
        level: this.level
      };
      this.$store
        .dispatch("doGetBusinessCategories", categoryData)
        .then(res => {});
    },
    getSubCategory(level) {
      let categoryData = {
        lang: 7,
        category: "products",
        level: level,
        l1: this.codel1,
        l2: this.codel2,
        l3: this.codel3,
        l4: this.codel4,
        l5: this.codel5,
        l6: this.codel6
      };

      this.$store
        .dispatch("doGetProductsSubCategories", {
          data: categoryData,
          level: level
        })
        .then(res => {
          console.log("SA", res);
        });
    },
    SaveDetail() {
      console.log("userProducts", this.userProducts);
      var isAdult = false;
      var isBoy = false;
      if (this.radioDetail == "adult") {
        isAdult = true;
      } else {
        isBoy = true;
      }
      let dataDetail = {
        productData: {
          productId: this.userProducts._id,
          model: this.model,
          category: this.categoryData,
          brand: this.brand,
          price: {
            vMoney: this.vMoney,
            vPoint: this.vPoints,
            vCredit: this.vCredit,
            wholesaler: 0
          },
          additional: {
            adult: isAdult,
            isBundle: isBoy
          }
        },
        type: "ProductDetail"
      };
      console.log(dataDetail);
      this.$store.dispatch("doUpdateUserProducts", dataDetail).then(res => {
        this.$emit("onContinue");
      });
    },
    GoBack(data) {
      this.$emit("onGoBack", data);
    }
  }
};
</script>
<style scope>
.quantity-toggle {
  display: flex;
}
.quantity-toggle input {
  border: 0;
  border-top: 2px solid #ddd;
  border-bottom: 2px solid #ddd;
  width: 2.5rem;
  height: 40px !important;
  text-align: center;
  padding: 0 0.5rem;
}
.quantity-toggle button {
  border: 2px solid #ddd;
  padding: 0.5rem;
  height: 40px !important;
  background: #f5f5f5;
  color: #888;
  font-size: 1rem;
  cursor: pointer;
}
</style>