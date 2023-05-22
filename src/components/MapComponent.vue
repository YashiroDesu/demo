<template>
<div class="map">
    <!--Google map component with an input field and a button-->
    <GoogleMap ref="mapRef" api-key="AIzaSyAXkem4RNrrY4D2d80W5GPpVtowFYsk4e8" style="width: 100%; height: 100vh" :center="center" :zoom="7" :minZoom="+3">
        <!-- Component to cluster multiple markers on a single marker -->
        <MarkerCluster>
            <!-- Marker component for placing the markers on the map -->
            <Marker v-for="marker in markers" :options="{ position: marker[1] }" :key="marker[0]" />
        </MarkerCluster>
        <!-- Custom control component to add a button in the map-->
        <CustomControl position="TOP_LEFT">
            <button class="custom-btn" @click="getUsersCurrentLocation">📍</button>
        </CustomControl>

    </GoogleMap>
</div>
</template>

  
<script>
import {
    defineComponent,
    ref
} from 'vue'
import {
    GoogleMap,
    Marker,
    CustomControl
} from 'vue3-google-map'

export default defineComponent({
    name: "googleMap",
    components: {
        GoogleMap,
        Marker,
        CustomControl
    },
    props: {
        markers: {
            type: Array,
            default: () => []
        },
        latestLocation: {
            type: Object,
            required: true
        }
    },
    setup() {
        const mapRef = ref(null) //Reference to the google map object
        const center = {
            lat: 43.651070,
            lng: -79.347015
        } // center location of the map
        const infoWindow = ref(new window.google.maps.InfoWindow()) // reference to the infowindow object

        const getUsersCurrentLocation = () => {
            //Function to get the user's current location and update it on the map
            if (navigator.geolocation) {
                return new Promise((resolve, reject) => {
                    try {
                        navigator.geolocation.getCurrentPosition(
                            (position) => {
                                const userLocation = {
                                    lat: position.coords.latitude,
                                    lng: position.coords.longitude
                                }
                                infoWindow.value.setPosition(userLocation)
                                infoWindow.value.setContent('Your location.')
                                infoWindow.value.open(mapRef.value.map)
                                mapRef.value.map.panTo(userLocation)
                                resolve()
                            },
                            (error) => {
                                reject(error)
                            }
                        )
                    } catch (error) {
                        console.error('Error getting user location', error);
                        reject(error);
                    }
                })
            } else {
                console.error('Geolocation is not supported by this browser.')
            }
        }

        const panToLocation = (newLocation) => {
            //Function to panto a new location
            mapRef.value.map.panTo(newLocation)
        }

        return {
            center,
            getUsersCurrentLocation,
            mapRef,
            panToLocation
        }
    },
    watch: {
        latestLocation(newLocation) {
            //Watching for changes in the latest location prop and updating the map accordingly
            this.panToLocation(newLocation);
        }
    }
})
</script>

  
<style>
.custom-btn {
    box-sizing: border-box;
    background: white;
    height: 40px;
    width: 40px;
    border-radius: 2px;
    border: 0px;
    margin: 10px;
    padding: 0px;
    font-size: 1.25rem;
    text-transform: none;
    appearance: none;
    cursor: pointer;
    user-select: none;
    box-shadow: rgba(0, 0, 0, 0.3) 0px 1px 4px -1px;
    overflow: hidden;
}
</style>
