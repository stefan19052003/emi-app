<template>
    <div class="relative">
        <div id="map" class="w-screen h-full mb-20 bg-primary"></div>
        <div class="absolute w-screen top-[35rem] z-10 bg-white p-5 text-xs rounded-t-2xl">
            <div v-if="!done" class="h-24 flex mb-6">
                <button v-if="!running" @click="start()" class="bg-primary rounded-lg py-2 px-4 text-white font-bold mr-6 w-1/2">
                    START
                </button>
                <button v-if="running" @click="stop()" class="bg-red-500 rounded-lg py-2 px-4 text-white font-bold mr-6 w-1/2">
                    STOP
                </button>
                <div class="w-full self-center text-left">
                    <p class="font-bold text-xl text-primary">
                        DESTINATION
                    </p>
                    <p class="text-gray-500">
                        {{this.currentRoute}}
                    </p>
                    <p class="text-md">
                        {{this.time}}
                    </p>
                </div>
            </div>
            <div class="mb-10" v-else>
                <p class="mb-4 font-bold text-primary">
                    SHIPMENT CLEARED
                </p>
                <p class="text-xs">
                    EXPECTED CARBON EMITTED 
                </p>
                <p class="text-md text-red-500">
                    + 1000 kgCO2eq
                </p>
            </div>
            <hr class="mb-6">
            <div class="ml-4">
                <ol class="border-l border-primary text-left text-primary">                  
                    <li v-for="route, index in this.cRoute" :key="route" :class="{'bg-yellow-100': index == this.currentRouteIndex, 'bg-green-100': index < this.currentRouteIndex }" class="p-8">            
                        <div class="flex">
                            <span class="mr-3 flex absolute left-[1.5rem] justify-center items-center w-6 h-6 bg-white rounded-full ring-2 ring-primary">
                                <svg class="w-3 h-3 text-primary" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" d="M6 2a1 1 0 00-1 1v1H4a2 2 0 00-2 2v10a2 2 0 002 2h12a2 2 0 002-2V6a2 2 0 00-2-2h-1V3a1 1 0 10-2 0v1H7V3a1 1 0 00-1-1zm0 5a1 1 0 000 2h8a1 1 0 100-2H6z" clip-rule="evenodd"></path></svg>
                            </span>
                            <h3 v-if="index == 0" class="mb-4 text-base font-semibold text-primary"> FROM {{route.shop_name.toUpperCase()}}</h3>
                            <h3 v-else class="mb-4 text-base font-semibold text-primary"> SHIPMENT TO {{route.shop_name.toUpperCase()}}</h3>
                        </div>
                        <p class="text-xs font-normal =">Get access to over 20+ pages including a dashboard layout, charts, kanban board, calendar, and pre-order E-commerce & Marketing pages.</p>
                    </li>
                </ol>
            </div>
            <p>Copyright @EMI</p>
        </div>
    </div>

</template>

