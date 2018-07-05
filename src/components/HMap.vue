<template>
  <div class="hello" id="container">
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
    }
  },
  data () {
    return {
      geocoder: '',
      map: '',
      marker: '',
      addr: this.position
    }
  },
  methods: {
    initMap() {
      const _this = this;
      // 初始化地图
      _this.map = new AMap.Map('container', {
        center: _this.center, // 中心点
        resizeEnable: true,
        zoom: 15,// 地图初始化缩放比例
        enableHighAccuracy:true, // 是否使用高精度
      });

      // 添加地图配置项
      AMap.plugin(['AMap.ToolBar', 'AMap.Scale','AMap.Geocoder'], () => {
        _this.map.addControl(new AMap.ToolBar());
        _this.map.addControl(new AMap.Scale());
        _this.geocoder = new AMap.Geocoder({
          radius:1000 //以已知坐标为中心点，radius为半径，返回范围内兴趣点和道路信息
        });
      });
      _this.setPosition();
      // 点击地图动态创建标点
      _this.map.on('click', function(e) {
        // 如果存在标点，则移除之前标点
        if (_this.marker) _this.map.remove(_this.marker);
        // 创建标记点
        _this.marker = new AMap.Marker({
          map: _this.map,
          position: new AMap.LngLat(e.lnglat.getLng(), e.lnglat.getLat())
        });
        _this.map.add(_this.marker);
        const lnglatXY = [e.lnglat.getLng(), e.lnglat.getLat()];
        _this.map.panTo(lnglatXY); // 移动到标点为中心点
        _this.geocoder.getAddress(lnglatXY, (status, result) => {
          if (status === 'complete' && result.info === 'OK') {
            _this.getData(result);
            _this.getLngLat(e.lnglat.getLng(), e.lnglat.getLat());
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
    setPosition() {
      const _this = this;
      // 如果存在标点，则移除之前标点
      if (_this.marker) _this.map.remove(_this.marker);
      _this.geocoder.getLocation(_this.position, (status, result) => {
        if (status === 'complete' && result.info === 'OK') {
          const lnglatXY = [result.geocodes[0].location.lng, result.geocodes[0].location.lat];
          _this.map.panTo(lnglatXY); // 移动到标点为中心点
          _this.marker = new AMap.Marker({
            map: _this.map,
            position: new AMap.LngLat(result.geocodes[0].location.lng, result.geocodes[0].location.lat)
          });
          _this.map.add(_this.marker);
        }
      });
    }
  },
  mounted() {
    this.initMap();
  },
  watch: {
    position: function (val) {
      this.setPosition()
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
.hello{
  height: 300px;
}
</style>
