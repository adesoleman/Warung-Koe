# Warung-Koe
// Struktur file:
// - index.html
// - style.css
// - script.js

// index.html
const html = `
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Toko Online Sederhana</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Toko Online Sederhana</h1>

  <section id="login-section">
    <h2>Login Admin</h2>
    <form onsubmit="login(event)">
      <input type="text" id="admin-user" placeholder="Username" required>
      <input type="password" id="admin-pass" placeholder="Password" required>
      <button type="submit">Login</button>
    </form>
  </section>

  <section id="catalog">
    <h2>Katalog Produk</h2>
    <div id="product-list"></div>
  </section>

  <section id="cart">
    <h2>Keranjang Belanja</h2>
    <ul id="cart-items"></ul>
    <p>Total: <span id="total">Rp0</span></p>
    <button onclick="checkout()">Checkout</button>
  </section>

  <section id="admin-dashboard" style="display:none">
    <h2>Dashboard Admin</h2>
    <form onsubmit="addProduct(event)">
      <input type="text" id="name" placeholder="Nama Produk" required>
      <input type="number" id="price" placeholder="Harga" required>
      <button type="submit">Tambah Produk</button>
    </form>
  </section>

  <section id="transaction-history" style="display:none">
    <h2>Riwayat Transaksi</h2>
    <ul id="history-list"></ul>
  </section>

  <script src="script.js"></script>
</body>
</html>
`;

// style.css
const css = `
body {
  font-family: Arial, sans-serif;
  padding: 20px;
}
section {
  margin-bottom: 30px;
}
#product-list > div {
  border: 1px solid #ccc;
  padding: 10px;
  margin-bottom: 10px;
}
button {
  margin-top: 5px;
}
`;

// script.js
const js = `
let products = [];
let cart = [];
let transactions = [];
const admin = { username: 'admin', password: '1234' };

function renderProducts() {
  const list = document.getElementById('product-list');
  list.innerHTML = '';
  products.forEach((product, index) => {
    const item = document.createElement('div');
    item.innerHTML = `
      <p><strong>${product.name}</strong> - Rp${product.price}</p>
      <button onclick="addToCart(${index})">Tambah ke Keranjang</button>
    `;
    list.appendChild(item);
  });
}

function addProduct(e) {
  e.preventDefault();
  const name = document.getElementById('name').value;
  const price = parseInt(document.getElementById('price').value);
  products.push({ name, price });
  renderProducts();
  e.target.reset();
}

function addToCart(index) {
  cart.push(products[index]);
  renderCart();
}

function renderCart() {
  const cartItems = document.getElementById('cart-items');
  cartItems.innerHTML = '';
  let total = 0;
  cart.forEach(item => {
    const li = document.createElement('li');
    li.textContent = `${item.name} - Rp${item.price}`;
    cartItems.appendChild(li);
    total += item.price;
  });
  document.getElementById('total').textContent = 'Rp' + total;
}

function checkout() {
  if (cart.length === 0) {
    alert('Keranjang kosong!');
    return;
  }
  const total = cart.reduce((sum, item) => sum + item.price, 0);
  alert(`Pembayaran berhasil sebesar Rp${total} (Simulasi gateway pembayaran)`);
  transactions.push({ items: [...cart], total, date: new Date().toLocaleString() });
  cart = [];
  renderCart();
  renderHistory();
}

function renderHistory() {
  const historyList = document.getElementById('history-list');
  historyList.innerHTML = '';
  transactions.forEach((trx, idx) => {
    const li = document.createElement('li');
    li.innerHTML = `#${idx + 1} - ${trx.date} - Rp${trx.total}`;
    historyList.appendChild(li);
  });
}

function login(e) {
  e.preventDefault();
  const user = document.getElementById('admin-user').value;
  const pass = document.getElementById('admin-pass').value;
  if (user === admin.username && pass === admin.password) {
    alert('Login berhasil');
    document.getElementById('admin-dashboard').style.display = 'block';
    document.getElementById('transaction-history').style.display = 'block';
  } else {
    alert('Username atau password salah');
  }
  e.target.reset();
}

window.onload = () => {
  renderProducts();
};
`;

export const ecommerceFiles = {
  'index.html': html,
  'style.css': css,
  'script.js': js
};
