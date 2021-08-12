<template>
  <v-card flat >
    <!--  <v-btn fab dark color="#FFA726" absolute style="left:10px; top:20px;" v-if="isNew">NEW</v-btn> -->
    <v-layout flat row wrap>
      <v-flex
        hover
        @mouseover="hoverDeal = true;"
        @mouseout="hoverDeal = false;"
        pt-2
        style="text-align:left"
        xs12
        md4
        sm4
        lg4
      >
        <v-card height="200px" flat color="transparent">
          <v-img
            key="image1"
            height="200px"
            src="https://andreamx.vteximg.com.br/arquivos/ids/1803501-1000-1000/Perfil-Normal.jpg?v=636636810087100000"
            v-show="!hoverDeal"
            class="animated fadeIn"
          ></v-img>
          <v-img
            key="image2"
            height="200px"
            src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBxAQEBUQDw8PFRUVEBUVFRUVFQ8VFRUVFRUXFxUVFRUYHSggGBolHRUVITEhJSkrLi4uFx8zODMtNygtLisBCgoKDQ0NFQ8PFS0ZFR0tLSs4LS4rKysuKy0rLSstKys3Ky4rKy0rKzcrKzc3LSstKysrKysrKysrLS0rKysrK//AABEIAOEA4QMBIgACEQEDEQH/xAAcAAEBAAIDAQEAAAAAAAAAAAAAAQMHAgQFBgj/xABMEAACAQICBAgGDQoGAwAAAAAAAQIDBAURBxIxUQYTIUFhcZGhIjKBkrHBJEJSU3SCk6KywsPR0xQVFyMzNGJyc7MlY2Sj0uFDRIP/xAAXAQEBAQEAAAAAAAAAAAAAAAAAAQID/8QAGREBAQEBAQEAAAAAAAAAAAAAAAERMQIS/9oADAMBAAIRAxEAPwDeAKAICgAQoAgKQACkAAFAgAAAoAgBQICkAAoAgAAAAACkAAFAAgAoAAAgAoIdPFsVt7SHG3NanSjnlnNpZvdFbZPoQHcKa1xfTJY081bUq9d5cjy4qnn0ufhrzT4XGtLGJ181SlTt4/5cVKeW5znn3JFyje2LYvb2lPjbmvTpR3zaWfRFbZPoWbPgsW0nOUJysKKmoqLg5KWtOOfhyVPkayXLlyvJN5cmRpC8valabnWq1Kk3tlOUpy7ZcplssSnS2cqzzy3PenzFwbawzTBrL9bSpS/llqvske7Q0o281mres+rJ8u7kT3o0pXxK3rPOtS8Lnkl4T6XKPjP+ZM+j4L8LadjQlQpNOMqjm3J5STlGMXlyR5ooqNiXWlKjD/1ay3a3g59qPFvtMTj4lCkuueb7j5PhXwop4hThSqzilCbmnHa3quPK3nvPm6dxaUXnClKclslPl5ehPKPl1QNrU9JdakqdatBOEqevUp5JTim3q6j5MpOOq8nvWw+04O8NbC+yVCvFTf8A4qngVPJF+N8VtH5svsSnWectm3LlfLvbe062uMH6+B+ZsF4eYla5KndVHFe0qfrY9Xh5tLqaPvMH0zp5K8tOudGX2c/+Rn5Vt0HicH+FdlfL2NXjKXPTlnGoviPla6Vmj2iAAUCAAACkAAACgEAoBAKCFA8LhlwjhhtpO5mlJ5qNOGeWvUexZ7sk2+iLPzTjmN3F7WlXuarnN+bFe5hH2sehenlNiaesVcrqjapvKlRdWW7WqycV5VGHzzVTRuJTMNhkKg2ACKIrCOWQHFHIZAohWQoAsWQqAzUK8oSUoycZJ5pptNNbGmtjNz6LOH1S4mrK9nrTafE1XlrSyWbpz3vJNp7Xk8+Y0qkd3DL2VCtTrQ8anUjNdcWn6hmj9Ygx29aNSEakXnGUVJPepLNekyHNQEAFBABQABACgQFIBSAoH5n0o3LqYvdvPxakYLqhThH0pnyjPX4X1+MxC8nvva68iqyiu5I8bM2gwySZzyCOJUhkckgIkUpCgwAgBSFQBomfMcjE5eFl0BWVMywMKM0AP0ro3veOwu2lnyxpcW+ulJ0/qo+mPgNClfWw1x9xc1I9qjP6zPvjF6qkBSAQAACgCAoAEKAICgD8j4rU169afuq9SXnTk/WdGR2b2OVSa3VJd0mdZm0caj2dZkgzBcvZ/MZ4ICnJHE5IIrIUFECKQAzszhTVGMk/Dc5JrPYlseWXV3nXZAD2HWT/AFnkR2J7DrRXh5/wkqu1HaZoGGCM0Swbr0EVfY9zDdXhLzoZfUNnmo9As+W8j0W776y+424YvVAAQAUgAAAAUgFAAEKQknkm+gD8i3v7Sf8AUl9JnWZmqyzbe9sws2jHWjnl/MjNFGGs+Rda9JnQBFJFHIqK0CMMAyFZACKGEBxmdak/D6l6zPMw0fG7fUSq7UDJExwMsSwbb0C+Pd/yUPTVNvmntAz/AFt0v8uj3Sn95uEx66oCkIAKAIAUAAQCgAAYrqWVOT3Qk+xMynUxd5W9V7qNT6LA/I8X4Kb3L0HGRyWxdSI0bRhreszxMNUyoDmgggVFDIVAAykYAoAGGozvfm7VsqVzl493cU/JCnbtd8pnRqn094l+ZLT4fdfRp/8ARFfOwOcThE5xKNraCH7IuP6EPps3MaW0Ev2VXX+nX0195ukx66oACCkAAAFAAEApAUAdHHf3Wv8AB6v0Gd46GPyytLh7req/9uQH5My5F1HBnN7F1GNm0cJbUZUS2gpVYRexzin1OSRnvKepVnBe1qTj2Sa9QGNFZC5lQRUQqAFYIwKCFAw1T6u+p5YFZv3V/dP5sY+mLPlKp9piLT4O2W+OIXMe3jJetEV8iiwZxOUCjaOgp+zK6/0v2kTdppLQV++1/gv2kDdhj11VBAQCkAAAAUAgFAAEPK4WSyw+7e6zrv8A2pHrHmcJ452Nyt9pWXbTkB+UZGFsyyfIYmbR2cGt+NuqFNe3uaMPPqRj6zPjbTu7hrZ+VV8urjZZHe0dwTxW1z2Kvr/JQlU+oeKqjnnN7ZPWfXLlfpA5IjKQIqKkRAo5ECYAFRCgY6qPoZ3DlglOD9pi1RL41rCS79c8Ca5D2LVOWEVV73idCb/+lvXh9REqvGTOdMxIywKNraB4+ybmW6hBds/+jdBpvQL+2uv6VL6Uzcpj11UBQQCFAAEKBCgACFAA87hD+53Hwar/AG5HonQx6OdpXW+3qr5kgPyUti6jgc1sXUcGbR73AWWpc1a3vNheVV1q3nBd80eLBZLI7uCVnTp3Ul7a04ry1bihmvMjU7DpgXMIhyCCIUFEDLkAIUhQDR7WD1f8NxGm+aVlVXy0qUv7qPGO7hFdRV1CWyrYVUl/FSnTrx/svtCvNRlgYjJTA25oC/a3j/goemqbjNRaAKf75Lf+Trs45+s26YvVAUEAhQBAUAAQoAAADrYnHOhVW+lNfNZ2TjOOaafOsu0D8eRfgrqXoOLM1alqScPcycfNbXqMSXKjaLTqNRaz5HJN9Ornl9JnPM9qpgijhdK8a5al9Wp/EVOGr86FTtPHUEBxRyfWXixxZRxLmOL6RxYQbIctTpJqAQZouoRwAuZjqTyeafM15JRcX3NoyKBnw/DZXNejbw8arWhTXPlryS1upZt+QK6cdi6jNE41aTh4L2xlKL64tp+g5QA3ZoDh7HuZb68F5sM/rG0zXGgqjq4fVn7u7l2Rp0195scxeqAgIKAQAAAKAAAAAAAD8l4/DVu7mL9rd14+bWmvUdGhHw49aPqdKWGO2xW4TjlGrPj4PeqvhSfn668h8tRnqyT3M3EbJ4XWqocH8Npc86kqz+PGc/tUa7R+huGPAOOI29tTp3HE/k9NxjnDXi4yjBZNayyy1F3nxc9CtyvFvaD64VI+hsbBq8ZGy5aGr7mubN9brL6jMb0N4hzV7Lz6/wCGNg1zkRmxXocxL36x+Ur/AIQ/Q1iPv9j59f8ADGwxrhsI2MtDOI89ex8+v+GHobxLmrWL+PXX2Q2DXTRDYv6HMT99sPlK/wCET9DmJ++2Pylf8IumNdH1Wi9x/PFprLPw6mXXxNTJ9p70NDWI89exXx67+yPV4L6Lb60vqFzOtaOFKqpS1ZVtZrJppJwSz5d5Ng13w5sfyfELilzK4qyXRGdSU490keLA+6020FDFW0vHtqU315zh6II+D1skUfonQzS1cIpSy8erWl15VJR+qfbnj8DsOdtYW1CSylChDWX8bWtPvbPZOdUIUgFAAAEKAICgCFAAAAal0/Wq4u1q5LNTq02+iUYyS+YzTThyG8dPcPYNu914l20av3Gkqcc2lvkl3m5xK/W9GOUYrdFLuOYBhQAAAABSAACgAQpCgaV082iVzbVvd0Jw+TmpL+6z4TgrQVS/tYNJqV3RTW9cZHPuNlafIclpLprrt4p+o17wEX+J2nwqn6TpOI/TgKQ5qFAAAgAFAAAgAoBAKAAOnieGULqHF3NGnVhrKWrOKks1seT5+Vnl0eBOFwkpxw+1UotNPi4vJrY0nyH0BAAAAoIABSACgEAoIUACADpYphFtdRUbq3pVVF5xVSMZare1xz2eQ6VlwRw6hUjVo2VvCcXnGShHOL3p8zPbIAKCAUEKAIAABQBAUACFAEKAAAAEBQAAAAhQAAAAgAFAAEKAAAAAAAAAAAAH/9k="
            class="animated fadeIn"
            v-show="hoverDeal"
          ></v-img>
        </v-card>
      </v-flex>
      <v-flex xs12 md8 sm8 lg8 class="pt-0 mt-0">
        <v-card-title primary-title class="pt-0 mt-0 pb-0 mb-0">
          <div>
            <span
              class="darkTitles headline titleProductsListView"
            >Accessorize with a straw hat</span><br>
            <span style="font-size:21px!important; " class="darkTitles pb-0 ">$15.19</span>
            <v-flex xs12>
                <span
              class="priceProductSubrayed"
            >$14.19</span>
            </v-flex>
            <span
              class="textDescription"
            >Faded short sleeves t-shirt with high neckline. Soft and stretchy material for a comfortable fit. Accessorize with a straw hat and you're ready for summer!Faded short sleeves t-shirt with high neckline. Soft and stretchy material.</span>
          </div>
        </v-card-title>

        <v-card-actions class="pd-top pl-3 mt-0">
          
              <v-flex v-if="$vuetify.breakpoint.lgAndUp" lg4 xs3>
            <v-btn dark :color="currentBussinesClub.theme.primary">{{$t('message.addToCar')}}</v-btn>
          </v-flex>

          <v-flex class="pl-3" v-if="$vuetify.breakpoint.lgAndUp" xs4>
            <v-icon style="font-size:25pt;">share</v-icon>
            <span class="pl-3 colorTextIcons">{{$t('message.share')}}</span>
          </v-flex>
          <v-flex v-if="$vuetify.breakpoint.lgAndUp" xs4>
            <v-icon @click="likes()" v-if="actualLike" style="font-size:25pt;">ti-heart</v-icon>
            <v-icon
              @click="likes()"
              v-if="!actualLike"
              :color="currentBussinesClub.theme.secondary"
              class="animated rubberBand"
              style="font-size:25pt;"
            >fas fa-heart</v-icon>
            <span class="pl-3 colorTextIcons">{{$t("message.favorite")}}</span>
          </v-flex>
          <v-layout row wrap>
          <v-flex v-if="$vuetify.breakpoint.mdAndDown" xs12 class="text-xs-center">
            <div class="pb-3">
              <v-btn dark  :color="currentBussinesClub.theme.primary">
              {{$t("message.add")}}
              <v-icon style="padding-left:5px!important ;font-size:12pt;">add_shopping_cart</v-icon>
            </v-btn>
            </div>
          </v-flex>
          <v-flex  v-if="$vuetify.breakpoint.mdAndDown" xs6 class="text-xs-center" >
            <div>
             
                <v-icon small class="pr-4">share</v-icon ><span class="colorTextIcons">{{$t('message.share')}}</span>
            </div>
             </v-flex>
             <v-flex  v-if="$vuetify.breakpoint.mdAndDown" xs6 class="text-xs-center">
          <div>
       
            <v-icon small @click="likes()" v-if="actualLike">ti-heart</v-icon>
            <v-icon
              small
              @click="likes()"
              v-if="!actualLike"
              :color="currentBussinesClub.theme.secondary"
              class="animated rubberBand"
            >fas fa-heart</v-icon>
            <span class="pl-3 colorTextIcons">{{$t("message.favorite")}}</span>
            </div>      
          </v-flex>
          </v-layout>
        
        </v-card-actions>
      </v-flex>
    </v-layout>
    <v-divider></v-divider>
  </v-card>
</template>
 <script>
 import { mapGetters } from "vuex";
export default {
     computed:{
     ...mapGetters(["currentBussinesClub"])
  },
  created() {
    this.actualLike = this.like;
  },
  data() {
    return {
      actualLike: true,
      hoverDeal: false
    };
  },
  props: {
    like: Boolean,
    isNew: Boolean
  },
  methods: {
    likes() {
      this.actualLike = !this.actualLike;
    }
  }
};
</script>
 <style>
 .priceProductSubrayed{
   font-size:13px!important; ; 
   text-decoration-line:line-through; 
   color:#757575;
 }
 .titleProductsListView{
font-size:14pt!important;
 padding-bottom:10px!important;
 }
 .colorTextIcons{
   color:#363636!important;
 }
 .textDescription{
font-size:14px!important; 
color:#757575;
 }
 .darkTitles{
   color: black!important;
 }
.heart3 {
  max-height: 70px;
  fill: white;
  stroke: white;
  cursor: pointer;
}
.pd-top {
  padding-top: 20px !important;
}
@media (max-width: 600px) {
  .pd-top {
    padding-top: 15px !important;
  }
}
</style>
 