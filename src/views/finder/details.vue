<template>
  <v-layout row wrap style="height:0px">
    <gallery class="wrapperGallery" :images="images" :index="indexImg" @close="indexImg = null"></gallery>
    <v-flex md12 xs12 lg12 sm12 style="position: relative;">
      <v-layout row wrap>
        <v-flex md12 xs12 lg12 sm12 style="height:48px"></v-flex>
        <v-flex style="width:25vh">
          <v-flex :style="'background-color:#2c353a'">
            <v-flex md12 lg12 style="padding-bottom: 20px;padding-top: 5px;">
              <span :style="'padding-left: 20px;color:#e9edf2'">{{location.businessName}}</span>
            </v-flex>
            <v-flex md12 lg12 style="margin-left: 20px;padding-bottom: 6px">
              <v-flex class="hover-card" style="width: auto;">
                <v-flex class="post-container-business-detail">
                  <v-flex class="post-thumb-icon-business" :style="$vuetify.breakpoint.smAndDown?' padding-top: 3vh;':' padding-top: 0;'" >
                    <img
                      src="https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg"
                      :style="$vuetify.breakpoint.smAndDown?'width: 100px;height: 100px;border-radius: 100px;':'width: 180px;height: 180px;border-radius: 100px;'"
                    >
                  </v-flex>

                  <v-flex
                    :style="$vuetify.breakpoint.smAndDown?'margin-left:115px;':'margin-left:215px;'"
                  >
                    <v-flex>
                      <p
                        style="color:white"
                        align=" justify"
                      >Lorem ipsum dolor sit amet, consectetur adipiscing elit. Ut vitae tincidunt sem, ac luctus orci. Aliquam condimentum velit at lacinia varius. Nulla sit amet lectus nulla.</p>
                    </v-flex>
                    <v-flex>
                      <i
                        class="far fa-clock"
                        :style="'margin-right: 5px;color:'+this.currentBussinesClub.theme.secondary"
                      ></i>
                      <span :style="'color:'+this.currentBussinesClub.theme.secondary">Abierto ahora</span>
                    </v-flex>
                    <v-flex>
                      <i style="margin-right: 5px;color:#89959c;" class="fas fa-map-marker-alt"></i>
                      <span style="color:#89959c">Quito, Ecuador</span>
                    </v-flex>
                  </v-flex>
                </v-flex>
                <v-flex class="post-container-results"></v-flex>
              </v-flex>
            </v-flex>
          </v-flex>
        </v-flex>

        <v-flex style="width:75vh">
          <v-flex :style="'background-color:#e9edf2;z-index:0'">
            <v-flex md12 lg12 style="padding-bottom: 20px;padding-top: 5px;">
              <span :style="'padding-left: 20px;color:#2c353a '">Galeria</span>
            </v-flex>
            <v-flex md12 lg12 style="padding-bottom: 6px">
              <carousel
                autoWidth
                :dots="false"
                :margin="20"
                :items="1"
                :navText="['&larr;','&rarr;']"
              >
                <v-flex
                  v-for="(vector,index) in bestResults"
                  :key="index"
                  style="height:250px;width: 250px; margin-right:0px;display:inline-block;"
                >
                  <v-flex class="hover-card">
                    <v-img
                      @click="indexImg = index"
                      style="border-radius: 4%;"
                      :src="vector.imgUrl"
                      height="180px"
                      width="250px"
                    ></v-img>

                    <v-flex class="post-container-results"></v-flex>
                  </v-flex>
                </v-flex>
              </carousel>
            </v-flex>
          </v-flex>
        </v-flex>
      </v-layout>
    </v-flex>
  </v-layout>
</template>
<script>
import Vue from "vue";
import { getCurrentAppLayout } from "Helpers/helpers";
import { mapGetters } from "vuex";

import carousel from "vue-owl-carousel";
import VueGallery from "vue-gallery";
export default {
  props: {
    location: Object
  },
  watch: {
    tabs(val) {}
  },
  components: {
    carousel,
    gallery: VueGallery
  },
  mounted() {
    /* this.$el.style.setProperty(
      "--colorPrimary",
      this.currentBussinesClub.theme.primary
    );
    this.$el.style.setProperty(
      "--colorSecondary",
      this.currentBussinesClub.theme.secondary
    ); */
    this.$el.style.setProperty("--colorButtom", "#525556");
    this.getImages();
  },
  data() {
    return {
      indexImg: null,
      bussinessPremium: [
        {
          businessName: "La casa del lobo",
          lat: "",
          long: "",
          imgUrl:
            "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "10234",
          likes: "1034"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "26",
          likes: "1023"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "26",
          likes: "1023"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "26",
          likes: "1023"
        }
      ],
      bestResults: [
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "56",
          likes: "1234"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://media-cdn.tripadvisor.com/media/photo-s/0e/c5/b5/dc/restaurante-los-galenos.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "56",
          likes: "134"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://s3-eu-west-1.amazonaws.com/gr-cms/media/filer_public/cc/ec/ccecba57-fc3a-4b94-911a-966ab4265c40/1.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "26",
          likes: "104"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://img.peru21.pe/files/ec_article_multimedia_gallery/uploads/2017/10/25/59f0fad00f10a.jpeg",
          address: "Quito, Ecuador",
          icon: "",
          views: "6",
          likes: "104"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://d3tp7kxwot1w6l.cloudfront.net/_novaimg/3198031-1056941_0_12_1600_1028_700_450.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "26",
          likes: "234"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://www.abc.es/Media/201509/11/restaurante-casa-juan--644x362.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "25",
          likes: "104"
        },
        {
          businessName: "Palmeras de la vida",
          lat: "",
          long: "",
          imgUrl:
            "https://www.infobae.com/new-resizer/vFq9q-pS58VbSr8izgYMPcIdOUw=/750x0/filters:quality(100)/s3.amazonaws.com/arc-wordpress-client-uploads/infobae-wp/wp-content/uploads/2018/07/01090537/Nuetsro-Secreto-2.jpg",
          address: "Quito, Ecuador",
          icon: "",
          views: "256",
          likes: "1004"
        }
      ],
      tabs: "tab-0",
      slickOptions: {
        slidesToShow: 3
      },
      images: [],
      categories: [
        "Todos",
        "Recientes",
        "Hoteles",
        "Centros Nocturnos",
        "Patios de Autos",
        "Academias de Baile",
        "Ropa y Accesorios",
        "Hospitales"
      ],
      currentView: "",
      auxTab: 0,
      canSave: true,
      nameUser: "",
      email: "",
      tabs: 0,
      tabsButtons: 0,
      nextTab: 0,
      searchPanel: true,
      drawer: true,
      mini: true,
      right: true
    };
  },
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  methods: {
    getImages() {
      this.bestResults.forEach(element => {
        this.images.push(element.imgUrl);
      });
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
.post-container-business-detail {
  margin: 5px 20px 0 0;
  overflow: auto;
}

.post-thumb-icon-business {
  float: left;
}
.post-content-business {
  margin-left: 27vh;
}

.wrapperGallery .close {
  color: white;
  font-size: 22px;
}
.wrapperGallery .next {
  border: none;
  background: transparent;
}
.wrapperGallery .prev {
  border: none;
  background: transparent;
}
</style>
