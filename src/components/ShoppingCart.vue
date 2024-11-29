<template>
  <div class="container mt-4">
    <h1 class="text-center mb-4">Shopping Cart</h1>
    <form @submit.prevent="addProduct" class="row mb-3">
      <input v-model="newProduct.name" placeholder="Product" class="form-control col" required>
      <input v-model.number="newProduct.quantity" type="number" placeholder="Qty" class="form-control col" required>
      <div class="input-group col">
        <span class="input-group-text">$</span>
        <input v-model.number="newProduct.price" type="number" placeholder="Price (e.g. 10.99)" class="form-control" step="0.01" required>
      </div>
      <button class="btn btn-success col">Add Product</button>
    </form>
    <table class="table">
      <thead>
      <tr>
        <th>Product</th>
        <th>Quantity</th>
        <th>Price</th>
        <th>Action</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="item in cartItems" :key="item.name">
        <td>{{ item.name }}</td>
        <td>{{ item.quantity }}</td>
        <td>${{ item.price }}</td>
        <td>
          <button @click="removeProduct(item.name)" class="btn btn-danger btn-sm">Remove Product</button>
        </td>
      </tr>
      </tbody>
    </table>
    <h3 class="mt-4">Total: ${{ cartTotal.toFixed(2) }}</h3>
    <h3 class="mt-4">Calculated Tax: ${{ tax.toFixed(2) }}</h3>
    <h3 class="mt-4">Cart Total With Tax: ${{ cartTotalWithTax.toFixed(2) }}</h3>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: () => ({
    api: "http://localhost:8089/api/cart",
    newProduct: { name: "", quantity: 1, price: 0},
    cartItems: [],
    cartTotal: 0,
    tax: 0,
    cartTotalWithTax: 0,
  }),
  methods: {
    fetchCart() {
      axios.all([
        axios.get(`${this.api}/get-cart-items`).then(res => (this.cartItems = res.data)),
        axios.get(`${this.api}/calculate-cart-total`).then(res => (this.cartTotal = res.data)),
        axios.get(`${this.api}/calculate-tax`).then(res => (this.tax = res.data)),
        axios.get(`${this.api}/cart-total-with-tax`).then(res => (this.cartTotalWithTax = res.data)),
      ]);
    },
    addProduct(){
      axios.post(`${this.api}/add-product`, this.newProduct).then(this.fetchCart); // Värskenda ostukorvi
            this.newProduct = { name: "", quantity: 1, price: 0}; // Nulli vorm
    },
    removeProduct(name){
      axios.delete(`${this.api}/remove-product-by-name/${name}`).then(this.fetchCart);
    },
  },
  mounted() {
    this.fetchCart();
  },
};
</script>

<style>
/* General Styles */
body {
  font-family: "Montserrat", sans-serif;
  background-color: #f8f9fa;
  color: #333;
  margin: 0;
  padding: 0;
}

/* Container Styles */
.container {
  background: #ffffff;
  border-radius: 12px;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.1);
  padding: 30px;
  max-width: 900px;
  margin: 40px auto;
  border: 1px solid #e5e5e5;
}

/* Header Styles */
h1 {
  font-size: 2.4rem;
  font-weight: bold;
  color: #444444;
  text-transform: uppercase;
  text-align: center;
  margin-bottom: 30px;
}

/* Form Styles */
form {
  display: flex;
  flex-wrap: wrap;
  gap: 15px;
  justify-content: space-between;
}
form input,
form .input-group {
  flex: 1;
  min-width: 100px;
}
form input {
  border: 2px solid #cccccc;
  border-radius: 6px;
  padding: 10px;
  font-size: 1rem;
  background-color: #ffffff;
  color: #555555;
}
form input:focus {
  border-color: #6c63ff;
  outline: none;
  box-shadow: 0 0 5px rgba(108, 99, 255, 0.4);
}
form .input-group {
  display: flex;
  align-items: center;
  border: 2px solid #cccccc;
  border-radius: 6px;
  overflow: hidden;
}
form .input-group-text {
  background-color: #6c63ff;
  color: white;
  border: none;
  padding: 10px;
  font-size: 1rem;
}
form .input-group input {
  border: none;
  padding: 10px;
  font-size: 1rem;
  color: #555555;
}
form .input-group input:focus {
  outline: none;
  box-shadow: none;
}
form button {
  background-color: #6c63ff;
  color: white;
  border: none;
  border-radius: 6px;
  padding: 12px 20px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
form button:hover {
  background-color: #5149d3;
}

/* Table Styles */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 25px;
  border: 1px solid #e5e5e5;
}
table th,
table td {
  padding: 15px;
  text-align: left;
  border-bottom: 1px solid #e5e5e5;
}
table th {
  background-color: #6c63ff;
  color: white;
  font-weight: bold;
  text-transform: uppercase;
  border-bottom: none;
}
table tbody tr:hover {
  background-color: #f1f1f1;
}
table .btn-sm {
  padding: 6px 12px;
  border-radius: 6px;
  background-color: #ff4d4d;
  color: white;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
table .btn-sm:hover {
  background-color: #cc0000;
}

/* Summary Section */
h3 {
  font-size: 1.8rem;
  color: #444444;
  margin-top: 20px;
}
h3 span {
  color: #6c63ff;
  font-weight: bold;
}

/* Responsive Design */
@media (max-width: 768px) {
  form {
    flex-direction: column;
    gap: 10px;
  }
  form input,
  form .input-group,
  form button {
    width: 100%;
  }
  table th,
  table td {
    font-size: 0.9rem;
    padding: 10px;
  }
}
</style>