<script>
    import mapboxgl from "mapbox-gl";
    import "mapbox-gl/dist/mapbox-gl.css";
    import * as turf from '@turf/turf'
    import axios from 'axios'

    export default {
        name: "JourneyView",
        data() {
            return {
            // routes: ['Warehouse', 'Shop_27', 'Shop_1', 'Shop_10', 'Shop_46', 'Shop_41'],
            accessToken: "pk.eyJ1Ijoic3RlZmFucmFmYWVsIiwiYSI6ImNsMWV2MG1vdTA2YXkzZG54dXBpZGJtd3IifQ.dFkQvD8fs51PMPrFOvaKKg",
            driverIndex: 3,
            coordinatesList: [],
            routeList:[],
            currentLocation: [],
            coordinates:[],
            trackingFlag: false,
            time: '00:00:00.000',
            started: null,
            running: false,
            timeBegan: null,
            timeStopped: null,
            stoppedDuration: 0,
            currentRoute: '',
            currentRouteIndex: 1,
            done: false,
            overallReport: [],
            waypoints: [],
            cRoute: '',
            };
        },
        methods: {
            async getRoutes(){
                const url = "http://localhost:8000"
                await axios.get(`${url}/emy/routes`)
                .then(response => this.waypoints = response.data.data[0])
                .catch(err => console.log(err))

                console.log(this.waypoints)
                return this.waypoints
            },
            start() {
                if(this.running) return;
  
                if (this.timeBegan === null) {
                    this.reset();
                    this.timeBegan = new Date();
                }

                if (this.timeStopped !== null) {
                    this.stoppedDuration += (new Date() - this.timeStopped);
                }
                
                this.started = setInterval( async () => {
                    this.clockRunning()
                }, 10)

                this.running = true;
                this.startTracking()
            },
            stop() {
                this.running = false;
                this.timeStopped = new Date();
                clearInterval(this.started);
                this.stopTracking()
                this.reset()
            },
            reset() {
                this.running = false;
                clearInterval(this.started);
                this.stoppedDuration = 0;
                this.timeBegan = null;
                this.timeStopped = null;
                this.time = "00:00:00.000";
            },
            clockRunning() {
              var currentTime = new Date()
            , timeElapsed = new Date(currentTime - this.timeBegan - this.stoppedDuration)
            , hour = timeElapsed.getUTCHours()
            , min = timeElapsed.getUTCMinutes()
            , sec = timeElapsed.getUTCSeconds()
            , ms = timeElapsed.getUTCMilliseconds();

            this.time = 
                this.zeroPrefix(hour, 2) + ":" + 
                this.zeroPrefix(min, 2) + ":" + 
                this.zeroPrefix(sec, 2) + "." + 
                this.zeroPrefix(ms, 3);  
            },

            zeroPrefix(num, digit) {
                var zero = '';
                for(var i = 0; i < digit; i++) {
                    zero += '0';
                }
                return (zero + num).slice(-digit);
            },
            calculateDistance(cList){
                var line = turf.lineString(cList);
                var length = turf.length(line, {units: 'kilometers'});
                return length
            },
            async currentPos(){
                return await this.$getLocation()
                .then((coordinates) => {
                    return [coordinates.lng, coordinates.lat]
                })
                .catch((error) => {
                    return error
                });
            },
            async startTracking() {
                console.log('start')
                this.tracking()
            },
            async stopTracking() {
                clearInterval(this.trackingInterval)
                this.currentLocation = await this.currentPos()
                
                console.log(this.currentLocation)
                this.coordinatesList.push(this.currentLocation)
                this.routeList.push(this.coordinatesList)

                var shipmentReport =  {
                    'orig_store': this.cRoute[this.currentRouteIndex-1],
                    'dest_store': this.cRoute[this.currentRouteIndex],
                    'dist_km': this.calculateDistance(this.coordinatesList)
                }

                this.overallReport.push(shipmentReport)
                
                this.coordinatesList = []
                this.currentRouteIndex++
            },
            async tracking() {
                this.trackingInterval = setInterval( async () => {
                    this.currentLocation = await this.currentPos()
                    this.coordinatesList.push(this.currentLocation)
                    console.log(this.coordinatesList)
                }, 1000)
            },
            getTrackingData() {
                console.log(this.routeList)
            }
        },
        async created() {

        },
        async mounted() {
            this.waypoints = await this.getRoutes()
            this.currentLocation = await this.currentPos()
            mapboxgl.accessToken = this.accessToken

            const map = new mapboxgl.Map({
                container: 'map', // container ID
                style: 'mapbox://styles/mapbox/streets-v11', // style URL
                center: this.currentLocation, // starting position [lng, lat]
                zoom: 10 // starting zoom
            });

            const geolocate = new mapboxgl.GeolocateControl({
            positionOptions: {
                enableHighAccuracy: true, timeout:1000
            },
            trackUserLocation: true,
            // Draw an arrow next to the location dot to indicate which direction the device is heading.
            showUserHeading: true
            });
            map.addControl(geolocate, "top-right")

            const nav = new mapboxgl.NavigationControl();
            map.addControl(nav, "top-right");
            
            var wp = ''
            this.waypoints.routes[this.driverIndex].map((r, idx) => {
                if(idx == this.waypoints.routes[this.driverIndex].length - 1){
                    wp += `${r.long},${r.lat}`
                } else {
                    wp += `${r.long},${r.lat};`
                }
                const marker = new mapboxgl.Marker({
                color: '#063E3F',
                scale: 0.85
                })
                .setLngLat([r.long,r.lat])
                .addTo(map);
            })
            var url = `https://api.mapbox.com/directions/v5/mapbox/driving/${wp}?alternatives=true&annotations=duration%2Cdistance&geometries=geojson&language=en&overview=full&steps=true&access_token=${mapboxgl.accessToken}`
            console.log(url)
            const query = await axios.get(url)
            
            
            const json = await query.data;
            const data = json.routes[0];
            const route = data.geometry.coordinates;

            const geojson = {
                type: 'Feature',
                properties: {},
                geometry: {
                type: 'LineString',
                coordinates: route
                }
            };

            if (map.getSource('route')) {
                map.getSource('route').setData(geojson);
            }
            // otherwise, we'll make a new request
            else {
                map.on('load', () => {
                    map.addLayer({
                        id: 'route',
                        type: 'line',
                        source: {
                            type: 'geojson',
                            data: geojson
                        },
                        layout: {
                            'line-join': 'round',
                            'line-cap': 'round'
                        },
                        paint: {
                            'line-color': '#063E3F',
                            'line-width': 5,
                            'line-opacity': 0.75
                        }
                        });
                })
                
            }
            
            this.cRoute = this.waypoints.routes[this.driverIndex]
            this.currentRoute = this.cRoute[this.currentRouteIndex].shop_name
            console.log(this.cRoute)
        },
        watch: {
            currentLocation(oval, nval) {
                if (nval[0] >= (nval[0]-0.001) && nval[0] <= (nval[0]+0.001)) {
                    if (nval[1] >= (nval[1]-0.001) && nval[1] <= (nval[1]+0.001)) {
                        console.log('stop')
                    } else {
                        console.log('skip')
                    }
                } else {
                    console.log('skip')
                }
            },
            currentRouteIndex(oval, nval) {
                if (this.currentRouteIndex > this.cRoute.length-1) {
                    this.done = true
                    this.currentRoute = ''
                } else {
                    this.currentRoute = this.cRoute[this.currentRouteIndex].shop_name
                }
            }
        },
    };
</script>

<style scoped>
#map{
    position: fixed;
    z-index: -99;
}
</style>
