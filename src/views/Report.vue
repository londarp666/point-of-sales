<template>
  <div class="report-page">
    <h1 class="page-title">Transaction Report</h1>

    <nav class="navbar">
      <router-link to="/" class="nav-button">Home</router-link>
      <router-link to="/report" class="nav-button">Report</router-link>
    </nav>

    <table class="report-table">
      <thead>
        <tr>
          <th>Transaction ID</th>
          <th>Date</th>
          <th>Subtotal</th>
          <th>Action</th> <!-- Tambahkan kolom untuk tindakan -->
        </tr>
      </thead>
      <tbody>
        <tr v-for="transaction in transactions" :key="transaction.id">
          <td>{{ transaction.id }}</td>
          <td>{{ transaction.report_datetime }}</td>
          <td>{{ transaction.report_subtotal | currency }}</td>
          <td>
            <button @click="deleteTransaction(transaction.id)" class="btn btn-delete">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    <button @click="exportToExcel" class="btn btn-export">Export To Excel</button>
  </div>
</template>

<script>
import * as XLSX from 'xlsx';

export default {
  data() {
    return {
      transactions: [] // Menyimpan data transaksi yang diambil dari localStorage
    };
  },
  mounted() {
    // Mengambil data transaksi dari localStorage saat komponen di-mount
    this.transactions = JSON.parse(localStorage.getItem('transactions')) || [];
  },
  methods: {
    deleteTransaction(id) {
      // Menghapus transaksi dari localStorage
      const transactions = JSON.parse(localStorage.getItem('transactions')) || [];
      const updatedTransactions = transactions.filter(transaction => transaction.id !== id);
      localStorage.setItem('transactions', JSON.stringify(updatedTransactions));
      
      // Memperbarui daftar transaksi di tampilan
      this.transactions = updatedTransactions;
    },
    exportToExcel() {
      const table = document.querySelector('.report-table'); // Mengambil elemen tabel untuk diekspor
      const workbook = XLSX.utils.table_to_book(table); // Mengonversi tabel menjadi workbook Excel
      XLSX.writeFile(workbook, 'transaction_report.xlsx'); // Menyimpan file Excel
    }
  },
  filters: {
    currency(value) {
      return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR' }).format(value);
    }
  }
};
</script>

<style scoped>
.report-page {
  padding: 20px;
  font-family: Arial, sans-serif;
}

.page-title {
  text-align: center;
  margin-bottom: 20px;
}

.navbar {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.nav-button {
  background-color: #007bff;
  color: white;
  padding: 10px 15px;
  border: none;
  border-radius: 5px;
  margin: 0 10px;
  text-decoration: none;
}

.nav-button:hover {
  background-color: #0056b3;
}

.report-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.report-table th, .report-table td {
  border: 1px solid #ccc;
  padding: 10px;
  text-align: left;
}

.report-table th {
  background-color: #f8f9fa;
}

.btn {
  padding: 5px 10px; /* Perbaikan untuk ukuran tombol */
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.btn-delete {
  background-color: #dc3545;
}

.btn-export {
  background-color: #007bff;
  display: block;
  margin: 20px auto; /* Agar tombol export berada di tengah */
}
</style>
