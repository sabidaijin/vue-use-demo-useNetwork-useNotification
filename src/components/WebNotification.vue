<template>
  <div class="p-6 font-sans">
    <h1 class="text-2xl font-bold mb-4">🔔 useWebNotification デモ</h1>
<div v-if="currentNotification?.title !== 'デフォルト'" class="p-4 border rounded bg-gray-50 mt-4">
  <h2 class="text-lg font-semibold mb-2">📤 通知内容（画面表示）</h2>
  <p><strong>タイトル:</strong> {{ currentNotification?.title }}</p>
  <p><strong>本文:</strong> {{ currentNotification?.body }}</p>
</div>

    <button
      @click="handleNotify"
      :disabled="!permissionGranted"
      class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 disabled:opacity-50 mb-4"
    >
      通知を送信
    </button>

    <button
      @click="close"
      :disabled="!notification"
      class="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600 disabled:opacity-50 mb-4 ml-2"
    >
      通知を閉じる
    </button>

    <button
      @click="ensurePermissions"
      class="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600 mb-4 ml-2"
    >
      通知許可を確認・要求
    </button>

    <div class="grid grid-cols-1 gap-4">
      <div
        v-for="item in notificationInfo"
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
          説明: {{ item.description }}
        </p>
<a class="text-sm text-gray-600 mt-1" :href="item.mdn" target="_blank">MDNドキュメントはこちら</a>
</div>
  </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { useWebNotification } from "@vueuse/core";

const showCalled = ref(false);
const clicked = ref(false);
const closed = ref(false);
const errored = ref(false);
const shown = ref(false);
const currentNotification = ref<Notification | null>(null);
const {
  isSupported,
  notification,
  ensurePermissions,
  permissionGranted,
  show,
  close,
  onClick,
  onShow,
  onError,
  onClose,
} = useWebNotification({
  title: "デフォルト",
  body: "これは VueUse の通知です。",
  icon: "https://vueuse.org/favicon.svg",
});

async function handleNotify  () {
  currentNotification.value=await show({
  title: "通知",
  body: "これは VueUse の通知です。",
  icon: "https://vueuse.org/favicon.svg",
}) as Notification;
  showCalled.value = true;
}

onClick(() => {
  clicked.value = true;
});

onClose(() => {
  closed.value = true;
});

onError(() => {
  errored.value = true;
});

onShow(() => {
  shown.value = true;
});

const notificationInfo = [
  {
    key: "isSupported",
    label: "isSupported（通知API対応状況）",
    value: isSupported,
    source: "typeof window !== 'undefined' && 'Notification' in window",
    description: "ブラウザが Notification API に対応しているかどうかを判定します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification",
  },
  {
    key: "permissionGranted",
    label: "permissionGranted（通知許可状態）",
    value: permissionGranted,
    source: "Notification.permission === 'granted'",
    description: "ユーザーが通知の表示を許可しているかどうかを示します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/permission",
  },
  {
    key: "notification",
    label: "notification（通知インスタンス）",
    value: !!notification.value,
    source: "new Notification(...) によって生成されたインスタンスを保持",
    description: "現在表示されている Notification オブジェクトです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification",
  },
  {
    key: "showCalled",
    label: "show（通知表示）",
    value: showCalled.value,
    source: "内部で new Notification(options.title, options) を呼び出す",
    description: "通知を表示する関数。クリックで呼び出されます。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/Notification",
  },
  {
    key: "clicked",
    label: "onClick（通知クリック）",
    value: clicked.value,
    source: "notification.onclick にイベントリスナーを登録",
    description: "通知がクリックされたときに発火するイベントです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/onclick",
  },
  {
    key: "closed",
    label: "onClose（通知閉じる）",
    value: closed.value,
    source: "notification.onclose にイベントリスナーを登録",
    description: "通知が閉じられたときに発火するイベントです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/onclose",
  },
  {
    key: "errored",
    label: "onError（通知エラー）",
    value: errored.value,
    source: "notification.onerror にイベントリスナーを登録",
    description: "通知の表示に失敗したときに発火するイベントです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/onerror",
  },
  {
    key: "shown",
    label: "onShow（通知表示完了）",
    value: shown.value,
    source: "notification.onshow にイベントリスナーを登録",
    description: "通知が表示されたときに発火するイベントです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/onshow",
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
