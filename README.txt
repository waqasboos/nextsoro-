NEXTSORO - AFFILIATE MARKETING WEBSITE
=======================================

Nextsoro is a professional, production-ready affiliate marketing website built with Firebase Realtime Database as the primary backend, with an optional JSON fallback.

PROJECT STRUCTURE
-----------------
- main_site.html: Public-facing e-commerce site
- admin.html: Admin panel for managing products
- admin.css: Styles for the admin panel
- products.json: Sample product data
- README.txt: This file

FEATURES
--------
- Modern, responsive design inspired by Flipkart/Amazon
- Real-time product updates using Firebase
- Product catalog with search and category filters
- Product detail pages with image galleries
- Admin panel with full CRUD operations
- Image upload to Firebase Storage
- Import/export functionality
- Hash-based routing for shareable product URLs

FIREBASE SETUP
--------------
1. Create a Firebase project at https://console.firebase.google.com
2. Enable Realtime Database:
   - Go to Build > Realtime Database
   - Click "Create Database"
   - Start in test mode (for development)
3. Enable Firebase Storage:
   - Go to Build > Storage
   - Click "Get Started"
   - Start in test mode (for development)
4. The provided firebaseConfig in the code is already configured for a demo project. For production, replace with your own Firebase project config.

DEPLOYMENT
----------
Static Hosting (Firebase Backend):
1. Upload all files to a static hosting service (Netlify, Vercel, GitHub Pages, etc.)
2. No server required when using Firebase backend

Server Hosting (JSON Backend):
1. Set DATA_BACKEND to "json" in the configuration section
2. Deploy the files to a web server
3. For write operations, you'll need a server API (see Server API Example below)

CONFIGURATION
-------------
In both main_site.html and admin.html, find the CONFIG object at the top of the JavaScript:

const CONFIG = {
  SITE_NAME: "Nextsoro",
  ADMIN_PASSWORD: "Password (@#@$@#%@#%26#2#6#26#%26#&26#&2sdhdddgg32#$%#3#%%#454)",
  THEME_COLORS: {
    primaryBlue: "#2563eb",
    primaryPurple: "#7c3aed"
  },
  DATA_BACKEND: "firebase" // or "json"
};

Change these values as needed for your implementation.

SWITCHING BETWEEN BACKENDS
--------------------------
To switch from Firebase to JSON backend:
1. Change DATA_BACKEND to "json" in the CONFIG object
2. Ensure products.json is accessible at the root of your deployment
3. Note: Write operations (add, edit, delete) will not work with JSON backend without a server API

For JSON backend with write operations, you need to implement server-side endpoints.

SERVER API EXAMPLE (Node.js/Express)
------------------------------------
For JSON backend with full CRUD operations, you can use this example:

const express = require('express');
const fs = require('fs').promises;
const path = require('path');
const app = express();
const port = 3000;

app.use(express.json());
app.use(express.static('public'));

const productsFile = path.join(__dirname, 'products.json');

// GET all products
app.get('/api/products', async (req, res) => {
  try {
    const data = await fs.readFile(productsFile, 'utf8');
    res.json(JSON.parse(data));
  } catch (error) {
    res.status(500).json({ error: 'Failed to read products' });
  }
});

// POST new product
app.post('/api/products', async (req, res) => {
  try {
    const data = await fs.readFile(productsFile, 'utf8');
    const products = JSON.parse(data);
    const newProduct = {
      id: 'prod-' + Date.now(),
      ...req.body,
      createdAt: new Date().toISOString()
    };
    products.push(newProduct);
    await fs.writeFile(productsFile, JSON.stringify(products, null, 2));
    res.json(newProduct);
  } catch (error) {
    res.status(500).json({ error: 'Failed to add product' });
  }
});

// PUT update product
app.put('/api/products/:id', async (req, res) => {
  try {
    const data = await fs.readFile(productsFile, 'utf8');
    let products = JSON.parse(data);
    const index = products.findIndex(p => p.id === req.params.id);
    if (index === -1) return res.status(404).json({ error: 'Product not found' });
    
    products[index] = {
      ...products[index],
      ...req.body,
      updatedAt: new Date().toISOString()
    };
    
    await fs.writeFile(productsFile, JSON.stringify(products, null, 2));
    res.json(products[index]);
  } catch (error) {
    res.status(500).json({ error: 'Failed to update product' });
  }
});

// DELETE product
app.delete('/api/products/:id', async (req, res) => {
  try {
    const data = await fs.readFile(productsFile, 'utf8');
    let products = JSON.parse(data);
    products = products.filter(p => p.id !== req.params.id);
    await fs.writeFile(productsFile, JSON.stringify(products, null, 2));
    res.json({ message: 'Product deleted' });
  } catch (error) {
    res.status(500).json({ error: 'Failed to delete product' });
  }
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});

SECURITY NOTES
--------------
- The provided admin password is for demonstration. Change it for production.
- Firebase test mode rules allow read/write to anyone. For production, implement proper security rules.
- For JSON backend, implement proper authentication for write operations.
- Never expose admin credentials in client-side code in production.

REAL-TIME BEHAVIOR
------------------
The Firebase implementation uses real-time listeners (onValue) to automatically update the product list when changes are made in the admin panel.

For JSON backend, you would need to implement polling:

// Polling example for JSON backend
function startPolling() {
  setInterval(fetchProductsJSON, 5000); // Poll every 5 seconds
}

Or implement WebSocket communication for real-time updates.

INITIAL SETUP
-------------
1. Deploy the files to your hosting platform
2. If using Firebase backend, ensure your Firebase project is properly configured
3. Access the admin panel at /admin.html
4. Use the default password to login
5. Add products or import the sample products.json

SUPPORT
-------
For issues or questions, refer to the code comments or standard web development resources.

LICENSE
-------
This project is provided as-is for educational and commercial use.
