<script>
  import { onMount, onDestroy } from 'svelte'
  import { Map, NavigationControl, Marker } from 'maplibre-gl';
  import 'maplibre-gl/dist/maplibre-gl.css';

  let map;
  let mapContainer;
  let popup;

  import Geolocation from "svelte-geolocation";

  let geolocation;
  let getPositionAgain = false;
  let detail = {};

  function moveToPosition(e, detail) {
    if (map) {
      const userLngLat = [detail.coords.longitude, detail.coords.latitude];
      console.log('move map', map, e, detail, userLngLat);
      map.flyTo({
        center: userLngLat,
        bearing: 0,
        zoom: 10
      });
      new Marker({color: "#00FF00"})
      .setLngLat(userLngLat)
      .addTo(map);
    }
  }

  onMount(() => { 


    // console.log('detail', detail);
    
    const apiKey = 'GlJWKS3DFD5ab4vrTpZJ';
    const resorts = {
      alta: ['-111.644791', '40.588509'],
      brighton: ['-111.5821', '40.6038'],
    };
    // const longLat = [detail.coords.longitude, detail.coords.latitude];
    const initialState = { lng: resorts.brighton[0], lat: resorts.brighton[1], zoom: 14 };

    // map.center = [detail.coords.longitude, detail.coords.latitude];

    map = new Map({
      container: mapContainer,
      style: `https://api.maptiler.com/maps/outdoor/style.json?key=${apiKey}`,
      center: [initialState.lng, initialState.lat],
      zoom: initialState.zoom,
      pitch: 61,
      maxPitch: 85,
      maxZoom: 14
    });

    for (const [key, value] of Object.entries(resorts)) {
      new Marker({color: "#FF00DD"})
      .setLngLat(value)
      .addTo(map);
    }


    map.on('load', function() {
      map.addSource("terrain", {
        "type": "raster-dem",
        "url": `https://api.maptiler.com/tiles/terrain-rgb/tiles.json?key=${apiKey}`
      });
      map.setTerrain({
        source: "terrain"
      });
    });


    map.addControl(new NavigationControl(), 'top-right');

  

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
    
    console.log('geolocation', geolocation.getGeolocationPosition({ enableHighAccuracy: true }));
    console.log('detail', detail);


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
  <button on:click="{moveToPosition}">
    Go to Position
  </button>
  <pre>{JSON.stringify(detail, null, 2)}</pre>
</div>

<Geolocation
  getPosition="{getPositionAgain}"
  watch="{true}"
  bind:this="{geolocation}"
  on:position="{(e) => {
    detail = e.detail;
    moveToPosition(e, e.detail);
  }}"
/>
<div class="map-wrap">
  <a href="https://www.maptiler.com" class="watermark"><img
    src="https://api.maptiler.com/resources/logo.svg" alt="MapTiler logo"/></a>
  <div class="map" id="map" bind:this={mapContainer}></div>
</div>