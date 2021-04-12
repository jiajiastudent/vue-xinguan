<template>
    <div class="map">
        <el-input v-model="addressKeyword" placeholder="请输入地址来直接查找相关位置"></el-input>
        <!-- 给地图加点击事件getLocationPoint，点击地图获取位置相关的信息，经纬度啥的 -->
        <!-- scroll-wheel-zoom：是否可以用鼠标滚轮控制地图缩放，zoom是视图比例 -->
        <baidu-map class="bmView" :scroll-wheel-zoom="true" :center="center" :zoom="zoom" @click="getLocationPoint" ak="FV4ZcOesQ6tpzr8i0R0ePq0Q">
            <bm-view style="width: 100%; height:600px; flex: 1"></bm-view>
            <bm-local-search :keyword="addressKeyword" :auto-viewport="true" style="display: none"></bm-local-search>
        </baidu-map>
    </div>
</template>
<script>
    export default {
        data(){
            return{
                ponit: {
                    lng: 116.404,
                    lat: 39.915
                },
                zoom: 12.8,
                addressKeyword: "",
                center:"北京",
            }
        },
        created(){
        },
        methods: {
            getLocationPoint(e) {
                this.ponit.lng = e.point.lng;
                this.ponit.lat = e.point.lat;
                alert(this.ponit.lng+"     "+this.ponit.lat)
            },
        }
    }
</script>
<style>
    .map {
        width: 100%;
        height: 400px;
    }

</style>