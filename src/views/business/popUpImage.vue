<template>
<v-dialog class="borderCards" v-model="open" :overlay="true" max-width="600px" transition="dialog-transition" lazy>
   
    <v-card class="borderCards">
         <vue-perfect-scrollbar
          class="scroll-area"
          :class="{'dialog-scroll':!$vuetify.breakpoint.xsOnly, 'dialog-full-scroll':$vuetify.breakpoint.xsOnly}"
          :settings="settings"
        >
        <v-img v-if="imageName" :src="image">
            <v-layout style="margin-right: 16px;" align-start justify-end fill-height>
                <v-btn style="height: 30px!important ; width: 30px!important;position:fixed" fab dark :color="colorBackground">
                    <v-icon color="white" @click="$emit('onCancel')">close</v-icon>
                </v-btn>
            </v-layout>
        </v-img>
        </vue-perfect-scrollbar>
    </v-card>
</v-dialog>
</template>
<script>
export default {
    props: {
        imageName: String,
        image: String,
        colorBackground:String
    },
    data() {
        return {
            open: false,  
            settings: {
              maxScrollbarLength: 160
            }
        }
    },
    methods: {
        openDialog() {
            this.open = true;
        },
        close() {
            this.open = false;
        }
    }
}
</script>
