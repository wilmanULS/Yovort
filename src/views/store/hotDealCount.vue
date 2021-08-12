     <template>
     <v-container class="no-padding">
           <v-container grid-list-sm class="no-padding">
       <v-layout align-end justify-center row fill-height class="paddingCardNumbers">
          <v-flex xs3 >
            <div class="borderNumber timeBox">
              <v-layout   align-center justify-center column fill-height>
                <span class="counterDownNumbers">{{days}}</span>
                <span class="textTime">Dias</span> 
              </v-layout>
             </div>
          </v-flex>
          <v-flex xs3 >
            <div class="borderNumber timeBox">
              <v-layout align-center justify-center column fill-height>
                <span class="counterDownNumbers">{{hours}}</span>
                <span class="textTime">Horas</span> 
              </v-layout>
             </div>
          </v-flex>
           <v-flex xs3 >
            <div class="borderNumber timeBox">
              <v-layout align-center justify-center column fill-height>
                <span class="counterDownNumbers">{{minutes}}</span>
                <span class="textTime">Min</span> 
              </v-layout>
             </div>
          </v-flex>
          <v-flex xs3 >
            <div class="borderNumber timeBox">
              <v-layout align-center justify-center column fill-height>
                <span class="counterDownNumbers">{{minutes}}</span>
                <span class="textTime">Seg</span> 
              </v-layout>
             </div>
          </v-flex>
        </v-layout>
     </v-container> 
     </v-container>
        
     </template>
     <script>

     export default {

          props: {
    startDate: String,
    endDate: String
    },
         data() {
    return {
      isNew: false,
      hoverDeal: false,
      timeInterval:null,
      countDownDate:0,
      now:0,
      days:0,
      hours:0,
      minutes:0,
      seconds:0,
      distance:0
    };
  },
    mounted(){
      this.startCountDown();
  },
  methods:{
      startCountDown(){
          this.countDownDate = new Date(this.endDate).getTime();
          this.timeInterval = setInterval(()=>{
              this.now = new Date().getTime();
              this.distance = parseFloat(this.countDownDate) - parseFloat(this.now);
              this.days = Math.floor(this.distance/(1000 * 60 * 60 * 24));
              this.hours = Math.floor((this.distance % (100 * 60 * 60 * 24))/(1000 * 60 * 60));
              this.minutes = Math.floor((this.distance % (1000 * 60 * 60)) / (1000 * 60));
              this.seconds = Math.floor((this.distance % (1000 * 60)) / 1000);
          },1000)
      }
  }
     }
     </script>
<style scoped>
.counterDownNumbers{
    color: #E62E04;
    font-size: 14px;
    font-weight: 700;
    line-height: 1;
}
.borderNumber{
width:45px!important;
height:48px!important;
}
.textTime{
color:#666666;
font-size:10pt;
font-weight:300; 
position:relative; top:0px
}
.paddingCardNumbers{
padding:0 20px 0 20px!important;
}
@media screen and (max-width: 600px) {
  .paddingCardNumbers{
padding:0 70px 0 70px!important;
}
}   
</style>
     

