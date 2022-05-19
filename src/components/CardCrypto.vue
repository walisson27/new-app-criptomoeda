<template>
  <div class="card">
    <b-skeleton-wrapper :loading="loading">
      <template #loading>
  
          <b-skeleton type="button" width="20%"></b-skeleton>
          <b-skeleton type="button" class="my-2 my-md-4" width="75%"></b-skeleton>
          <b-skeleton type="button" width="50%"></b-skeleton>

      </template>

      <div class="entrada">
          <div class="rank">
          <span>Rank #{{ rank }}</span>
        </div>
        <div class="crypto">
          <img :src="image" alt="" />
          <span>{{ coinName }} ({{ symbol }})</span>
        </div>
        <div class="price">
          <span class="value">{{
            currentPrice 
          }}</span>
          <span class="echange" :class="echangePorcentage > 0 ? 'up' : 'down'"
            >{{ echangePorcentage | porcentage }}%</span
          >
        </div>
        </div>
      
    </b-skeleton-wrapper>
        
  </div>
</template>

<script>
export default {
  name: "CardCrypto",
  props: [
    "loading",
    "rank",
    "image",
    "coinName",
    "symbol",
    "currency",
    "currentPrice",
    "echangePorcentage",
  ],
  filters: {
    porcentage(e) {
      if (e) {
        return e.toFixed(2);
      }
    }
  }
}
</script>

<style scoped>
.card {
  display: flex;
  flex-direction: column;
  background: linear-gradient(90deg, rgba(2,0,36,1) 38%, rgba(3,0,37,1) 39%, rgba(69,9,121,1) 100%, rgba(0,212,255,1) 100%);
  color: #fff;
  margin-top: 20px;
  margin-bottom: 20px;
  border-radius: 20px;
  padding: 40px;
  font-weight: bold;

  text-transform: uppercase;
}

.card img {
  width: 50px;
  margin-right: 10px;
  height: 50px;
}

.card .rank {
  margin-bottom: 10px;
  text-transform: none;
}

.card .rank span {
  background: #fff;
  color: #0d111b;
  font-size: 16px;
  padding: 4px;
  border-radius: 14px;
}

.card .crypto {
  font-size: 40px;
  margin-bottom: 20px;
  display: flex;
  align-items: center;
}

.card .price {
  font-size: 40px;
  display: flex;
  flex-direction: row;
  gap: 10px;
}

.card .echange {
  font-size: 22px;
}

.up {
  color: #64e700;
}

.down {
  color: #ff4a4a;
}

@media (max-width: 768px) {

  .card {
    padding: 20px;
  }

  .card .rank span {
    font-size: 12px;
  }

  .card .crypto {
   font-size: 20px;
  }

  .card img {
    width: 30px;
    height: 30px;
  }

  .card .price {
  font-size: 25px;
  }

  .card .echange {
  font-size: 16px;
}
}

</style>
