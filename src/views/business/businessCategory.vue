<template>
<v-flex xs12 md12>
   
            <span class="titleCard darker-titles">{{$t('message.certifications')}}</span>
            <v-btn small :color="currentBussinesClub.theme.primary" fab dark absolute right @click="openCertificationDialog(-1)">
                <v-icon>add</v-icon>
            </v-btn>
      
            <v-container class="no-padding">
                <v-layout column fill-height>
                    <span class="noContent">{{$t('message.emptyCertification')}}</span>
                </v-layout>       
                 <v-data-table
    :headers="headers"
    :items="desserts"
    class="elevation-1"
  >
    <template v-slot:items="props">
      <td>{{ props.item.name }}</td>
      <td class="text-xs-left">{{ props.item.calories }}</td>
      <td class="text-xs-left">{{ props.item.fat }}</td>
 
    </template>
  </v-data-table>

            </v-container>

       
</v-flex>
</template>

<script>
import Vue from "vue";
import confirmDialog from "Components/ySocialHome/confirmDialog";
import {
    mapGetters
} from "vuex";

export default {
    components: {
        "v-confirm-dialog": confirmDialog
    },
    props: {
        'certifications': Array,
    },
    data() {
        return {

   headers: [
          {
            text: 'Categorías',
            align: 'center',
            sortable: true,
            value: 'name'
          },
          { text: 'Subcatetgorías', value: 'calories', sortable: true, },
          {  sortable: true, },
        
        ],
        desserts: [
          {
            name: 'Frozen Yogurt',
            calories: 159,
            fat: 6.0,
            carbs: 24,
            protein: 4.0,
            iron: '1%'
          },
          {
            name: 'Ice cream sandwich',
            calories: 237,
            fat: 9.0,
            carbs: 37,
            protein: 4.3,
            iron: '1%'
          },
          {
            name: 'Eclair',
            calories: 262,
            fat: 16.0,
            carbs: 23,
            protein: 6.0,
            iron: '7%'
          },
          {
            name: 'Cupcake',
            calories: 305,
            fat: 3.7,
            carbs: 67,
            protein: 4.3,
            iron: '8%'
          }
        ],

            certificationRules: [],
            indexCertification: -1,
            certificationDialog: false,
            certificationDialogModified: false,
            certification: {
                name: "",
                institution: "",
                year: ""
            },
            yearsArray: [],
            settings: {
                maxScrollbarLength: 160
            }
        };
    },
    computed: {
        ...mapGetters(["currentBussinesClub"]),
        sortedCertifications: function() {
            return this.certifications.sort((a, b) => {
                if (a.year > b.year)
                    return -1;
                if (a.year < b.year)
                    return 1;
                if (a.name < b.name)
                    return -1;
                if (a.name > b.name)
                    return 1;
                if (a.institution < b.institution)
                    return -1;
                if (a.institution > b.institution)
                    return 1;
                return 0;
            });
        },
    },
    watch: {
        /* Certification dialog watch fields */
        'certification.name'(value) {
            this.certificationDialogModified = true;
        },
        'certification.institution'(value) {
            this.certificationDialogModified = true;
        },
        'certification.year'(value) {
            this.certificationDialogModified = true;
        },
    },
    created() {
        this.certificationRequiredName = [v => !!v || '* ' + this.$t('message.certificationRequiredName')];
        this.certificationRequiredInstitution = [v => !!v || '* ' + this.$t('message.certificationRequiredInstitution')];
        this.certificationRequiredYear = [v => !!v || '* ' + this.$t('message.certificationRequiredYear')];

        let end = new Date().getFullYear();
        for (let year = end; year >= 1950; year--) {
            this.yearsArray.push(year);
        }
    },
    methods: {
        /**
         * Open the certification dialog
         */
        openCertificationDialog(index) {
            this.indexCertification = index;
            if (index < 0) {
                this.certification.name = "";
                this.certification.institution = "";
                this.certification.year = ""
            } else {
                this.certification.name = this.certifications[index].name;
                this.certification.institution = this.certifications[index].institution;
                this.certification.year = this.certifications[index].year;
            }
            this.$refs.certification.doReset()
            this.certificationDialogModified = false;
            this.certificationDialog = true
        },

        /**
         * Discard the changes into the certification dialog
         */
        discardCertification() {
            this.$refs.discardCertification.close();
            this.certificationDialog = false;
        },

        /**
         * Close the active certification dialog
         */
        closeCertification() {
            if (this.certificationDialogModified) {
                this.$refs.discardCertification.openDialog();
            } else {
                this.certificationDialog = false;
            }
        },

        /**
         * Save the certification dialog information
         */
        saveCertification() {
            if (!this.$refs.certification.doValidate()) {
                return;
            }

            if (this.indexCertification >= 0) {
                this.certifications[this.indexCertification].name = this.certification.name;
                this.certifications[this.indexCertification].institution = this.certification.institution;
                this.certifications[this.indexCertification].year = this.certification.year;
            } else {
                this.certifications.push({
                    name: this.certification.name,
                    institution: this.certification.institution,
                    year: this.certification.year,
                });
            }

            this.saveEducationInfo(this.certifications, () => {
                this.certificationDialog = false;
            });
        },

        /**
         * Show a popup to confirm the certification removal
         */
        askDeleteCertification() {
            if (this.indexCertification < 0) {
                return;
            }

            this.$refs.deleteCertification.openDialog();
        },

        /**
         * Delete the current certification
         */
        deleteCertification() {
            if (this.indexCertification < 0) {
                return;
            }

            /* Remove the certification */
            this.certifications.splice(this.indexCertification, 1);

            /* Update into the Business Club */
            this.$refs.deleteCertification.close();
            this.saveEducationInfo(this.certifications, () => {
                this.certificationDialog = false;
            });

        },

        /**
         * Store the profile information into the Bussines Club service
         */
        saveEducationInfo(value, cb) {
            const educationInformation = {
                type: 'education',
                field: 'certifications',
                education: value,
            };
            this.$store.dispatch("updateProfile", educationInformation)
                .then(() => {
                    this.$store.dispatch("doNotification", {
                        msg: this.$t('message.infoUpdate'),
                        type: 'success'
                    });
                }).catch(() => {
                    this.$store.dispatch("doNotification", {
                        msg: this.$t("message.sessionErrorDefault"),
                        type: 'error'
                    });
                }).finally(() => {
                    if (cb) {
                        cb();
                    }
                });
        },
    }
};
</script>
