<template>
  <v-form ref="form" lazy-validation v-model="validInitialData">
    <v-flex class="text-xs-left">
      <v-container grid-list-xl class="pt-0 pl-1">
        <v-layout row wrap>
          <v-flex xs12 md6>
            <v-flex xs12 md12>
              <v-text-field
                clearable
                :color="currentBussinesClub.theme.primary"
                v-bind:label="$t('message.commercialName')"
                required
                v-model="name"
                :rules="nameRules"
              ></v-text-field>
              <v-text-field
                ref="businessName"
                hide-details
                clearable
                required
                :color="currentBussinesClub.theme.primary"
                v-bind:label="$t('message.vProductSku')"
                v-model="sku"
                @blur="updateSku(sku)"
              ></v-text-field>
              <v-flex xs12 md12 class="pl-0">
                <v-card-text class="pl-0 pb-0">
                  <span class="textLabelEditors">{{$t('message.vProductShortDescription')}}</span>
                </v-card-text>
                <vue-editor v-model="shortDescription"></vue-editor>
              </v-flex>
              <v-flex xs12 md12 class="pl-0">
                <v-card-text class="pl-0 pb-0">
                  <span class="textLabelEditors">{{$t('message.vProductLargeDescription')}}</span>
                </v-card-text>
                <vue-editor v-model="largeDescription"></vue-editor>
              </v-flex>
              <v-flex xs12 md12 class="pl-0">
                <v-card-text class="pl-0 pb-0">
                  <span class="textLabelEditors">{{$t('message.vProductDetailDescription')}}</span>
                </v-card-text>
                <vue-editor v-model="detailDescription"></vue-editor>
              </v-flex>
            </v-flex>
          </v-flex>
          <!-- right-->
          <v-flex xs12 md6 style="height:450px !important">
              <span class="darkText pb-0">{{$t('message.vProductPreview')}}</span>
            <v-product-preview :name="name" :sku="sku" :longDescription="largeDescription" :currentProduct="currentProductStore"></v-product-preview>
          </v-flex>
        </v-layout>
        <v-container class="pr-0 pl-1">
          <!--   <v-flex mb-5></v-flex> -->
          <v-btn
            :color="currentBussinesClub.theme.primary"
            dark
            @click="SaveInitialData()"
          >{{$t('message.vBusinessStepBusinessContinue')}}</v-btn>
        </v-container>
      </v-container>
    </v-flex>
  </v-form>
