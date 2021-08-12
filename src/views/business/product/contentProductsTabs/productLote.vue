<template>
    <v-container>
        <v-layout row wrap>
            <v-flex xs12 md3>
             <v-card height="300px" class="cardAdd borderCards">
                  <v-container class="no-padding">
                    <v-layout row wrap>
                      <v-flex xs12 text-xs-center class="center">
                        <div>
                          <v-btn
                            small
                            :color="currentBussinesClub.theme.primary"
                            fab
                            dark
                            @click="openFormLoteDialog"
                          >
                            <v-icon>mdi mdi-plus</v-icon>
                          </v-btn>
                        </div>
                        <span class="infoCardText">{{$t('message.vProductCreateNewLote')}}</span>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-card>               
            </v-flex>
        </v-layout>
        <v-form-lote-dialog ref="loteDialog" @onClose="closeFormLoteDialog()"></v-form-lote-dialog>
    </v-container>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import formLoteDialog from "./formLoteDialog"
export default {
    data(){
        return{
        }
    },
        computed: {
    ...mapGetters([
      "currentBussinesClub",
    ]),
  },
    
    components:{
        "v-form-lote-dialog":formLoteDialog
    },
    methods:{
        openFormLoteDialog(){
         
    
         this.$refs.loteDialog.openDialog()   
        },
        closeFormLoteDialog(){
     
          this.$refs.loteDialog.closeDialog()   
        }
    } 
}
</script>