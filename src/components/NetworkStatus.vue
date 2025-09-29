<template>
  <div class="p-6 font-sans">
    <h1 class="text-2xl font-bold mb-4">📡 useNetwork デモ & ドキュメント</h1>
    <div class="grid grid-cols-1 gap-4">
      <div
        v-for="item in networkInfo"
        :key="item.key"
        class="p-4 border rounded shadow"
      >
        <h2 class="text-lg font-semibold">{{ item.label }}</h2>
        <p :class="highlight(item.value)">
          <strong>現在の値:</strong> {{ item.value }}
        </p>
        <p class="text-sm text-gray-600 mt-1">
          <strong>取得元:</strong> {{ item.source }}
        </p>
        <p class="text-sm text-gray-600 mt-1">
説明:          {{ item.description }}
</p>
<a class="text-sm text-gray-600 mt-1" :href="item.mdn" target="_blank">MDNドキュメントはこちら</a>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { useNetwork } from "@vueuse/core";

const {
  isSupported,
  isOnline,
  downlink,
  effectiveType,
  saveData,
  type,
  offlineAt,
  onlineAt,
  downlinkMax,
  rtt,
} = useNetwork();

const networkInfo = [
  {
    key: "isSupported",
    label: "isSupported（API対応状況）",
    value: isSupported,
    source: "useSupported(() => navigator && 'connection' in navigator)",
    description:
      "ブラウザが navigator.connection API に対応しているかどうかを判定します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Navigator/connection",
  },
  {
    key: "isOnline",
    label: "isOnline（オンライン状態）",
    value: isOnline,
    source: "navigator.onLine",
    description: "現在ブラウザがオンラインかどうかを示します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine",
  },
  {
    key: "saveData",
    label: "saveData（省データモード）",
    value: saveData,
    source: "navigator.connection.saveData",
    description: "ユーザーが省データモードを有効にしているかどうかを示します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation/saveData",
  },
  {
    key: "offlineAt",
    label: "offlineAt（オフラインになった時刻）",
    value: offlineAt?.toLocaleString(),
    source: "window 'offline' イベント",
    description: "ブラウザがオフラインになったタイミングのタイムスタンプです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event",
  },
  {
    key: "onlineAt",
    label: "onlineAt（オンラインになった時刻）",
    value: onlineAt?.toLocaleString(),
    source: "window 'online' イベント",
    description: "ブラウザがオンラインになったタイミングのタイムスタンプです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event",
  },
  {
    key: "downlink",
    label: "downlink（通信速度）",
    value: downlink,
    source: "navigator.connection.downlink",
    description: "現在の通信速度（Mbps）を示します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation/downlink",
  },
  {
    key: "downlinkMax",
    label: "downlinkMax（最大通信速度）",
    value: downlinkMax,
    source: "navigator.connection.downlinkMax",
    description: "接続がサポートする最大通信速度（Mbps）です。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation/downlinkMax",
  },
  {
    key: "effectiveType",
    label: "effectiveType（通信の種類）",
    value: effectiveType,
    source: "navigator.connection.effectiveType",
    description: "通信の種類（例: '4g', '3g'）を示します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation/effectiveType",
  },
  {
    key: "rtt",
    label: "rtt（往復時間）",
    value: rtt,
    source: "navigator.connection.rtt",
    description: "現在の接続の往復時間（ms）です。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation/rtt",
  },
  {
    key: "type",
    label: "type（接続タイプ）",
    value: type,
    source: "navigator.connection.type",
    description: "接続の種類（例: 'wifi', 'cellular'）を示します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation",
  },
];

function highlight(value: any) {
  return value ? "text-green-600 font-bold" : "text-gray-700";
}
</script>

<style>
body {
  font-family: sans-serif;
}
</style>
