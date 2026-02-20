<template>
  <div class="app-container">
    <h1>🎨 我的原創貼圖庫</h1>
    
    <div class="sticker-grid">
      <div 
        v-for="sticker in stickers" 
        :key="sticker.id" 
        class="sticker-card"
        @click="copySticker(sticker.url, sticker.id)"
      >
        <div class="image-wrapper">
          <img :src="sticker.url" :alt="sticker.name" />
        </div>
        <p>{{ activeId === sticker.id ? '✅ 已複製！' : '點擊複製' }}</p>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref } from 'vue';

// 1. 定義貼圖資料（未來可以從 API 獲取）
const stickers = ref([
  { id: 1, name: '開心小貓', url: '/images/sticker1.png' },
  { id: 2, name: '驚訝兔子', url: '/images/sticker2.png' },
  // 繼續增加...
]);

const activeId = ref(null);

// 2. 核心複製函數
const copySticker = async (url, id) => {
  try {
    const response = await fetch(url);
    const blob = await response.blob();
    
    // 檢查瀏覽器是否支援
    if (!navigator.clipboard || !window.ClipboardItem) {
      throw new Error('瀏覽器不支援 Clipboard API');
    }

    const item = new ClipboardItem({ 'image/png': blob });
    await navigator.clipboard.write([item]);

    // 成功後的視覺回饋
    activeId.value = id;
    setTimeout(() => { activeId.value = null; }, 2000);
    
  } catch (err) {
    console.error('複製失敗:', err);
    alert('複製失敗，請長按圖片手動複製。');
  }
};
</script>
<style scoped>
.sticker-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
  gap: 15px;
  padding: 20px;
}

.sticker-card {
  border: 1px solid #ddd;
  border-radius: 12px;
  padding: 10px;
  cursor: pointer;
  transition: transform 0.2s;
  background: white;
}

.sticker-card:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

.image-wrapper {
  /* 棋盤格背景顯示去背效果 */
  background-image: conic-gradient(#eee 0.25turn, #fff 0 0.5turn, #eee 0 0.75turn, #fff 0);
  background-size: 16px 16px;
  border-radius: 8px;
}

img { width: 100%; height: auto; display: block; }
</style>