<template>
  <div>
    <v-form v-model="validNewState">
      <v-text-field
        browser-autocomplete="off"
        :color="currentBussinesClub.theme.secondary"
        v-model="description"
        :rules="requiredField"
        :label="$t('message.description')"
        id="id"
      ></v-text-field>
    </v-form>
    <label style="font-size:13px">{{$t('message.attachFile')}}</label>
    <v-upload-button refName="attachmentTicket" ref="upload"></v-upload-button>
    <v-layout justify-end>
      <div v-if="$vuetify.breakpoint.mdAndUp">
        <v-btn
          @click="onActionButton('waiting')"
          :disabled="!validNewState"
          class="ma-0 mr-2 mb-2"
          :color="currentBussinesClub.theme.secondary"
        >
          <span style="color:white">{{$t('message.requestInformation')}}</span>
        </v-btn>
        <v-btn
          :color="currentBussinesClub.theme.secondary"
          @click="onActionButton('solved')"
          :disabled="!validNewState"
          class="ma-0 mb-2"
        >
          <span style="color:white">{{$t('message.solve')}}</span>
        </v-btn>
      </div>
      <v-flex xs12 v-if="$vuetify.breakpoint.smAndDown">
        <v-btn
          @click="onActionButton('waiting')"
          :disabled="!validNewState"
          :color="currentBussinesClub.theme.secondary"
        >
          <span style="color:white">{{$t('message.requestInformation')}}`</span>
        </v-btn>
        <v-btn
          @click="onActionButton('solved')"
          :disabled="!validNewState"
          :color="currentBussinesClub.theme.secondary"
        >
          <span style="color:white">{{$t('message.solve')}}</span>
        </v-btn>
      </v-flex>
    </v-layout>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  mounted() {
    this.$watch(
      () => {
        return this.$refs.upload.$refs.attachmentTicket.uploaded;
      },
      val => {
        if (val) {
          try {
            var noError = true;
            var attachements = [];
            var files = this.$refs.upload.$refs.attachmentTicket.files;
            if (files.length > 0) {
              try {
                for (var i = 0; i < files.length; i++) {
                  var contentType = files[i].response.data.mimetype;
                  var url = files[i].response.data.path.replace(/ /g, "%20");
                  var fileName = files[i].response.data.originalName;
                  var size = files[i].response.data.size;
                  this.additionalAttachments.push({
                    contentType: contentType,
                    url: {
                      url: url,
                      filename: fileName,
                      size: size
                    }
                  });
                }
              } catch (error) {
                noError = false;
                this.$refs.upload.$refs.attachmentTicket.emitInput();
                this.$refs.upload.$refs.attachmentTicket.clear();
              } finally {
                if (noError) {
                  this.dispatchState();
                }
              }
            }
          } catch (error) {}
        }
      }
    );
  },
  computed: {
    ...mapGetters(["selectedTicket", "currentBussinesClub"])
  },
  data() {
    return {
      validNewState: false,
      requiredField: [v => !!v || "Campo requerido"],
      description: "",
      additionalAttachments: [],
      action: ""
    };
  },
  methods: {
    dispatchState() {
            this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.savingInformation")
      );
      let payload = {
        ticketId: this.selectedTicket._id,
        description: this.description,
        attachment: this.additionalAttachments
      };
      if (this.action == "solved") {
        this.$store.dispatch("progressToSolved", payload).then(response=>{
        this.$root.$children[0].$emit("hideLoading");
        });
      } else {
        this.$store.dispatch("progressToWaiting", payload).then(response=>{
          this.$root.$children[0].$emit("hideLoading");
        });
      }
    },
    /* progressToSolved */
    onActionButton(action) {
      this.action = action;
      if (!this.$refs.upload.$refs.attachmentTicket.uploaded) {
        this.$refs.upload.$refs.attachmentTicket.active = true;
      } else {
        this.dispatchState();
      }
    }
  }
};
</script>
<style>
</style>

