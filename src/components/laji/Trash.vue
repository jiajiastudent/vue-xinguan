<template>
    <!--地图容器-->
    <div id="allmap" class="allmap"></div>

</template>
<script>
    import BMap from 'BMap'
    export default {
        data () {

            return {
                trashcans:[],
                Point:[],
                marker:[]
            }
        },
        mounted() {
            var url="/ms/bucketmanage/get-all-bucket";
            this.$axios.get(url).then(response =>{
                if ((response.statusText = "OK")) {
                    this.total_count = response.data.retTotalCount;
                    this.tableData = [];
                    var array = response.data.retValue;
                   //console.log(array);
                    for(var i=0;i<array.length;i++){
                        var trashcans={};
                        trashcans.BucketID=array[i].BucketID;
                        trashcans.Position=array[i].Position;
                        trashcans.Status=array[i].Status;
                        trashcans.company_ref=array[i].company_ref;
                        trashcans.flag_recycle=array[i].flag_recycle;
                        trashcans.maxWeight=array[i].maxWeight;
                        trashcans.weightArray=array[i].weightArray;
                        this.trashcans.push(trashcans);
                    }
                }
                //console.log(this.trashcans[0].Position.lng);
                //创建Map实例
                var map = new BMap.Map("allmap");
                // 初始化地图,设置中心点坐标
                //var point = new BMap.Point(121.160724,31.173277);// 创建点坐标
                var point = new BMap.Point(120.24504602377,30.833402274285);
                map.centerAndZoom(point,11);
                //添加鼠标滚动缩放
                map.enableScrollWheelZoom();

                //添加缩略图控件
                map.addControl(new BMap.OverviewMapControl({isOpen:false,anchor:BMAP_ANCHOR_BOTTOM_RIGHT}));
                //添加缩放平移控件
                map.addControl(new BMap.NavigationControl());
                //添加比例尺控件
                map.addControl(new BMap.ScaleControl());
                //添加地图类型控件
                //map.addControl(new BMap.MapTypeControl());

                //设置标注的图标
                var icon=[];
                //console.log(typeof parseInt(this.trashcans[1].Status));
                for(var i=0;i<this.trashcans.length;i++){
                    //console.log(this.trashcans[i]);
                    if(this.trashcans[i].Status>=0&&this.trashcans[i].Status<30){
                        icon[i]= new BMap.Icon("static/img/garbage_green.png", new BMap.Size(30,34));
                    }
                    else if(this.trashcans[i].Status>=30&&this.trashcans[i].Status<80){
                        icon[i]= new BMap.Icon("static/img/garbage_gray.png", new BMap.Size(30,34));
                    }
                    else
                        icon[i]= new BMap.Icon("static/img/garbage_red.png", new BMap.Size(30,34));
                }
               // var icon = new BMap.Icon("static/img/garbage_green.png", new BMap.Size(30,34));
                //设置标注的经纬度
                for(var i=0;i<this.trashcans.length;i++){
                    var marker=[];
                    marker[i] = new BMap.Marker(new BMap.Point(this.trashcans[i].Position.lng,this.trashcans[i].Position.lat),{icon:icon[i]});
                    map.addOverlay(marker[i]);
                      var content="回收桶编号："+this.trashcans[i].BucketID+"<br>"+
                                    "所属企业："+this.trashcans[i].company_ref.CompanyName+"<br>"
                                    +"质量："+this.trashcans[i].Status;

                    // var content = "<table class='table2'>";
                    // content = content + "<tr><td> 回收桶编码: "+this.trashcans[i].BucketID+"</td></tr>";
                    // content = content + "<tr><td> 所属企业: "+this.trashcans[i].company_ref.CompanyName+"</td></tr>";
                    // content = content + "<tr><td> 状态: "+this.trashcans[i].Status+"</td></tr>";
                    // content += "</table>";
                    this.addClickHandler(content,marker[i]);
                }
                // for (var i=0;i<this.marker.length;i++){
                //     this.Point[i]=new BMap.Point(this.trashcans[i].Position.lng,this.trashcans[i].Position.lat);
                //     this.marker[i]=new BMap.Marker(this.Point[i],{icon:icon});
                //     map.addOverlay(this.marker[i]);
                //     var content="回收桶编号："+this.trashcans[i].BucketID+"<br>"+"所属企业："+this.trashcans[i].company_ref.CompanyName+"<br>"+"状态："+this.trashcans[i].Status;
                //     //把标注添加到地图上
                //     var infowindow = new BMap.InfoWindow(content);
                //     this.marker.addEventListener("click",function(){
                //         this.openInfoWindow(infowindow);
                //     });
                // }


            });

        },
        methods:{
            getTrashcanlists(){
                var url="/ms/bucketmanage/get-all-bucket";
                this.$axios.get(url).then(response =>{
                    if ((response.statusText = "OK")) {
                        this.total_count = response.data.retTotalCount;
                        this.tableData = [];
                        var array = response.data.retValue;
                        for(var i=0;i<array.length;i++){
                            var trashcans={};
                            trashcans.BucketID=array[i].BucketID;
                            trashcans.Position=array[i].Position;
                            trashcans.Status=array[i].Status;
                            trashcans.company_ref=array[i].company_ref;
                            trashcans.flag_recycle=array[i].flag_recycle;
                            trashcans.maxWeight=array[i].maxWeight;
                            trashcans.weightArray=array[i].weightArray;
                            this.trashcans.push(trashcans);
                        }
                    }
                });
            },
            addClickHandler:function(content,market){
                var infowindow = new BMap.InfoWindow(content);
                market.addEventListener("click",function(){
                    this.openInfoWindow(infowindow);
                });
            }
        },
    }
</script>
<style scoped>
    html,body,.XSDFXPage{
        width: 50%;
        height: 50%;
        overflow: hidden;
        margin: 0px auto;
    }
    .handle-box {
        margin-bottom: 15px;
        width: 100%;
    }
    .select{
        width: 12%!important;
    }
    #allmap{
        width: 100%;
        height: 90vh;
        font-size: 18px;
        line-height: 36px;
    }
    .table2{
        width:120%;
        font-size:11px;
    }
    .table1{
        width:100%;
        font-size: 13px;
    }
    .anchorBL {
        display: none;
    }
</style>
<style>
    .anchorBL {
        display: none;
    }
</style>