</template>
<script>
import { createEditor } from "vueditor";
import Vue from "vue";
import { mapGetters } from "vuex";
import { VueEditor } from "vue2-editor";
import productPreview from "../../../../components/Store/productPreview";
export default {
  components: {
    "vue-editor": VueEditor,
    "v-product-preview": productPreview
  },
 
  computed: {
    ...mapGetters([
      "userProducts",
      "currentBussinesClub",
      "getSkuProducts",
      "businessId",
      "getSelectedProductInformation",
      "allUserProducts"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  data() {
    return {
      getObjectPath: Vue.getObjectPath,
      /**v-Models*/
      validInitialData: true,
      name: "",
      sku: "",
      /**config editor */
      shortDescription: "",
      largeDescription: "",
      detailDescription: "",
      nameRules: "",
      currentProductStore: {
        businessId: "5bd0a7aa23581f11b08b8511",
        names: [
          {
            name: "CAMARO RS",
            description: {
              long:
                "El Chevrolet Camaro es un auto deportivo producido por el fabricante estadounidense Chevrolet GM. Se clasifica como un pony car y en algunas versiones también como un muscle car. El Camaro surgió como la respuesta de General Motors a su rival más digno durante esta época: el Ford Mustang.En plena era de los pony cars, Chevrolet presentó este modelo en dos versiones: el camaro Rally Sport (RS) y el camaro Super Sport (SS). Este último contaba con un V8 de 7.9 litros, y otro motor opcional de 6,5 litros con 396 plgs³, estaban hechos para la clase de cliente estadounidense apasionado por la velocidad, con la idea de correr en el verano y guardarlo en el invierno, ya que se fabricaba en versión descapotable como en coupé.",
              short:
                "El Chevrolet Camaro es un auto deportivo producido por el fabricante estadounidense Chevrolet GM. Se clasifica como un pony car y en algunas versiones también como un muscle car.",
              detail:
                "Esta tradición jamás continuó a pesar de la gran decadencia a mediados de los años 1970, con la subida del precio de los combustibles. Este modelo se hizo famoso en las carreras de Trans-Am y la National Hot Rod Association."
            },
            lang: 0
          }
        ],
        sku: "String",
        price: {
          vMoney: 100000,
          vPoint: 100000,
          vCredit: 100000,
          wholesaler: 0
        },
        media: [
          {
            name: "Camaro1",
            contentType: "img",
            extension: "jpg",
            url:
              "https://www.chevrolet.com/content/dam/chevrolet/na/us/english/index/vehicles/2019/performance/camaro/colorizer/01-images/2019-camaro-coupe-2ss-gan-colorizer.jpg?imwidth=1200",
            order: 0
          },
          {
            name: "String",
            contentType: "String",
            extension: "String",
            url:
              "https://s.aolcdn.com/dims-global/dims3/GLOB/legacy_thumbnail/640x400/quality/80/https://s.aolcdn.com/commerce/autodata/images/USC90CHC021C021001.jpg",
            order: 1
          },
          {
            name: "String",
            contentType: "String",
            extension: "String",
            url:
              "https://www.chevrolet.com/content/dam/chevrolet/na/us/english/index/vehicles/2019/performance/camaro/colorizer/01-images/2019-camaro-coupe-2ss-g16-colorizer.jpg?imwidth=1200",
            order: 0
          },
          {
            name: "String",
            contentType: "String",
            extension: "String",
            url:
              "https://www.chevrolet.com/content/dam/chevrolet/na/us/english/index/vehicles/2019/performance/camaro/colorizer/01-images/2019-camaro-coupe-2ss-g16-colorizer.jpg?imwidth=1200",
            order: 0
          },
          {
            name: "Camaro1",
            contentType: "img",
            extension: "jpg",
            url:
              "http://demo.vinovathemes.com/prestashop_cariana/170-large_default/lorem-ipsum-dolor.jpg",
            order: 0
          },
          {
            name: "String",
            contentType: "String",
            extension: "String",
            url:
              "https://s.aolcdn.com/dims-global/dims3/GLOB/legacy_thumbnail/640x400/quality/80/https://s.aolcdn.com/commerce/autodata/images/USC90CHC021C021001.jpg",
            order: 1
          },
          {
            name: "String",
            contentType: "String",
            extension: "String",
            url:
              "https://www.chevrolet.com/content/dam/chevrolet/na/us/english/index/vehicles/2019/performance/camaro/colorizer/01-images/2019-camaro-coupe-2ss-g16-colorizer.jpg?imwidth=1200",
            order: 0
          },
          {
            name: "String",
            contentType: "String",
            extension: "String",
            url:
              "https://www.chevrolet.com/content/dam/chevrolet/na/us/english/index/vehicles/2019/performance/camaro/colorizer/01-images/2019-camaro-coupe-2ss-g16-colorizer.jpg?imwidth=1200",
            order: 0
          }
        ],
        availabilityDate: "10/10/2018",
        productType: 0,
        brand: "String",
        model: "Rs",
        category: [0],
        warranty: {
          policy: "La mejor Garantia porque este auto es indestructible",
          time: "10 años",
          typeWarranty: "String"
        },
        additional: {
          adult: false,
          isBundle: false
        },
        measureValue: 0,
        shippingPolicy: {
          delivery: "La entrega es en Heklicoptero",
          returns: "La devolucion solo puede hacerse en caso de falla del motor"
        },
        lots: [
          {
            sku: "String",
            receivedDate: "9/10/2019",
            expirationDate: "10/10/2019",
            stock: 100,
            sold: 0,
            refunds: 0,
            media: [
              {
                name: "String",
                contentType: "String",
                extension: "String",
                url:
                  "https://www.chevrolet.com/content/dam/chevrolet/na/us/english/index/vehicles/2019/performance/camaro/colorizer/01-images/2019-camaro-coupe-2ss-g16-colorizer.jpg?imwidth=1200",
                order: 0
              }
            ],
            purchasePrice: 0,
            warehouse: {
              country: "5ce32fc6afb6e413b037cb23",
              province: "String",
              city: "String",
              fullAddress: "String",
              zip: "String",
              location: {
                latitude: 0,
                longitude: 0
              }
            },
            variants: [
              {
                color: "Red",
                size: "String",
                material: "String",
                pattern: "String",
                sizeSystem: "String",
                variantLocalization: [0],
                customVariants: [
                  {
                    name: "String",
                    value: "String"
                  }
                ],
                stock: 50,
                sold: 0,
                refunds: 0,
                media: [
                  {
                    name: "String",
                    contentType: "String",
                    extension: "String",
                    url: "String",
                    order: 0
                  }
                ]
              },
              {
                color: "Black",
                size: "String",
                material: "String",
                pattern: "String",
                sizeSystem: "String",
                variantLocalization: [0],
                customVariants: [
                  {
                    name: "String",
                    value: "String"
                  }
                ],
                stock: 50,
                sold: 0,
                refunds: 0,
                media: [
                  {
                    name: "String",
                    contentType: "String",
                    extension: "String",
                    url: "String",
                    order: 0
                  }
                ]
              }
            ],
            propertyLocalization: [0],
            weight: 0,
            dimensions: {
              large: 0,
              width: 0,
              height: 0
            },
            codes: {
              bar: "String",
              qr: "String"
            }
          }
        ],
        offers: [
          {
            price: {
              vMoney: 0,
              vPoint: 0,
              vCredit: 0,
              wholesaler: 0
            },
            discount: 0,
            startDate: "10/10/2018",
            endDate: "10/10/2018",
            status: 0
          }
        ],
        status: 0
      }
    };
  },
  created() {
    this.nameRules = [v => !!v || "Nombre del producto es requerido"];
  },
  mounted() {
   this.reloadFields()
  },
  methods: {
    reloadFields(){
      console.log(this.userProducts);
      console.log(this.allUserProducts);
      this.name=this.getObjectPath(this.getSelectedProductInformation,"names.0.name","");
      this.sku=this.getObjectPath(this.getSelectedProductInformation,"codes.sku","");
      this.shortDescription=this.getObjectPath(this.getSelectedProductInformation,"names.0.description.short","");
      this.largeDescription=this.getObjectPath(this.getSelectedProductInformation,"names.0.description.long","");
      this.detailDescription=this.getObjectPath(this.getSelectedProductInformation,"names.0.description.detail","");
    },
    updateSku(sku) {
      let dataSku = {
        productData: {
          productId: this.userProducts._id,
          codes:{
            sku:sku,
            qr:"",
            bar:""
          }
        },
        type: "Codes"
      };
      this.$store.dispatch("doUpdateUserProducts", dataSku);
    },
    SaveInitialData() {
      if (this.$refs.form.validate()) {
        let initialData = {
          productData: {
            productId: this.userProducts._id,
            namesId: this.getObjectPath(this.userProducts, "names.0._id", ""),
            name: [{
              description: {
                long: this.largeDescription,
                short: this.shortDescription,
                detail: this.detailDescription
              },
              name: this.name,
              lang: 7
            }]
          },
          type: "Name"
        };
        this.$store
          .dispatch("doUpdateUserProducts", initialData)
          .then(res => {
            this.$emit("onContinue");
            this.$store.dispatch("doGetUserProducts", this.businessId);
          })
          .catch(err => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.couldNotAddUserBusiness"),
              type: "error"
            });
          });
      }
    }
  }
};
</script>
<style scope>
.darkText {
  color: black;
}
</style>