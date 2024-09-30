<template>
  <div class="home-page">
    <h1 class="page-title">Point of Sales</h1>

    <nav class="navbar">
      <router-link to="/" class="nav-button">Home</router-link>
      <router-link to="/report" class="nav-button">Report</router-link>
    </nav>

    <div class="form-section">
      <label for="product">Select Product:</label>
      <select v-model="selectedProduct" @change="resetQty" class="select-product">
        <option v-for="product in products" :key="product.id" :value="product">
          {{ product.product_name }} - {{ product.product_price | currency }}
        </option>
      </select>

      <label for="qty">Qty:</label>
      <input type="number" v-model="qty" min="1" class="input-qty" />

      <p class="total-price">Total Harga: {{ totalHarga | currency }}</p>

      <button @click="addToTable" class="btn btn-add">Add</button>
    </div>

    <table class="transaction-table">
      <thead>
        <tr>
          <th>Product Name</th>
          <th>Price</th>
          <th>Qty</th>
          <th>Total</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in tableData" :key="index">
          <td>{{ item.product_name }}</td>
          <td>{{ item.product_price | currency }}</td>
          <td>{{ item.qty }}</td>
          <td>{{ item.total | currency }}</td>
          <td><button @click="deleteItem(index)" class="btn btn-delete">Delete</button></td>
        </tr>
      </tbody>
    </table>

    <p class="subtotal">Subtotal: {{ subtotal | currency }}</p>

    <div class="button-group">
      <button @click="saveTransaction" class="btn btn-save">Save</button>
      <button @click="clearTable" class="btn btn-clear">Clear</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [
        { id: 1, product_name: 'Product A', product_price: 10000 },
        { id: 2, product_name: 'Product B', product_price: 15000 },
        { id: 3, product_name: 'Product C', product_price: 20000 }
      ],
      selectedProduct: null,
      qty: 1,
      tableData: []
    };
  },
  computed: {
    totalHarga() {
      return this.selectedProduct ? this.selectedProduct.product_price * this.qty : 0;
    },
    subtotal() {
      return this.tableData.reduce((sum, item) => sum + item.total, 0);
    }
  },
  methods: {
    resetQty() {
      this.qty = 1;
    },
    addToTable() {
      if (this.selectedProduct && this.qty > 0) {
        const item = {
          ...this.selectedProduct,
          qty: this.qty,
          total: this.selectedProduct.product_price * this.qty
        };
        this.tableData.push(item);
        this.resetQty();
      }
    },
    deleteItem(index) {
      this.tableData.splice(index, 1);
    },
    saveTransaction() {
      const transaction = {
        id: Date.now(),
        report_datetime: this.formatDate(new Date()), // Memanggil fungsi formatDate untuk mendapatkan format yang diinginkan
        report_subtotal: this.subtotal
      };
      let transactions = JSON.parse(localStorage.getItem('transactions')) || [];
      transactions.push(transaction);
      localStorage.setItem('transactions', JSON.stringify(transactions));
      this.clearTable();
    },
    formatDate(date) {
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(2, '0'); // Menambahkan 0 di depan untuk bulan
      const day = String(date.getDate()).padStart(2, '0'); // Menambahkan 0 di depan untuk hari
      const hours = String(date.getHours()).padStart(2, '0'); // Menambahkan 0 di depan untuk jam
      const minutes = String(date.getMinutes()).padStart(2, '0'); // Menambahkan 0 di depan untuk menit
      const seconds = String(date.getSeconds()).padStart(2, '0'); // Menambahkan 0 di depan untuk detik

      return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`; // Format yang diinginkan
    },
    clearTable() {
      this.tableData = [];
    }
  },
  filters: {
    currency(value) {
      return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR' }).format(value);
    }
  }
};
</script>

<style src="../assets/home.css"></style>