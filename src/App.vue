<template>
  <div>
    <header class="header">
      <h1>Cosméticos Fortnite</h1>
      <p>Hoy es {{ currentDate }}</p>
    </header>

    <div class="cosmetics-container">
      <div v-if="cosmesticosFortnite.length === 0">Cargando...</div>
      <div class="cosmetic-card" v-for="cosmetico in cosmesticosFortnite" :key="cosmetico.devName">
        <img :src="cosmetico.images.icon" :alt="cosmetico.name" class="cosmetic-image">
        <div class="cosmetic-details">
          <h2 class="cosmetic-title">{{ cosmetico.name }}</h2>
          <p><strong>Rareza</strong> {{ cosmetico.rarity.value }}</p>
          <p><strong>Giftable:</strong> {{ cosmetico.giftable ? 'Sí' : 'Si' }}</p>
          <p><strong>Valor:</strong> {{ cosmetico.price.finalPrice }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";

let cosmesticosFortnite = ref([]);

async function cargarCosmeticosFortnite() {
  try {
    const response = await axios.get('https://fortnite-api.com/v2/shop/br');
    const dailyEntries = response.data?.data?.featured?.entries;

    if (dailyEntries && Array.isArray(dailyEntries)) {
      cosmesticosFortnite.value = dailyEntries
        .map(entry => {
          const item = entry.items[0];
          return {
            name: item.name,
            rarity: item.rarity,
            giftable: item.giftable, // Utilizamos directamente el valor booleano
            price: {
              finalPrice: entry.finalPrice
            },
            images: {
              icon: item.images.icon
            },
            devName: item.devName
          };
        });
    } else {
      console.error('La estructura de la respuesta no es la esperada.');
    }
  } catch (error) {
    console.error(error);
  }
}
const currentDate = new Date().toLocaleDateString('es-ES', {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric'
});
onMounted(async () => {
  cargarCosmeticosFortnite(); 
});
</script>

<style scoped>
.header {
  background-color: #81ebfd;
  color: #fff;
  text-align: center;
  padding: 10px;
  border-radius: 10px;
}

.cosmetics-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: flex-start;
  margin-top: 20px;
}

.cosmetic-card {
  width: 200px;
  border: 1px solid #ccc;
  padding: 10px;
  margin: 10px;
  text-align: center;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.cosmetic-image {
  max-width: 100%;
  border-radius: 5px;
  margin-bottom: 10px;
}

.cosmetic-details h2 {
  font-size: 18px;
  margin-bottom: 5px;
}

.cosmetic-details p {
  font-size: 14px;
  margin: 5px 0;
}
</style>
