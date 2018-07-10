<template>
  <div>
    <div class="h-map" id="container">
      <div id="h-panel" v-if="showPanel"></div>
    </div>
  </div>
</template>
<script>
import AMap from 'AMap';
export default {
  name: 'HMap',
  props: {
    center: {
      type: Array,
      default: null
    },
    position: {
      type: String,
      default: ''
    },
    zoom: {
      type: Number,
      default: 15
    },
    moreMarker: {
      type: Boolean,
      default: false
    },
    startPoint: {
      type: String,
      default: ''
    },
    endPoint: {
      type: String,
      default: ''
    },
    showPanel: {
      type: Boolean,
      default: false
    },
  },
  data () {
    return {
      geocoder: '',
      map: '',
      marker: ''
    }
  },
  methods: {
    initMap() {
      const _this = this;
      // 初始化地图
      _this.map = new AMap.Map('container', {
        center: _this.center, // 中心点
        resizeEnable: true,
        zoom: _this.zoom,// 地图初始化缩放比例
        enableHighAccuracy:true, // 是否使用高精度
      });

      // 添加地图配置项
      AMap.plugin(['AMap.ToolBar', 'AMap.Scale','AMap.Geocoder', 'AMap.Walking'], () => {
        _this.map.addControl(new AMap.ToolBar());
        _this.map.addControl(new AMap.Scale());
        _this.geocoder = new AMap.Geocoder({
          radius:1000 //以已知坐标为中心点，radius为半径，返回范围内兴趣点和道路信息
        });
        _this.map.addControl(new AMap.Walking());
      });
      // 点击地图动态创建标点
      _this.map.on('click', function(e) {
        // 如果存在标点，则移除之前标点
        if (_this.marker && !_this.moreMarker) _this.map.remove(_this.marker);
        // 创建标记点
        const x = e.lnglat.getLng();
        const y = e.lnglat.getLat();
        _this.marker = new AMap.Marker({
          map: _this.map,
          position: new AMap.LngLat(x, y)
        });
        _this.map.add(_this.marker);
        const lnglatXY = [x, y];
        _this.map.panTo(lnglatXY); // 移动到标点为中心点
        _this.geocoder.getAddress(lnglatXY, (status, result) => {
          if (status === 'complete' && result.info === 'OK') {
            _this.getData(result);
            _this.getLngLat(x, y);
          }
        });
      });
    },
    getData(data) {
      this.$emit('cityChange', data);
    },
    getLngLat(lng, lat) {
      this.$emit('getLngLat', {lng, lat});
    },
    // 搜索时设置标点
    setPosition() {
      const _this = this;
      // 如果存在标点，则移除之前标点
      if (_this.marker) _this.map.remove(_this.marker);
      _this.geocoder.getLocation(_this.position, (status, result) => {
        if (status === 'complete' && result.info === 'OK') {
          const x = result.geocodes[0].location.lng;
          const y = result.geocodes[0].location.lat;
          const lnglatXY = [x, y];
          _this.map.panTo(lnglatXY); // 移动到标点为中心点
          _this.marker = new AMap.Marker({
            map: _this.map,
            position: new AMap.LngLat(x, y)
          });
          _this.map.add(_this.marker);
        }
      });
    },
    searchWalking() {
      const _this = this;
      let startX = '';
      let startY = '';
      let endX = '';
      let endY = '';
      const walking = new AMap.Walking(); //构造路线导航类
      _this.showPanel = false;
      _this.map.clearMap();
      _this.geocoder.getLocation(_this.startPoint, (status, result) => {
        if (status === 'complete' && result.info === 'OK') {
          startX = result.geocodes[0].location.lng;
          startY = result.geocodes[0].location.lat;
          _this.geocoder.getLocation(_this.endPoint, (status, result) => {
            if (status === 'complete' && result.info === 'OK') {
              endX = result.geocodes[0].location.lng;
              endY = result.geocodes[0].location.lat;
              _this.showPanel = true;
              walking.search(new AMap.LngLat(startX, startY), new AMap.LngLat(endX, endY), function(status, result){
                if(status === 'complete'){
                  _this.map.clearMap();
                  (new Lib.AMap.WalkingRender()).autoRender({
                    data: result,
                    map: _this.map,
                    panel: "h-panel"
                  });
                }
              });
            }
          });
        }
      });
    }
  },
  mounted() {
    this.initMap();
  },
  watch: {
    position: function (newVal, oldVal) {
      this.setPosition();
    },
    startPoint: function (newVal, oldVal) {
      this.searchWalking();
    },
    endPoint: function (newVal, oldVal) {
      this.searchWalking();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.h-map{
  height: 300px;
  position: relative;
}
#h-panel {
  z-index: 999;
  position: absolute;
  background-color: white;
  max-height: 100%;
  overflow-y: auto;
  right: 0;
  width: 280px;
}
</style>
