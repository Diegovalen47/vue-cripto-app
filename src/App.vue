
<script lang="ts">
import { defineComponent } from "vue";
import { Cripto } from "./types";

export default defineComponent({
  name: "App",
  data() {
    return {
      coins: [] as Cripto[],
      filteredCoins: [] as Cripto[],
      titles: ["#", "Coin", "Price", "Price Change", "24h Volume"] as string[],
      textSearch: '',
    };
  },
  async mounted() {
    const res = await fetch(
      "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false"
    );
    const data = await res.json();
    this.coins = data;
    this.filteredCoins = data;
  },
  methods: {
    searchCoin() {
      this.filteredCoins = this.coins.filter(
        (coin) => {
          return (
            coin.name.toLowerCase().includes(this.textSearch.toLowerCase()) ||
            coin.symbol.toLowerCase().includes(this.textSearch.toLowerCase())
          )
        }
      );
    },
  },
})
</script>

<template>
  <div class="container">
    <div class="row">
      <h1 class="mt-4">Coin Market</h1>

      <input
        type="text"
        class="form-control bg-dark text-light rounded-0 border-0 my-4"
        placeholder="Search Coin"
        v-model="textSearch"
        @keyup="searchCoin()"
        autofocus
      />

      <table class="table table-hover table-dark text-light">
        <thead>
          <tr>
            <th v-for="(title, index) in titles" :key="index">
              {{ title }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(coin, index) in filteredCoins" :key="coin.id">
            <td class="text-muted">
              {{ index + 1 }}
            </td>
            <td>
              <img
                :src="coin.image"
                :alt="coin.name"
                style="width: 2rem"
                class="me-2"
              />
              <span>
                {{ coin.name }}
              </span>
              <span class="ms-2 text-uppercase text-muted">
                {{ coin.symbol }}
              </span>
            </td>
            <td>$ {{ coin.current_price }}</td>
            <td
              :class="[
                coin.price_change_percentage_24h > 0
                  ? 'text-success'
                  : 'text-danger',
              ]"
            >
              {{ coin.price_change_percentage_24h }} %
            </td>
            <td>
              {{ coin.total_volume.toLocaleString() }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style>
</style>
