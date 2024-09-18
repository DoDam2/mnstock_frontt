<template>
  <div>
    <h1>한국 주식 가격 조회 (네이버 금융)</h1>
    <input v-model="stockSymbol" placeholder="종목 코드를 입력하세요" />
    <button @click="fetchStockPrice">조회</button>
    <p v-if="stockPrice !== null">{{ stockSymbol }} 현재 가격: {{ stockPrice }}원</p>
    <p v-if="errorMessage">{{ errorMessage }}</p>
  </div>
</template>

<script>
import axios from "axios";
import cheerio from "cheerio"; // 웹 페이지 파싱을 위한 cheerio

export default {
  data() {
    return {
      stockSymbol: "", // 사용자가 입력할 주식 코드
      stockPrice: null,
      errorMessage: "",
    };
  },
  methods: {
    async fetchStockPrice() {
      if (!this.stockSymbol) {
        this.errorMessage = "종목 코드를 입력하세요.";
        return;
      }

      try {
        // 네이버 금융 URL
        const url = `https://finance.naver.com/item/main.nhn?code=${this.stockSymbol}`;
        
        // 네이버 금융 페이지에서 HTML 가져오기
        const response = await axios.get(url);
        const html = response.data;

        // cheerio를 사용해 HTML 파싱
        const $ = cheerio.load(html);

        // 주식 현재가 가져오기 (네이버 금융의 주식 가격 CSS 셀렉터 사용)
        const stockPrice = $("p.no_today .blind").first().text();

        if (stockPrice) {
          this.stockPrice = stockPrice.replace(",", ""); // 쉼표 제거
          this.errorMessage = "";
        } else {
          this.errorMessage = "해당 종목의 데이터를 찾을 수 없습니다.";
        }
      } catch (error) {
        this.errorMessage = "주식 데이터를 가져오는 데 실패했습니다.";
        console.error(error);
      }
    },
  },
};
</script>

<style>
input {
  padding: 10px;
  margin-right: 10px;
}
button {
  padding: 10px;
}
p {
  margin-top: 20px;
}
</style>
