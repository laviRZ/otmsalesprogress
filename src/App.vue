<script setup lang="ts">
import Sale from './components/Sale.vue';
import PublicGoogleSheetsParser from 'public-google-sheets-parser';
import { reactive } from 'vue';
const sales: SaleData[] = reactive([]);

const sheetId = '1WGatv0ZhLyU9Dq7TKwLZS0rU6SZCbnmKQnQIX1AEpGo';
const parser = new PublicGoogleSheetsParser(sheetId, { useFormat: true } as any);
parser.parse().then((data) => {
  const keys = {
    'name': 'מכירה',
    'target': 'כמה',
    'currency': 'תמורת',
    'progress': 'התקדמות',
    'seller': 'מוכר',
    'buyer': 'קונה',
    'end': 'תאריך סיום'
  }
  data = data.map((row) => ({
    name: row[keys.name],
    target: row[keys.target],
    currency: row[keys.currency],
    progress: row[keys.progress],
    seller: row[keys.seller],
    buyer: row[keys.buyer],
    end: new Date([row[keys.end].split('/')[1], row[keys.end].split('/')[0], row[keys.end].split('/')[2]].join('/'))
  })
  );
  data.forEach((sale) => {
    sales.push(sale);
  });
});


</script>

<template>
  <img src="./assets/otm.png" class="logo" alt="Or Torah Machanaim Logo" />
  <div id="sales">
    <Sale v-if="sales.length > 0" v-for="sale in sales" :data="sale" :key="sale.name + sale.buyer" />
    <h3 v-else>טוען, תנשמו...</h3>
  </div>
</template>



<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}

#sales {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1em;
}
</style>
