<template>
  <div class="container posts">
    <div
      class="check-layers"
      v-for="layer in layers"
      :key="layer.id"
    >
      <label class="checkbox">
        <input type="checkbox"
          v-model="layer.active"
          @change="layerChanged(layer.id, layer.active)"
        >
        {{layer.name}}
      </label>
    </div>
    <div id="map" class="map"></div>
    <div v-if="loading" class="loading">
      Loading...
    </div>
    <Post v-for="post in posts" :key="post.id" :post="post"/>
  </div>
</template>

<script>
import Post from './components/Post.vue'

export default {
  name: 'App',
  data: function(){
    return {
      posts: [],
      loading: false,
      map: null,
      tileLayer: null,
      layers: [
        {
          id: 1,
          name: 'Рестораны',
          active: false,
          features: [
            {
              id: 0,
              name: 'Столовая №1',
              type: 'marker',
              coords: [59.932675, 30.362747],
            },
            {
              id: 1,
              name: 'Макдоналдс',
              type: 'marker',
              coords: [59.930589, 30.363414],
            },
            {
              id: 2,
              name: 'Кальянная',
              type: 'marker',
              coords: [59.930957, 30.359436],
            },
          ],
        },
      ],
    }
  },
  components: {
    Post
  },
  created() {
    this.fetchData();
  },
  mounted() {
    this.initMap();
    this.initLayers();
  },
  methods: {
    async fetchData(){
      this.loading = true;
      const response = await fetch("https://jsonplaceholder.typicode.com/posts");
      this.loading = false;
      this.posts = await response.json();
    },
    initMap() {
      var L = window.L;
      this.map = L.map('map').setView([59.931058, 30.360910], 17);
      this.tileLayer = L.tileLayer(
        'https://cartodb-basemaps-{s}.global.ssl.fastly.net/rastertiles/voyager/{z}/{x}/{y}.png',
        {
          maxZoom: 18,
          attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>, &copy; <a href="https://carto.com/attribution">CARTO</a>',
        }
      );
      this.tileLayer.addTo(this.map);
    },
    initLayers() {
        var L = window.L;
        this.layers.forEach((layer) => {
          const markerFeatures = layer.features.filter(feature => feature.type === 'marker');
          const polygonFeatures = layer.features.filter(feature => feature.type === 'polygon');
          markerFeatures.forEach((feature) => {
            feature.leafletObject = L.marker(feature.coords)
              .bindPopup(feature.name);
          });
          polygonFeatures.forEach((feature) => {
            feature.leafletObject = L.polygon(feature.coords)
              .bindPopup(feature.name);
          });
        });
    },
    layerChanged(layerId, active) {
      const layer = this.layers.find(layer => layer.id === layerId);
      layer.features.forEach((feature) => {
        if (active) {
          feature.leafletObject.addTo(this.map);
        } else {
          feature.leafletObject.removeFrom(this.map);
        }
      });
    },
  }
}
</script>

<style src="bulma/css/bulma.min.css">
</style>

<style scoped>
  .posts{
    padding-top: 20px;
  }
  .map {
    height: 400px;
    margin-bottom: 20px;
    margin-top: 20px;
  }
</style>
