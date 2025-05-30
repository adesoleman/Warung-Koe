# Warung-Koe
const express = require('express');
const mongoose = require('mongoose');
const dotenv = require('dotenv');
const cors = require('cors');

dotenv.config();
const app = express();

const adminRoutes = require('./routes/admin');
const paymentRoutes = require('./routes/payment');

app.use(cors());
app.use(express.json());

mongoose.connect(process.env.MONGO_URI)
  .then(() => console.log('MongoDB connected'))
  .catch((err) => console.error(err));

app.use('/api/admin', adminRoutes);
app.use('/api/payment', paymentRoutes);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));

const mongoose = require('mongoose');
const ProductSchema = new mongoose.Schema({
  name: String,
  price: Number,
});
module.exports = mongoose.model('Product', ProductSchema);

const mongoose = require('mongoose');
const TransactionSchema = new mongoose.Schema({
  items: Array,
  total: Number,
  date: { type: Date, default: Date.now }
});
module.exports = mongoose.model('Transaction', TransactionSchema);

const express = require('express');
const router = express.Router();
const Product = require('../models/Product');

// Add product
router.post('/product', async (req, res) => {
  const { name, price } = req.body;
  const newProduct = new Product({ name, price });
  await newProduct.save();
  res.json(newProduct);
});

// Get all products
router.get('/product', async (req, res) => {
  const products = await Product.find();
  res.json(products);
});

module.exports = router;

const express = require('express');
const router = express.Router();
const Transaction = require('../models/Transaction');
const midtransClient = require('midtrans-client');

const snap = new midtransClient.Snap({
  isProduction: false,
  serverKey: process.env.MIDTRANS_SERVER_KEY,
});

router.post('/checkout', async (req, res) => {
  const { items } = req.body;
  const total = items.reduce((sum, item) => sum + item.price, 0);
  const orderId = 'ORDER-' + Date.now();

  const parameter = {
    transaction_details: {
      order_id: orderId,
      gross_amount: total
    },
    credit_card: { secure: true },
    customer_details: {
      first_name: 'Pembeli',
      email: 'pembeli@example.com'
    }
  };

  try {
    const transaction = await snap.createTransaction(parameter);
    await new Transaction({ items, total }).save();
    res.json({ redirect_url: transaction.redirect_url });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

router.get('/history', async (req, res) => {
  const history = await Transaction.find().sort({ date: -1 });
  res.json(history);
});

module.exports = router;
