<template>
    <Page class="page">
        <ActionBar title="Home" class="action-bar" />
        <ScrollView>
            <StackLayout class="home-panel">
                <Label :text="'Latitude : ' + ajoutPhotoLatitude" />
                <Label :text="'Longitude : ' + ajoutPhotoLongitude" />
                <Button text="Géolocalisation" @tap="geolocalise" />
                <Label text="Choisissez une ville :" />
                <ListPicker :items="getTableauSeries" v-model="selectedListPickerIndex" />
                <Button text="Ajouter une photo" @tap="takePicture" />
            </StackLayout>
        </ScrollView>
    </Page>
</template>

<script>
    
    
    import * as camera from "nativescript-camera";
    import axios from "axios";
    import { Image } from "tns-core-modules/ui/image";
    var geolocation = require("nativescript-geolocation");
    import { Accuracy } from "tns-core-modules/ui/enums"; 
    import { isEnabled, enableLocationRequest, getCurrentLocation, watchLocation, distance, clearWatch } from "nativescript-geolocation";
    export default {
      data() {
            return {
                ajoutPhotoLongitude: 0,
                ajoutPhotoLatitude: 0,
                selectedListPickerIndex: 0,
                idUploadedImage: null,
                imageTaken: null,
                series: [],
                selectedSerieId: null,
                tableauTypeSerie: [],
            };
        },

        methods: {

          geolocalise(){
                var self = this
                geolocation.enableLocationRequest()
                var location = geolocation.getCurrentLocation({
                  desiredAccuracy: 3,
                  updateDistance: 10,
                  maximumAge: 2000,
                  timeout: 20000
                })
                .then(function(location){
                  self.ajoutPhotoLatitude = location.latitude
                  self.ajoutPhotoLongitude =  location.longitude
                }).catch(error => {
                  console.log(error)
                })
              },

          takePicture() {
              /* Méthode qui lance l'application de pour prendre une photo puis upload l'image*/
			    camera.requestPermissions()
                .then(() => {
                    camera.takePicture({ width: 300, height: 300, keepAspectRatio: true, saveToGallery:false })
                    .then(imageAsset => {
                        let img = new Image()
                        img.src = imageAsset
                        this.imageTaken = img
                        this.postUploadImage()
                    })
                    .catch(e => {
                        console.log('error:', e);
                    });
                })
                .catch(e => {
                    console.log('Error requesting permission');
                });
		    },

            getAllSeries() {
              /* Méthode qui retourne la liste des toutes les séries de la BDD */
                axios.get('https://felix18u.pagekite.me/series', 
                {
                    proxy: {
                        host: 'http://www-cache.iutnc.univ-lorraine.fr/',
                        port: 3128
                    },

                }).then(response => {
                  this.series = response.data
                }).catch(error => {
                    console.log(error)
                })
            },

            postUploadImage() {
              /* Méthode qui upload l'image et enregistre l'id de l'image */
                console.log("postUploadImage")
                console.log("enregistrer id photo")

                /* this.imageTaken */

                const base64String = android.util.Base64.encodeToString(this.imageTaken.readSync(), android.util.Base64.NO_WRAP);

                this.selectedSerieId = this.tableauTypeSerie[this.selectedListPickerIndex]

                axios.post('https://felix18u.pagekite.me/photos/mobile/' + this.selectedSerieId, 
                        {

                          base64 : base64String,
                        }, {
                          headers:{
                            'Content-Type': 'application/json',
                          }
                        }) 
                        .then(response => {
                          console.log(response)
                            this.idUploadedImage = response.data.id
                            this.putModifyImage()
                        })
                        .catch(error => {
                            console.log(error)
                        })
            },


            putModifyImage() {
              /* Méthode qui modifie la position de la photo via l'id de l'image */
                
                axios.put('http://felix18u.pagekite.me:8081/photos/info/' + this.idUploadedImage, {
                        lon: "" + this.ajoutPhotoLongitude,
                        lat: "" + this.ajoutPhotoLatitude
                }, {
                            headers: {
                                'Content-Type': 'application/json'
                            }
                        },
                    ).then(response => {
                        
                    })
                    .catch(error => {
                        })
                    .finally(() => {
                        })
            },
        },

       created() {
          this.getAllSeries()
        },

        computed: {
            getTableauSeries: function(){
              let tableauSerie = []
              this.series.forEach(element => {
                tableauSerie.push(element.ville)
              });
              this.tableauTypeSerie = tableauSerie
              return tableauSerie
            },
        },

        
    };
</script>

<style scoped>
    .home-panel {
        vertical-align: center;
        font-size: 20;
        margin: 15;
    }

    .description-label {
        margin-bottom: 15;
    }
</style>