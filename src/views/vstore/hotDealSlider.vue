<template>
<v-container class="no-padding" >
        <v-layout row wrap class="pb-0">
         <v-flex xs6 md6 class=" pb-0 pl-1">
             <span class="pt-2 pb-0 headline titleBusines">{{$t('message.hotBusiness')}}</span>
             </v-flex>           
             <v-flex xs6 md6 class="text-xs-right">
                        <div class="pb-3 mt-0">                
                <v-btn  icon small flat  @click="swiper.slidePrev()" >
                    <v-icon color="primaryPurple">zmdi-arrow-left</v-icon>
                </v-btn>
                <v-btn icon small flat  @click="swiper.slideNext()"  >
                    <v-icon color="primaryPurple">zmdi-arrow-right</v-icon>
                </v-btn>
                  </div> 
             </v-flex>  
        </v-layout>
        <v-container pr-1 pl-1 pt-0>
             <swiper :options="swiperOption2" ref="mySwiper2">
                <swiper-slide  v-for="product in hotDealsProducts" :key="product.title" >
                    <v-hot-deal-item :like="false" :isNew="true"  :showcounter="true" :startDate="product.dealEndTime" :endDate="product.dealEndTime"></v-hot-deal-item>
                </swiper-slide>
             </swiper>
        </v-container>
       
</v-container>
</template>

<script>
import { mapGetters } from "vuex";
import moment from 'moment';
import hotDealItem from './hotDealItem';
export default {
    components:{
        'v-hot-deal-item':hotDealItem
    },
    data() {
        return {
            hoverDeal:'',
            hoverAddToCart:'',
            swiperOption2: {
                slidesPerView:5,
                spaceBetween:7,
                 breakpoints: {
                    // when window width is <= 320px
                    360: {
                    slidesPerView: 1,
                    spaceBetween: 5
                    },
                    // when window width is <= 480px
                    480: {
                    slidesPerView: 1,
                    spaceBetween: 6
                    },
                    // when window width is <= 640px
                    580: {
                    slidesPerView: 3,
                    spaceBetween: 3
                    },
                    680: {
                    slidesPerView: 3,
                    spaceBetween: 3
                    },
                    780: {
                    slidesPerView: 3,
                    spaceBetween: 3
                    },
                    900: {
                    slidesPerView: 4,
                    spaceBetween: 6
                    },
                     1250: {

                    slidesPerView: 4,
                    spaceBetween: 6
                    },
                    1270: {

                    slidesPerView: 5,
                    spaceBetween: 8
                    },
                    1370: {

                    slidesPerView: 5,
                    spaceBetween: 8
                    },
                    1470: {

                    slidesPerView: 5,
                    spaceBetween: 8
                    },
                    1570: {

                    slidesPerView: 5,
                    spaceBetween: 8
                    }
                }

            },
            hotDealsProducts:[
                {
                    title:"producto 1",
                    discountPrice: 84.45,
                    mainPicture:'https://cdn.vuetifyjs.com/images/cards/halcyon.png',
                    secondPicture:'/static/img/25.jpg',
                    price:105.50,
                    discountPercentage:20,
                    description:'Primer producto de prueba de hot deals',
                    isNew:false,
                    dealStartTime:'Fri Feb 01 2019 14:54:07 GMT-0500',
                    dealEndTime:'Fri Feb 05 2019 14:54:07 GMT-0500'
                },
                {
                    title:"producto 2",
                    discountPrice: 50.00,
                    mainPicture:'https://cdn.vuetifyjs.com/images/cards/halcyon.png',
                    secondPicture:'/static/img/43.jpg',
                    price:100.00,
                    discountPercentage:50,
                    description:'Segundo producto de prueba de hot deals',
                    isNew:true,
                    dealStartTime:'Fri Feb 01 2019 14:54:07 GMT-0500',
                    dealEndTime:'Fri Feb 06 2019 18:30:07 GMT-0500'
                },
                {
                    title:"producto 3",
                    discountPrice: 50.00,
                    mainPicture:'https://cdn.vuetifyjs.com/images/cards/halcyon.png',
                    secondPicture:'/static/img/31.jpg',
                    price:100.00,
                    discountPercentage:50,
                    description:'Tercer producto de prueba de hot deals',
                    isNew:false,
                    dealStartTime:'Fri Feb 01 2019 14:54:07 GMT-0500',
                    dealEndTime:'Fri Feb 05 2019 05:10:07 GMT-0500'
                },
                {
                    title:"producto 4",
                    discountPrice: 50.00,
                     mainPicture:'https://cdn.vuetifyjs.com/images/cards/halcyon.png',
                    secondPicture:'/static/img/9.jpg',
                    price:100.00,
                    discountPercentage:50,
                    description:'Cuarto producto de prueba de hot deals',
                    isNew:false,
                    dealStartTime:'Fri Feb 01 2019 14:54:07 GMT-0500',
                    dealEndTime:'Fri Feb 05 2019 16:20:07 GMT-0500'
                },
                {
                    title:"producto 5",
                    discountPrice: 50.00,
                     mainPicture:'https://cdn.vuetifyjs.com/images/cards/halcyon.png',
                    secondPicture:'/static/img/7.jpg',
                    price:100.00,
                    discountPercentage:50,
                    description:'Quinto producto de prueba de hot deals',
                    isNew:true,
                    dealStartTime:'Fri Feb 01 2019 14:54:07 GMT-0500',
                    dealEndTime:'Fri Feb 05 2019 10:15:07 GMT-0500'
                },
                 {
                    title:"producto 6",
                    discountPrice: 50.00,
                     mainPicture:'https://cdn.vuetifyjs.com/images/cards/halcyon.png',
                    secondPicture:'/static/img/11.jpg',
                    price:100.00,
                    discountPercentage:50,
                    description:'Sexto producto de prueba de hot deals',
                    isNew:false,
                    dealStartTime:'Fri Feb 01 2019 14:54:07 GMT-0500',
                    dealEndTime:'Fri Feb 05 2019 14:54:07 GMT-0500'
                },
                 {
                    title:"producto 7",
                    discountPrice: 50.00,
                     mainPicture:'https://cdn.vuetifyjs.com/images/cards/halcyon.png',
                    secondPicture:'/static/img/11.jpg',
                    price:100.00,
                    discountPercentage:50,
                    description:'Septimo producto de prueba de hot deals',
                    isNew:false,
                    dealStartTime:'Fri Feb 01 2019 14:54:07 GMT-0500',
                    dealEndTime:'Fri Feb 05 2019 14:54:07 GMT-0500'
                },
                {
                    title:"producto 8",
                    discountPrice: 50.00,
                    mainPicture:'https://cdn.vuetifyjs.com/images/cards/halcyon.png',
                    secondPicture:'/static/img/11.jpg',
                    price:100.00,
                    discountPercentage:50,
                    description:'Octavo producto de prueba de hot deals',
                    isNew:false,
                    dealStartTime:'Fri Feb 01 2019 14:54:07 GMT-0500',
                    dealEndTime:'Fri Feb 05 2019 14:54:07 GMT-0500'
                }
            ]
        }
    },
    computed: {
        swiper() {
            return this.$refs.mySwiper2.swiper;
        }
    },
    mounted(){
    }
}
</script>
<style scope>
.titleBusines{
color:#535353!important; 
font-weight:bold;
font-size:20pt!important 
}
@media screen and (max-width: 600px) {
  .titleBusines{
color:#535353!important; 
font-weight:bold;
font-size:14pt!important 
}
}
</style>

