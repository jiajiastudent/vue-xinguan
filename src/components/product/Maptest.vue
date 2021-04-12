<template>
    <baidu-map class="bmView" :scroll-wheel-zoom="true" :center="center" :zoom="zoom"  @ready="handler" @click="getLocationPoint"
               ak="FV4ZcOesQ6tpzr8i0R0ePq0Q" >
        <bm-local-search :keyword="addressKeyword" :auto-viewport="true" style="display: none"></bm-local-search>
        <bm-view style="width: 100%; height:600px; flex: 1" ></bm-view>
        <bm-driving :start="start" :end="start"
                    :waypoints="waypoints"></bm-driving>
    </baidu-map>

</template>
<script>
    export default {
        data () {
            return {
                waypoints:[ {lng: 113.33, lat: 37.84},{lng: 112.53, lat: 23.87},{lng: 121.53, lat: 30.87} ],
                start: {lng: 113.53, lat: 37.87},
                end: {lng: 110.53, lat: 37.87},
                center: {lng: 0, lat: 0},
                zoom: 3,
                ponit: {
                    lng: 116.404,
                    lat: 39.915
                },
                addressKeyword: "",
            }
        },
        methods: {
            getLocationPoint(e) {
                this.ponit.lng = e.point.lng;
                this.ponit.lat = e.point.lat;
                alert(this.ponit.lng+"     "+this.ponit.lat)
            },
            handler ({BMap, map}) {
                console.log("hhhh")
                console.log(BMap, map)
                this.center.lng = 116.404
                this.center.lat = 39.915
                this.zoom = 15
            }
        }
    }
</script>