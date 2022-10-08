<script>
  import { onMount, onDestroy } from 'svelte'
  import { Map, NavigationControl, Marker } from 'maplibre-gl';
  import 'maplibre-gl/dist/maplibre-gl.css';

  let map;
  let mapContainer;

  import Geolocation from "svelte-geolocation";

  let getPositionAgain = false;
  let detail = {};



  onMount(() => {
    
    const apiKey = 'GlJWKS3DFD5ab4vrTpZJ';
    const alta = ['-111.644791', '40.588509'];
    const initialState = { lng: alta[0], lat: alta[1], zoom: 15 };

    map = new Map({
      container: mapContainer,
      style: `https://api.maptiler.com/maps/hybrid/style.json?key=${apiKey}`,
      center: [initialState.lng, initialState.lat],
      zoom: initialState.zoom,
      pitch: 52,
      hash: true,
      // style: {
      //   key: apiKey,
      //   version: 8,
      //   sources: {
      //     osm: {
      //       type: 'raster',
      //       tiles: ['https://a.tile.openstreetmap.org/{z}/{x}/{y}.png'],
      //       tileSize: 256,
      //       attribution: '&copy; OpenStreetMap Contributors',
      //       maxzoom: 19
      //     } ,
      //     terrainSource: {
      //       type: 'raster-dem',
      //       url: 'https://demotiles.maplibre.org/terrain-tiles/tiles.json',
      //       tileSize: 256
      //     },
      //     hillshadeSource: {
      //       type: 'raster-dem',
      //       url: 'https://demotiles.maplibre.org/terrain-tiles/tiles.json',
      //       tileSize: 256
      //     }
      //   },
      //   layers: [
      //     {
      //       id: 'osm',
      //       type: 'raster',
      //       source: 'osm'
      //     },
      //     {
      //       id: 'hills',
      //       type: 'hillshade',
      //       source: 'hillshadeSource',
      //       layout: { visibility: 'visible' },
      //       paint: { 'hillshade-shadow-color': '#473B24' }
      //     }
      //   ],
      //   terrain: {
      //     source: 'terrainSource',
      //     exaggeration: 1
      //   } 
      // },
      maxZoom: 18,
      maxPitch: 85
      
    });

    map.addControl(new NavigationControl(), 'top-right');
    
    // map.addControl(
    //   new maplibregl.NavigationControl({
    //     visualizePitch: true,
    //     showZoom: true,
    //     showCompass: true
    //   })
    // );
    // map.addControl(
    //   new maplibregl.TerrainControl({
    //     source: 'terrainSource',
    //     exaggeration: 1
    //   })
    // );

    // new Marker({color: "#FF0000"})
    //   .setLngLat([40.7111,-41])
    //   .addTo(map);

    // map.on('load', function () {
      
    //   geoloc = new Geolocation({ 
    //   getPosition: getPositionAgain,
    //   watch: true,
    //   on:position( (e) => {
    //     detail = e.detail;
    //     const longLat = [detail.coords.longitude, detail.coords.latitude];
    //     console.log('longLat', longLat);
    //   })
  
    // }, 2000);

  });

  onDestroy(() => {
    map.remove();
  });
</script>

<style>

  .loc-ui {
    text-align: right;
    position: absolute;
    top: 14px;
    right: 60px;
    z-index: 1;
  }
  .loc-ui pre {
    background: white;
    padding: 8px;
    font-size: small;
    position: fixed;
    top: 50px;
    right: 64px;
    z-index: 2;
    text-align: left;
    box-shadow: 0px 0px 1px 2px rgba(0,0,0,0.15);
  }

  .map-wrap {
    position: relative;
    width: 100%;
    height: calc(100vh - 77px); /* calculate height of the screen minus the heading */
  }

  .map {
    position: absolute;
    width: 100%;
    height: 100%;
  }

  .watermark {
    position: absolute;
    left: 10px;
    bottom: 10px;
    z-index: 999;
  }
</style>


<div class="loc-ui">
  <button on:click="{() => (getPositionAgain = !getPositionAgain)}">
    Get Position
  </button>
  <pre>{JSON.stringify(detail, null, 2)}</pre>
</div>

<Geolocation
  getPosition="{getPositionAgain}"
  watch="{true}"
  on:position="{(e) => {
    detail = e.detail;
    const longLat = [detail.coords.longitude, detail.coords.latitude];
    console.log('longLat', longLat);

  }}"
/>
<div class="map-wrap">
  <a href="https://www.maptiler.com" class="watermark"><img
    src="https://api.maptiler.com/resources/logo.svg" alt="MapTiler logo"/></a>
  <div class="map" id="map" bind:this={mapContainer}></div>
</div>