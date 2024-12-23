<script lang="ts">
  import H from "@here/maps-api-for-javascript";
  import "@here/maps-api-for-javascript/bin/mapsjs-ui.css";
  import { onMount } from "svelte";

  const API_KEY = import.meta.env.API_KEY;

  let maspRef: HTMLElement;
  let map: H.Map | null = null;
  //let ui = $state();

  onMount(() => {
    const platform = new H.service.Platform({
      apikey: import.meta.env.VITE_API_KEY,
    });
    const defaultLayers = platform.createDefaultLayers();
    // ベクタータイルのソースを設定
    const omvService = platform.getOMVService({
      path: "v2/vectortiles/core/mc",
    });
    const baseUrl = "https://js.api.here.com/v3/3.1/styles/omv/oslo/japan/";

    // 日本向けの地図スタイルを読み込む
    const style = new H.map.Style(`${baseUrl}normal.day.yaml`, baseUrl);

    // 背景地図のプロバイダーとレイヤーを作成
    const omvProvider = new H.service.omv.Provider(omvService, style);
    const omvlayer = new H.map.layer.TileLayer(omvProvider, {
      max: 22,
      dark: true,
    });

    // マップを作成（中心は東京駅）
    map = new H.Map(maspRef, omvlayer, {
      zoom: 16,
      center: { lat: 35.680959106959, lng: 139.76730676352 },
    });

    // ユーザーの操作を有効にする
    new H.mapevents.Behavior(new H.mapevents.MapEvents(map));

    // デフォルトのUIコントローラーを作成
    H.ui.UI.createDefault(map, defaultLayers);

    // ウインドウサイズが変更されたときに地図をリサイズ
    window.addEventListener("resize", () => map?.getViewPort().resize());
  });
</script>

<div>
  <div class="map" bind:this={maspRef}></div>
</div>

<style>
  .map {
    width: 100vw;
    height: 100vh;
  }
</style>
