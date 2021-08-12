<template>
  <div class="custom_parent_stepper">
    <v-stepper v-model="controlStepper" v-if="$vuetify.breakpoint.mdAndUp" value="1">
      <div class="custom_header_stepper">
        <v-stepper-header>
          <div class="custom_step">
            <v-stepper-step step="1" editable>
              <v-layout>
                <span style="font-size:2.5rem;font-weight:200">01.</span>
                <v-flex class="align-center pl-1">
                  <span
                    style="font-size:0.8rem;font-weight:600"
                  >{{$t('message.shippingAddress').toUpperCase()}}</span>
                </v-flex>
              </v-layout>
            </v-stepper-step>
          </div>
          <div class="custom_step">
            <v-stepper-step step="2" editable>
              <v-layout>
                <span style="font-size:2.5rem;font-weight:200">02.</span>
                <v-flex class="align-center pl-1">
                  <span
                    style="font-size:0.8rem;font-weight:600"
                  >{{$t('message.billingAddress').toUpperCase()}}</span>
                </v-flex>
              </v-layout>
            </v-stepper-step>
          </div>
          <div class="custom_step">
            <v-stepper-step step="3" editable>
              <v-layout>
                <span style="font-size:2.5rem;font-weight:200">03.</span>
                <v-flex class="align-center pl-1">
                  <span
                    style="font-size:0.8rem;font-weight:600"
                  >{{$t('message.deliveryMethod').toUpperCase()}}</span>
                </v-flex>
              </v-layout>
            </v-stepper-step>
          </div>
          <div class="custom_step">
            <v-stepper-step step="4" editable>
              <v-layout>
                <span style="font-size:2.5rem;font-weight:200">04.</span>
                <v-flex class="align-center pl-1">
                  <span
                    style="font-size:0.8rem;font-weight:600"
                  >{{$t('message.paymentMethod').toUpperCase()}}</span>
                </v-flex>
              </v-layout>
            </v-stepper-step>
          </div>
        </v-stepper-header>
        <v-progress-linear
          :color="currentBussinesClub.theme.primary"
          class="ma-0 pa-0"
          height="4"
          v-model="stepProgress"
        ></v-progress-linear>
      </div>
      <v-stepper-content step="1" ripple="false">
        <v-step-one></v-step-one>
        <v-container fluid>
          <v-layout row wrap>
            <v-flex xs12>
              <v-card flat class="ma-0 pa-0">
                <v-card-actions class="ma-0 pa-0">
                  <v-spacer></v-spacer>
                  <v-btn
                    class="ma-0"
                    flat
                    :style="'background-color:'+currentBussinesClub.theme.primary"
                    color="white"
                    @click="controlStepper = 2"
                  >{{$t('message.continue')}}</v-btn>
                </v-card-actions>
              </v-card>
            </v-flex>
          </v-layout>
        </v-container>
      </v-stepper-content>
      <v-stepper-content step="2">
        <v-step-two></v-step-two>
        <v-container fluid>
          <v-layout row wrap>
            <v-flex xs12>
              <v-card flat class="ma-0 pa-0">
                <v-card-actions class="ma-0 pa-0">
                  <v-spacer></v-spacer>
                  <v-btn
                    flat
                    :style="'background-color:'+currentBussinesClub.theme.primary"
                    color="white"
                    @click="controlStepper = 3"
                  >{{$t('message.continue')}}</v-btn>
                </v-card-actions>
              </v-card>
            </v-flex>
          </v-layout>
        </v-container>
      </v-stepper-content>
      <v-stepper-content step="3">
        <v-step-three></v-step-three>
        <v-container fluid>
          <v-layout row wrap>
            <v-flex xs12>
              <v-card flat class="ma-0 pa-0">
                <v-card-actions class="ma-0 pa-0">
                  <v-spacer></v-spacer>
                  <v-btn
                    flat
                    :style="'background-color:'+currentBussinesClub.theme.primary"
                    color="white"
                    @click="controlStepper = 4"
                  >{{$t('message.continue')}}</v-btn>
                </v-card-actions>
              </v-card>
            </v-flex>
          </v-layout>
        </v-container>
      </v-stepper-content>
      <v-stepper-content step="4">
        <v-step-four></v-step-four>
        <v-container fluid>
          <v-layout row wrap>
            <v-flex xs12>
              <v-btn
                :loading="loading"
                :disabled="loading"
                class="ma-0"
                large
                box
                flat
                :style="'background-color:'+currentBussinesClub.theme.primary"
                color="white"
                @click="StartCheckOut()"
              >{{$t('message.placeOrder')}}</v-btn>
            </v-flex>
          </v-layout>
        </v-container>
      </v-stepper-content>
    </v-stepper>
    <v-stepper v-model="controlStepper" v-else vertical value="1">
      <div
        class="custom_step"
        :style="'border-left:0.3rem solid '+currentBussinesClub.theme.primary"
      >
        <v-stepper-step step="1" editable>
          <v-layout>
            <span style="font-size:2.5rem;font-weight:200">01.</span>
            <v-flex xs12 class="align-center pl-1">
              <span
                style="font-size:1rem;font-weight:600"
              >{{$t('message.shippingAddress').toUpperCase()}}</span>
            </v-flex>
          </v-layout>
        </v-stepper-step>
        <v-stepper-content step="1" ripple="false" class="ma-0 pa-0">
          <v-step-one></v-step-one>
          <v-container fluid>
            <v-layout row wrap>
              <v-flex xs12>
                <v-card flat class="ma-0 pa-0">
                  <v-card-actions class="ma-0 pa-0">
                    <v-spacer></v-spacer>
                    <v-btn
                      class="ma-0"
                      flat
                      :style="'background-color:'+currentBussinesClub.theme.primary"
                      color="white"
                      @click="controlStepper = 2"
                    >{{$t('message.continue')}}</v-btn>
                  </v-card-actions>
                </v-card>
              </v-flex>
            </v-layout>
          </v-container>
        </v-stepper-content>
      </div>
      <div
        class="custom_step"
        :style="controlStepper>=2?'border-left:0.3rem solid '+currentBussinesClub.theme.primary:''"
      >
        <v-stepper-step step="2" editable>
          <v-layout>
            <span style="font-size:2.5rem;font-weight:200">02.</span>
            <v-flex xs12 class="align-center pl-1">
              <span
                style="font-size:1rem;font-weight:600"
              >{{$t('message.billingAddress').toUpperCase()}}</span>
            </v-flex>
          </v-layout>
        </v-stepper-step>
        <v-stepper-content step="2" class="ma-0 pa-0">
          <v-step-two></v-step-two>
          <v-container fluid>
            <v-layout row wrap>
              <v-flex xs12>
                <v-card flat class="ma-0 pa-0">
                  <v-card-actions class="ma-0 pa-0">
                    <v-spacer></v-spacer>
                    <v-btn
                      flat
                      :style="'background-color:'+currentBussinesClub.theme.primary"
                      color="white"
                      @click="controlStepper = 3"
                    >{{$t('message.continue')}}</v-btn>
                  </v-card-actions>
                </v-card>
              </v-flex>
            </v-layout>
          </v-container>
        </v-stepper-content>
      </div>
      <div
        class="custom_step"
        :style="controlStepper>=3?'border-left:0.3rem solid '+currentBussinesClub.theme.primary:''"
      >
        <v-stepper-step step="3" editable>
          <v-layout>
            <span style="font-size:2.5rem;font-weight:200">03.</span>
            <v-flex xs12 class="align-center pl-1">
              <span
                style="font-size:1rem;font-weight:600"
              >{{$t('message.deliveryMethod').toUpperCase()}}</span>
            </v-flex>
          </v-layout>
        </v-stepper-step>
        <v-stepper-content step="3" class="ma-0 pa-0">
          <v-step-three></v-step-three>
          <v-container fluid>
            <v-layout row wrap>
              <v-flex xs12>
                <v-card flat class="ma-0 pa-0">
                  <v-card-actions class="ma-0 pa-0">
                    <v-spacer></v-spacer>
                    <v-btn
                      flat
                      :style="'background-color:'+currentBussinesClub.theme.primary"
                      color="white"
                      @click="controlStepper = 4"
                    >{{$t('message.continue')}}</v-btn>
                  </v-card-actions>
                </v-card>
              </v-flex>
            </v-layout>
          </v-container>
        </v-stepper-content>
      </div>
      <div
        class="custom_step"
        :style="controlStepper==4?'border-left:0.3rem solid '+currentBussinesClub.theme.primary:''"
      >
        <v-stepper-step step="4" editable>
          <v-layout>
            <span style="font-size:2.5rem;font-weight:200">04.</span>
            <v-flex xs12 class="align-center pl-1">
              <span
                style="font-size:1rem;font-weight:600"
              >{{$t('message.paymentMethod').toUpperCase()}}</span>
            </v-flex>
          </v-layout>
        </v-stepper-step>
        <v-stepper-content step="4">
          <v-step-four></v-step-four>
          <v-container fluid>
            <v-layout row wrap>
              <v-flex xs12>
                <v-btn
                  @click="StartCheckOut()"
                  :loading="loading"
                  :disabled="loading"
                  class="ma-0"
                  large
                  box
                  flat
                  :style="'background-color:'+currentBussinesClub.theme.primary"
                  color="white"
                >{{$t('message.placeOrder')}}</v-btn>
              </v-flex>
            </v-layout>
          </v-container>
        </v-stepper-content>
      </div>
    </v-stepper>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
