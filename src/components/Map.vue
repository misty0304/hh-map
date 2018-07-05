<template>
  <div>
    <input type="text" ref="input1" style="width: 300px;"/>
    <div @click="search">搜索</div>
    <h-map class="map" :center="[116.397428, 39.90923]" :position="position" @cityChange="getData" @getLngLat="getLngLat"></h-map>
  </div>
</template>

<script>
  import HMap from 'hh-map';
  export default {
    name: 'Map',
    data () {
      return {
        position: ''
      }
    },
    components: {
      HMap
    },
    methods: {
      getData(data) {
        console.log(data);
//        this.position = data.regeocode.formattedAddress;
      },
      getLngLat(lng, lat) {
        console.log(lng, lat);
      },
      search() {
        this.position = this.$refs.input1.value;
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
