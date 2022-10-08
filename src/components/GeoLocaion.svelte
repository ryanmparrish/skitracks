<script>
  import Geolocation from "svelte-geolocation";

  let getPositionAgain = false;
  let detail = {};

  new maplibregl.Marker()
.setLngLat([-65.017, -16.457])
.addTo(map);

</script>

<style>
  .loc-ui {
    position: relative;
    text-align: right;
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
  }}"
/>