import stepOneForm from "./stepOneForm";
import stepTwoForm from "./stepTwoForm";
import stepThreeForm from "./stepThreeForm";
import stepFourForm from "./stepFourForm";
export default {
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  data() {
    return {
      loading: false,
      color: "purple",
      controlStepper: 1,
      stepProgress: 25
    };
  },
  components: {
    "v-step-one": stepOneForm,
    "v-step-two": stepTwoForm,
    "v-step-three": stepThreeForm,
    "v-step-four": stepFourForm
  },
  watch: {
    controlStepper(val) {
      this.stepProgress = this.controlStepper * 25;
    }
  },
  methods: {
    StartCheckOut() {
      this.loading = true;
      this.$store
        .dispatch("startCheckOut")
        .then(res => {
          this.loading = false;
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.generateOrderSuccess"),
            type: "success"
          });
          this.loading = false;
        })
        .catch(err => {
          this.loading = false;
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.unknownError"),
            type: "error"
          });
        });
    }
  }
};
</script>

<style>
.custom_parent_stepper .v-stepper {
  -webkit-box-shadow: none !important;
  box-shadow: none !important;
}
.custom_header_stepper .v-stepper__header {
  flex-wrap: nowrap !important;
  -webkit-box-shadow: none;
  box-shadow: none;
}
.custom_step .v-stepper__step__step {
  display: none !important;
}
.custom_step .v-stepper__step {
  transition: 100ms;
  padding: 0.84rem !important;
}
.custom_step .v-stepper__step:hover {
  transition: 100ms;
  background-color: transparent !important;
}
.custom_step .v-ripple__container {
  color: transparent;
}
</style>
