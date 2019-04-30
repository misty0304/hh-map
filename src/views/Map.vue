<template>
  <div>
    <input type="text" ref="input1" style="width: 300px;"/>
    <div @click="search">搜索</div>
    起始：<input type="text" ref="input2" style="width: 300px;"/>
    终点：<input type="text" ref="input3" style="width: 300px;"/>
    <div @click="searchLoad">搜索路线</div>
    <h-map
      class="map"
      :zoom="15"
      :positionLngLat="[116.406315,39.908775]"
      :position="position"
      :startPoint="startPoint"
      :endPoint="endPoint"
      :showPanel="false"
      :getLngAndLat="getLngAndLat"
      @cityChange="getData"
      @getLngLat="getLngLat"
    ></h-map>
  </div>
</template>

<script>
  import HMap from '../components/HMap';
  export default {
    name: 'Map',
    data () {
      return {
        position: '',
        startPoint: '',
        endPoint: ''
      }
    },
    components: {
      HMap
    },
    methods: {
      getLngAndLat(x, y) {
        console.log(1, x, y);
      },
      getData(data) {
        console.log(data);
//        this.position = data.regeocode.formattedAddress;
      },
      getLngLat(lng, lat) {
        console.log(lng, lat);
      },
      search() {
        this.position = this.$refs.input1.value;
      },
      searchLoad() {
        this.startPoint = this.$refs.input2.value;
        this.endPoint = this.$refs.input3.value;
      }
    },
    mounted() {
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
  .map{
    width: 500px;
  }
</style>
