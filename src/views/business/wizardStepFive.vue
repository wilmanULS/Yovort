<template>
  <v-form data-vv-scope="step5">
    <v-layout row wrap>
      <v-flex xs12 style="padding-bottom:0px">
        <v-flex style="text-align: start;" xs10 md8 sm8 lg8 class="mt-3 ml-2">
          <span>{{$t('message.addFiles')}}</span>
          <v-btn
            @click="pickFileDocument()"
            small
            fab
            dark
            :color="currentBussinesClub.theme.primary"
          >
            <v-icon dark>fas fa-file-upload</v-icon>
          </v-btn>
          <file-upload
            class="btn btn-primary"
            :post-action="postAction"
            :multiple="true"
            :value="files"
            @input="inputUpdate(this)"
            ref="uploadFiles"
          ></file-upload>
        </v-flex>
      </v-flex>
      <v-flex xs12 md12 lg12 class="pb-4">
        <v-card-text class="borderCards">
          <v-container>
            <v-layout align-center justify-center column fill-height v-if="files.length<1 ">
              <span class="noContent">{{$t('message.emptyFile')}}</span>
            </v-layout>
            <template>
              <v-layout row wrap>
                <div v-for="(file,index) in files" :key="index">
                  <v-chip
                    outline
                    :color="currentBussinesClub.theme.primary"
                    :ref="'chip'+index"
                    close
                    @input="deleteFile(index)"
                  >{{file.name | truncate(25, '...')}}</v-chip>
                </div>
              </v-layout>
            </template>
          </v-container>
        </v-card-text>
      </v-flex>
    </v-layout>
  </v-form>
</template>
<script>
import FileUpload from "vue-upload-component";

import Vue from "vue";

import { mapGetters } from "vuex";

import AppConfig from "Constants/AppConfig";

export default {
  components: {
    FileUpload
  },
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "coordinatesSelected",
      "currentUserBusiness"
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
      files: [
        {
          name: "prueba"
        }
      ],
      postAction: ""
    };
  },
  methods: {
    pickFileDocument() {
      this.$refs.uploadFiles.$children[0].$el.click();
    },
    inputUpdate(files) {
      //this.$store.dispatch("updateFilesDocument", this.$refs.uploadFiles.files);
      this.$refs.uploadFiles.active = true;
    },
    deleteFile(index) {
      this.files.splice(index, 1);
    }
  }
};
</script>
<style>
.borderCards {
  border-style: solid;
  border-width: 1px;
  border-color: #cfd8dc;
}
</style>
