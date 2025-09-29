<template>
  <div class="p-6 font-sans">
    <h1 class="text-2xl font-bold mb-4">🔔 useWebNotification デモ</h1>
<p>Web Notification API を使って、ブラウザ通知を表示します。</p>
<p class="mb-4">動作確認には、ブラウザの通知許可を「許可」にしてください。</p>
<p class="mb-4">通知がポップアップしない場合、OSやブラウザの設定で通知がブロックされている可能性があります。</p>
    <!-- 状態メッセージ -->

    <div class="mb-4 text-sm">
      <p v-if="!supported" class="text-red-600 font-semibold">
        このブラウザは Notification API をサポートしていません。
      </p>
      <p v-else-if="!granted" class="text-orange-600">
        通知を出すには許可が必要です。「通知許可を確認・要求」を押してください。
      </p>
      <p v-else class="text-green-700">通知の準備OKです。</p>
    </div>

    <div class="flex flex-wrap gap-2 mb-6">
      <button
        @click="handleNotify"
        :disabled="!supported || !granted"
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 disabled:opacity-50"
      >
        通知を送信(handleNotify method)
      </button>

      <button
        @click="close"
        :disabled="!supported || !granted"
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 disabled:opacity-50"
      >
        通知を閉じる(close method)
      </button>
       </div>

    <!-- 画面側プレビュー -->
    <div v-if="currentNotification && currentNotification.title !== 'デフォルト'"
         class="p-4 border rounded bg-gray-50 mt-4">
      <h2 class="text-lg font-semibold mb-2">📤 通知内容（画面表示）</h2>
      <p><strong>タイトル:</strong> {{ currentNotification.title }}</p>
      <p><strong>本文:</strong> {{ currentNotification.body }}</p>
    </div>

    <!-- 状態一覧 -->
    <div class="grid grid-cols-1 gap-4 mt-6">
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
        <a class="text-sm text-blue-600 mt-1 underline" :href="item.mdn" target="_blank" rel="noreferrer">
          MDNドキュメントはこちら
        </a>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from "vue";
import { useWebNotification } from "@vueuse/core";

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

// Permission/UI 状態をリアクティブに使いやすく
const supported = computed(() => isSupported.value);
const granted = computed(() => permissionGranted.value);
const hasNotification = computed(() => !!notification.value);

// 初回は権限が未許可なら促す（自動でダイアログ出すのが嫌ならコメントアウト）
onMounted(async () => {
  if (supported.value && Notification.permission === "default") {
    await ensurePermissions();
  }
});

async function handleNotify() {
  // ここで毎回タイトル/本文を指定すると画面プレビューもしやすい
  const n = await show({
    title: "通知",
    body: "これは VueUse の通知です。",
    icon: "https://vueuse.org/favicon.svg",
  });
  if (n) {
    currentNotification.value = n as Notification;
    showCalled.value = true;
  }
}

onClick(() => {
  window.alert("通知がクリックされました！");
});

onClose(() => {
  window.alert("通知が閉じられました！");
});

onError(() => {
  window.alert("通知の表示に失敗しました。");
});

onShow(() => {
  window.alert("通知が表示されました。");
});

// 表示用の状態テーブル（Refをそのまま入れず、booleanに評価してから）
const notificationInfo = [
  {
    key: "isSupported",
    label: "isSupported（通知API対応状況）",
    value: supported.value,
    source: "typeof window !== 'undefined' && 'Notification' in window",
    description: "ブラウザが Notification API に対応しているかどうかを判定します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification",
  },
  {
    key: "permissionGranted",
    label: "permissionGranted（通知許可状態）",
    value: granted.value,
    source: "Notification.permission === 'granted'",
    description: "ユーザーが通知の表示を許可しているかどうかを示します。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/permission",
  },
  {
    key: "clicked",
    label: "onClick（通知クリック）",
    value: "NA",
    source: "notification.onclick にイベントリスナーを登録",
    description: "通知がクリックされたときに発火するイベントです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/onclick",
  },
  {
    key: "closed",
    label: "onClose（通知閉じる）",
    value: "NA",
    source: "notification.onclose にイベントリスナーを登録",
    description: "通知が閉じられたときに発火するイベントです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/onclose",
  },
  {
    key: "errored",
    label: "onError（通知エラー）",
    value: "NA",
    source: "notification.onerror にイベントリスナーを登録",
    description: "通知の表示に失敗したときに発火するイベントです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/onerror",
  },
  {
    key: "shown",
    label: "onShow（通知表示完了）",
    value: "NA",
    source: "notification.onshow にイベントリスナーを登録",
    description: "通知が表示されたときに発火するイベントです。",
    mdn: "https://developer.mozilla.org/en-US/docs/Web/API/Notification/onshow",
  },
]

function highlight(ok: boolean) {
  return ok ? "text-green-600 font-bold" : "text-gray-700";
}
</script>

<style>
body { font-family: sans-serif; }
</style>
