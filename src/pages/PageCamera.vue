<template>
    <q-page class="constrain-more q-pa-md">
        <div class="camera-frame q-pa-md">
            <video v-show="!imageCaptured" playsinline ref="video" class="full-width" autoplay/>
            <canvas v-show="imageCaptured" ref="canvas" class="full-width" height="240" />
        </div>
        <div class="text-center q-pa-md">
            <q-btn
              v-if="hasCameraSupport"
              @click="captureImage"
              color="grey-10"
              icon="eva-camera"
              round />
            
            <q-file
              label="Choose an image"  
              v-else
              v-model="imageUpload"
              @input="captureImageFallback"
              accept="image/*"
              outlined>
                <template v-slot:prepend>
                    <q-icon name="eva-attach-outline" />
                </template>
            </q-file>
        </div>
        <div class="row justify-center q-ma-md">
            <q-input
              v-model="post.caption"
              class="col col-sm-6"
              label="Caption" 
              dense/>
        </div>
        <div class="row justify-center q-ma-md">
            <q-input
              v-model="post.location"
              :loading="locationLoading"
              class="col col-sm-6"
              label="Location" 
              dense>
                <template v-slot:append>
                    <q-btn
                      v-if="!locationLoading && locationSupported"  
                      @click="getLocation"
                      icon="eva-navigation-2-outline"
                      dense
                      flat
                      round />
                </template>
            </q-input>
        </div>
        <div class="row justify-center q-mt-lg">
            <q-btn color="primary" label="Post Image" rounded unelevated/>
        </div>
    </q-page>
</template>

<script>
    import { uid } from "quasar";
    require('md-gum-polyfill');

    export default {
        name: 'PageCamera',
        data() {
            return {
                post: {
                    id: uid(),
                    caption: '',
                    location: '',
                    photo: null,
                    date: Date.now()
                },
                imageCaptured: false,
                imageUpload: [],
                hasCameraSupport: true,
                locationLoading: false
            }
        },
        computed: {
            locationSupported() {
                if('geolocation' in navigator) return true
                return false
            }
        },
        methods: {
            //methods for camera
            initCamera() {
                navigator.mediaDevices.getUserMedia({
                    video: true
                }).then(stream => {
                    this.$refs.video.srcObject = stream
                }).catch(error => {
                    this.hasCameraSupport = false
                })
            },
            captureImage() {
                let video = this.$refs.video
                let canvas = this.$refs.canvas
                canvas.width = video.getBoundingClientRect().width
                canvas.height = video.getBoundingClientRect().height
                let context = canvas.getContext('2d')
                context.drawImage(video, 0, 0, canvas.width, canvas.heigth)
                this.imageCaptured = true
                this.post.photo = this.dataURItoBlob(canvas.toDataURL())
                this.disableCamera()
            },
            disableCamera() {
                this.$refs.video.srcObject.getVideoTracks().fprEach(track => {
                    track.stop()
                })
            },
            captureImageFallback(file) {
                this.post.photo = file
                let canvas = this.$refs.canvas
                let context = canvas.getContext('2d')

                var reader = new FileReader()
                reader.onload = (event) => {
                    var img = new Image()
                    img.onload = () => {
                        canvas.width = img.width
                        canvas.height = img.height
                        context.drawImage(img, 0, 0)
                        this.imageCaptured = true
                    }
                    img.src = event.target.result
                }
                reader.readAsDataURL(file)
            },
            dataURItoBlob(dataURI) {
                // convert base64 to raw binary data held in a string
                // doesn't handle URLEncoded DataURIs - see SO answer #6850276 for code that does this
                var byteString = atob(dataURI.split(',')[1]);

                // separate out the mime component
                var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]

                // write the bytes of the string to an ArrayBuffer
                var ab = new ArrayBuffer(byteString.length);

                // create a view into the buffer
                var ia = new Uint8Array(ab);

                // set the bytes of the buffer to the correct values
                for (var i = 0; i < byteString.length; i++) {
                    ia[i] = byteString.charCodeAt(i);
                }

                // write the ArrayBuffer to a blob, and you're done
                var blob = new Blob([ab], {type: mimeString});
                return blob;

            },
            //methods for location
            getLocation() {
                this.locationLoading = true
                navigator.geolocation.getCurrentPosition(position =>{
                    this.getCityandCountry(position)
                }, err => {
                    console.log(err);
                },{ timeout: 7000 })
            },
            getCityandCountry(position) {
                let apiUrl = `https://geocode.xyz/${ position.coords.latitude },${ position.coords.longitude }?json=1`
                this.$axios.get(apiUrl).then(result => {
                    // console.log(result);
                    this.locationSucess(result)
                }).catch(err => {
                    console.log(err)
                })
            },
            locationSucess(result) {
                this.post.location = result.data.city
                if (result.data.country) {
                    this.post.location += `, ${ result.data.country }`
                }
                this.locationLoading = false
            },
            locationError() {
                this.$q.dialog({
                    title: 'Error',
                    message: 'Nao encontramos a sua localizacao'
                })
                this.locationLoading = false
            }
        },
        mounted() {
            this.initCamera()
        },
        beforeDestroy() {
            if (this.hasCameraSupport) {
                this.disableCamera()
            }
        }
    }
</script>

<style lang="sass">
    .camera-frame
        border: 2px solid $grey-10
        border-radius: 10px
</style>