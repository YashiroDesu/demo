<template>
<div class="app-container">
    <!-- MapComponent which receives 'markers' and 'latestLocation' values as props -->
    <MapComponent class="google-map" :markers="markers" :latestLocation="latestLocation" />
    <!-- SearchComponent with 'handleResult' function registered to @searchedResult event -->
    <SearchComponent class="search-bar" @searchedResult="handleResult" />
    <!-- TimezoneComponent which receives 'latest-location' value as prop -->
    <div>
    <TimezoneComponent class="time-card" :latestLocation="latestLocation" />
    <!-- HistoryPageComponent which receives 'addresses' value as prop and has @indexes-to-delete event registered to handleDelete function -->
    <HistoryPageComponent class="history-page" :addresses="addresses" @indexes-to-delete="handleDelete" />
    </div>
</div>
</template>

<script>
import MapComponent from './components/MapComponent.vue'
import SearchComponent from './components/SearchComponent.vue'
import TimezoneComponent from './components/TimezoneComponent.vue'
import HistoryPageComponent from './components/HistoryPageComponent.vue'


import {
    ref
} from 'vue'

export default {
    name: 'App',
    components: {
        MapComponent,
        SearchComponent,
        TimezoneComponent,
        HistoryPageComponent
    },

    setup() {
        const markers = ref([])
        const addresses = ref([])
        const latestLocation = ref()
        let id = 0

        //
        const handleResult = (searchedResult) => {
            console.log('Received result:', searchedResult);
            latestLocation.value = searchedResult.coordinates

            // Checking if the searched location is new location
            const isNewLocation = !markers.value.some(item => item[1].lat === searchedResult.coordinates.lat && item[1].lng === searchedResult.coordinates.lng);
            // Storing coordinates and adress for new location.
            if (isNewLocation) {
                addresses.value.push([id, searchedResult.address]);
                markers.value.push([id, searchedResult.coordinates]);
                id++;
            }
        }

        // Filter the 'markers.value' array to remove items whose index is present in 'indexesToDelete'
        const handleDelete = (indexesToDelete) => {
            console.log('Deleting:', indexesToDelete)
            markers.value = markers.value.filter((item) => !indexesToDelete.includes(item[0]))
            addresses.value = addresses.value.filter((item) => !indexesToDelete.includes(item[0]))
        }

        return {
            markers,
            addresses,
            latestLocation,
            handleResult,
            handleDelete
        }
    }
}
</script>

<style>
.app-container {
    position: relative;
}

.search-bar {
    position: absolute;
    top: 10px;
    left: 50%;
    transform: translateX(-50%);
}

.history-page {
    position: absolute;
    left: 10px;
    top: 235px;
}

.time-card {
    position: absolute;
    top: 100px;
    left: 10px;
}
</style>
