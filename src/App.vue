<template>
<div class="app-container">
    <MapComponent class="google-map" :markers="markers" :latestLocation="latestLocation" />
    <SearchComponent class="search-bar" @searchedResult="handleResult" />
    <TimezoneComponent class="time-card" :latestLocation="latestLocation" />
    <HistoryPageComponent class="history-page" :addresses="addresses" @indexes-to-delete="handleDelete" />
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

        const handleResult = (searchedResult) => {
            console.log('Received result:', searchedResult);
            latestLocation.value = searchedResult.coordinates

            const isNewLocation = !markers.value.some(item => item[1].lat === searchedResult.coordinates.lat && item[1].lng === searchedResult.coordinates.lng);
            if (isNewLocation) {
                addresses.value.push([id, searchedResult.address]);
                markers.value.push([id, searchedResult.coordinates]);
                id++;
            }
        }

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
    z-index: 1;
}

.google-map {
    z-index: 0;
}

.history-page {
    position: absolute;
    top: 40%;
    left: 10px;
    transform: translateY(-50%);
    z-index: 1;
}

.time-card {
    position: absolute;
    top: 100px;
    left: 10px;
    z-index: 1;
}
</style>
