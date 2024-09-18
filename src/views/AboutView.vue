<template>
  <div id="app">
    <h1>증권 거래내역 입력</h1>

    <div>
      <label>종목 선택:</label>
      <select v-model="selectedStock">
        <option disabled value="">종목을 선택하세요</option>
        <option v-for="stock in stocks" :key="stock" :value="stock">
          {{ stock }}
        </option>
      </select>
    </div>

    <div>
      <label>수량:</label>
      <input type="number" v-model="quantity" placeholder="수량 입력" />
    </div>

    <div>
      <label>가격:</label>
      <input type="number" v-model="price" placeholder="가격 입력" />
    </div>

    <div>
      <label>거래 날짜:</label>
      <input type="date" v-model="date" />
    </div>

    <button @click="saveTransaction">거래내역 저장</button>
    <button @click="clearForm">초기화</button>

    <h2>거래내역</h2>
    <table>
      <thead>
        <tr>
          <th>종목</th>
          <th>수량</th>
          <th>가격</th>
          <th>날짜</th>
          <th>수정</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(transaction, index) in transactions" :key="index">
          <td>{{ transaction.stock }}</td>
          <td>{{ transaction.quantity }}</td>
          <td>{{ transaction.price }}</td>
          <td>{{ transaction.date }}</td>
          <td><button @click="editTransaction(index)">수정</button></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import Cookies from "js-cookie";

export default {
  data() {
    return {
      selectedStock: "",
      quantity: 0,
      price: 0,
      date: "",
      transactions: [],
      stocks: ["A 종목", "B 종목"],
      editIndex: null, // 수정할 항목의 인덱스
    };
  },
  mounted() {
    this.loadTransactions(); // 쿠키에서 거래내역 불러오기
  },
  methods: {
    saveTransaction() {
      if (!this.selectedStock || this.quantity <= 0 || this.price <= 0 || !this.date) {
        alert("모든 필드를 올바르게 입력하세요.");
        return;
      }

      const newTransaction = {
        stock: this.selectedStock,
        quantity: this.quantity,
        price: this.price,
        date: this.date,
      };

      if (this.editIndex !== null) {
        // 수정 모드일 경우
        this.transactions.splice(this.editIndex, 1, newTransaction);
        this.editIndex = null;
      } else {
        // 새 항목 추가
        this.transactions.push(newTransaction);
      }

      this.saveToCookies();
      this.clearForm();
    },
    editTransaction(index) {
      const transaction = this.transactions[index];
      this.selectedStock = transaction.stock;
      this.quantity = transaction.quantity;
      this.price = transaction.price;
      this.date = transaction.date;
      this.editIndex = index; // 수정할 항목의 인덱스 설정
    },
    clearForm() {
      this.selectedStock = "";
      this.quantity = 0;
      this.price = 0;
      this.date = "";
      this.editIndex = null;
    },
    saveToCookies() {
      // JSON 형식으로 쿠키에 저장
      Cookies.set("transactions", JSON.stringify(this.transactions));
    },
    loadTransactions() {
      const cookieData = Cookies.get("transactions");
      if (cookieData) {
        this.transactions = JSON.parse(cookieData);
      }
    },
  },
};
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}
th, td {
  border: 1px solid #ddd;
  padding: 8px;
}
th {
  background-color: #f2f2f2;
}
</style>
