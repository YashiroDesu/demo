<template>
<div class="card" style="width: 300px;">
    <ul class="list-group list-group-flush">
        <!--Displaying local time here-->
        <li class="list-group-item">Time: {{ currentTime }}</li>
        <!--Displaying time zone here-->
        <li class="list-group-item">Time Zone: {{ timeZone }}</li>
    </ul>
</div>
</template>

<script>
import axios from 'axios';

export default {
    // Name of the component
    name: 'TimezoneInfo',
    props: {
        // Latest location object as a required property
        latestLocation: {
            type: Object,
            required: true
        }
    },

    data() {
        return {
            // Initial values for currentTime and timeZone are empty strings
            currentTime: '',
            timeZone: ''
        };
    },

    watch: {
        // Updating timezone information on change in location
        latestLocation(newLocation) {
            this.getTimezoneInfo(newLocation.lat, newLocation.lng);
        }
    },

    methods: {
        getTimezoneInfo(latitude, longitude) {
            // Google maps API key for timezone service
            const apiKey = 'AIzaSyAXkem4RNrrY4D2d80W5GPpVtowFYsk4e8'
            const url = `https://maps.googleapis.com/maps/api/timezone/json?location=${latitude}, ${longitude}&timestamp=${Date.now() / 1000}&key=${apiKey}`;

            // Fetching timezone information from Google API
            axios
                .get(url)
                .then(response => {
                    const {
                        data
                    } = response;
                    // Extracting dstOffset, rawOffset and timezoneid from response data
                    const {
                        dstOffset,
                        rawOffset,
                        timeZoneId
                    } = data;

                    // Calculating current time after adding dstOffset and rawOffset
                    const currentTime = new Date(
                        Date.now() + (dstOffset + rawOffset) * 1000
                    );

                    // Updating currentTime and timeZone values
                    this.currentTime = currentTime.toLocaleString()
                    this.timeZone = timeZoneId;
                })
                .catch(error => {
                    console.error(error);
                })
        }
    }
};
</script>
