# milanyeusi.store
<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MILANYEUSI | Live E-Commerce Store & Admin Portal</title>
    
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            black: '#0a0a0c',
                            card: '#141418',
                            border: '#26262e',
                            accent: '#3b82f6',
                            emerald: '#10b981',
                            amber: '#f59e0b'
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['JetBrains Mono', 'monospace']
                    }
                }
            }
        }
    </script>

    <!-- Google Fonts & FontAwesome -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <!-- Firebase App & Database Scripts (v10 Modular via ES Modules) -->
    <script type="module">
        import { initializeApp, getApps } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-app.js";
        import { getDatabase, ref, set, get, onValue, push, remove, update } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-database.js";
        
        window.FirebaseSDK = { initializeApp, getApps, getDatabase, ref, set, get, onValue, push, remove, update };
    </script>

    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0a0a0c;
            color: #f3f4f6;
            overflow-x: hidden;
        }
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #0a0a0c;
        }
        ::-webkit-scrollbar-thumb {
            background: #26262e;
            border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #3b82f6;
        }
        .glass-panel {
            background: rgba(20, 20, 24, 0.85);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between selection:bg-blue-600 selection:text-white">

    <!-- Top Bar -->
    <div class="bg-gradient-to-r from-blue-900 via-indigo-900 to-black text-white text-xs font-mono py-2 px-4 text-center tracking-wider flex justify-between items-center z-50 relative">
        <span class="hidden sm:inline-block">⚡ REAL-TIME CLOUD DATABASE ACTIVE</span>
        <span class="w-full sm:w-auto text-center font-bold">MILANYEUSI ADMIN & E-COMMERCE SUITE</span>
        <div class="hidden md:flex gap-3">
            <button onclick="toggleGithubGuide(true)" class="text-blue-300 hover:text-white font-bold transition flex items-center gap-1">
                <i class="fab fa-github"></i> Host Guide
            </button>
            <button onclick="openConfigModal()" class="text-amber-400 hover:text-amber-300 font-bold transition flex items-center gap-1">
                <i class="fas fa-database"></i> Firebase Config
            </button>
        </div>
    </div>

    <!-- Main Navigation Bar -->
    <nav class="sticky top-0 z-40 glass-panel border-b border-brand-border px-4 lg:px-8 py-3.5 transition-all">
        <div class="max-w-7xl mx-auto flex items-center justify-between gap-4">
            <!-- Brand Logo -->
            <div class="flex items-center gap-3">
                <a href="#" onclick="switchView('store')" class="text-2xl font-black tracking-tighter text-white flex items-center gap-1.5">
                    <span class="bg-white text-black px-2 py-0.5 rounded font-mono font-black text-xl">M</span>
                    <span>MILANYEUSI</span>
                </a>
            </div>

            <!-- Store / Admin View Switcher Tabs -->
            <div class="flex items-center bg-brand-black p-1 rounded-xl border border-brand-border">
                <button id="nav-btn-store" onclick="switchView('store')" class="px-4 py-1.5 rounded-lg text-xs font-bold uppercase transition bg-blue-600 text-white shadow-md">
                    <i class="fas fa-store mr-1.5"></i> Storefront
                </button>
                <button id="nav-btn-admin" onclick="switchView('admin')" class="px-4 py-1.5 rounded-lg text-xs font-bold uppercase transition text-gray-400 hover:text-white">
                    <i class="fas fa-user-shield mr-1.5"></i> Admin Dashboard
                </button>
            </div>

            <!-- Actions Right -->
            <div class="flex items-center gap-3">
                <!-- Search Box -->
                <div class="relative hidden sm:block w-36 md:w-48">
                    <input type="text" id="search-input" onkeyup="filterProducts()" placeholder="Search catalog..." class="w-full bg-brand-card text-xs text-white placeholder-gray-500 rounded-full py-2 pl-8 pr-3 border border-brand-border focus:outline-none focus:border-blue-500">
                    <i class="fas fa-search absolute left-3 top-2.5 text-xs text-gray-500"></i>
                </div>

                <!-- Database Status Badge -->
                <button onclick="openConfigModal()" id="db-status-badge" class="bg-emerald-950/60 border border-emerald-500/40 text-emerald-400 text-[10px] font-mono px-2.5 py-1.5 rounded-lg flex items-center gap-1.5">
                    <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
                    <span id="db-status-text">Cloud DB</span>
                </button>

                <!-- Cart Button -->
                <button onclick="toggleCartDrawer(true)" class="bg-blue-600 hover:bg-blue-500 text-white p-2.5 rounded-full shadow-lg transition flex items-center justify-center relative">
                    <i class="fas fa-shopping-bag"></i>
                    <span id="cart-count-badge" class="absolute -top-1.5 -right-1.5 bg-red-500 text-white text-[10px] font-bold w-5 h-5 rounded-full flex items-center justify-center border-2 border-brand-black">0</span>
                </button>
            </div>
        </div>
    </nav>

    <!-- VIEW 1: PUBLIC STOREFRONT -->
    <main id="view-store" class="flex-1">
        <!-- Hero Banner -->
        <header class="relative py-16 border-b border-brand-border overflow-hidden bg-brand-card">
            <div class="max-w-7xl mx-auto px-6 text-center relative z-10">
                <span class="text-xs font-mono text-blue-400 uppercase tracking-widest">// LIVE CLOUD STOREFRONT</span>
                <h1 class="text-4xl sm:text-6xl font-black uppercase text-white tracking-tight mt-2 mb-4">
                    FULLY EDITABLE STREETWEAR
                </h1>
                <p class="max-w-2xl mx-auto text-gray-400 text-sm leading-relaxed mb-6">
                    Edit products, prices, descriptions, and stock directly inside the Admin Dashboard. All changes sync in real-time across all active user screens via Firebase!
                </p>
                <div class="flex justify-center gap-3">
                    <a href="#catalog" class="bg-white hover:bg-gray-200 text-black font-extrabold text-xs uppercase px-6 py-3 rounded-xl transition">Shop Products</a>
                    <button onclick="switchView('admin')" class="bg-blue-600/20 hover:bg-blue-600/40 text-blue-400 border border-blue-500/40 font-bold text-xs uppercase px-6 py-3 rounded-xl transition">Open Admin Panel</button>
                </div>
            </div>
        </header>

        <!-- Store Catalog Section -->
        <section id="catalog" class="max-w-7xl mx-auto px-4 lg:px-8 py-12">
            <!-- Filter Bar -->
            <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-8 pb-4 border-b border-brand-border">
                <div>
                    <h2 class="text-2xl font-black uppercase text-white">CATALOG ITEMS</h2>
                    <p class="text-xs text-gray-400 font-mono" id="catalog-count-text">Loading catalog...</p>
                </div>

                <div class="flex flex-wrap gap-2">
                    <button onclick="filterCategory('All')" class="cat-btn active bg-blue-600 text-white text-xs font-bold px-4 py-2 rounded-lg transition">All</button>
                    <button onclick="filterCategory('Apparel')" class="cat-btn bg-brand-card hover:bg-gray-800 text-gray-300 text-xs font-bold px-4 py-2 rounded-lg border border-brand-border transition">Apparel</button>
                    <button onclick="filterCategory('Shoes')" class="cat-btn bg-brand-card hover:bg-gray-800 text-gray-300 text-xs font-bold px-4 py-2 rounded-lg border border-brand-border transition">Shoes</button>
                    <button onclick="filterCategory('Accessories')" class="cat-btn bg-brand-card hover:bg-gray-800 text-gray-300 text-xs font-bold px-4 py-2 rounded-lg border border-brand-border transition">Accessories</button>
                    <button onclick="filterCategory('Tech')" class="cat-btn bg-brand-card hover:bg-gray-800 text-gray-300 text-xs font-bold px-4 py-2 rounded-lg border border-brand-border transition">Tech</button>
                </div>
            </div>

            <!-- Dynamic Product Grid -->
            <div id="product-grid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
                <!-- Products rendered via JS -->
            </div>
        </section>
    </main>

    <!-- VIEW 2: ADMIN MANAGEMENT DASHBOARD -->
    <main id="view-admin" class="flex-1 hidden max-w-7xl mx-auto px-4 lg:px-8 py-8 w-full">
        <!-- Admin Dashboard Header -->
        <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 mb-8 p-6 bg-brand-card border border-brand-border rounded-2xl">
            <div>
                <span class="text-xs font-mono text-blue-400 uppercase tracking-widest">// CONTROL CENTER</span>
                <h2 class="text-3xl font-black text-white uppercase">STORE MANAGEMENT DASHBOARD</h2>
                <p class="text-xs text-gray-400 mt-1">Manage database records, product inventory, and customer orders in real-time.</p>
            </div>
            <div class="flex gap-2">
                <button onclick="openProductModal()" class="bg-blue-600 hover:bg-blue-500 text-white font-bold text-xs uppercase px-5 py-3 rounded-xl transition flex items-center gap-2">
                    <i class="fas fa-plus"></i> Add New Product
                </button>
                <button onclick="resetDefaultDatabase()" class="bg-brand-black hover:bg-gray-800 text-gray-300 border border-brand-border font-bold text-xs uppercase px-4 py-3 rounded-xl transition">
                    <i class="fas fa-rotate"></i> Reset Defaults
                </button>
            </div>
        </div>

        <!-- Admin Stats Bar -->
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 mb-8">
            <div class="glass-panel p-5 rounded-xl border border-brand-border">
                <span class="text-xs font-mono text-gray-400 uppercase">Total Catalog Items</span>
                <p id="stat-total-items" class="text-3xl font-black text-white mt-1">0</p>
            </div>
            <div class="glass-panel p-5 rounded-xl border border-brand-border">
                <span class="text-xs font-mono text-gray-400 uppercase">Total Inventory Units</span>
                <p id="stat-total-stock" class="text-3xl font-black text-blue-400 mt-1">0</p>
            </div>
            <div class="glass-panel p-5 rounded-xl border border-brand-border">
                <span class="text-xs font-mono text-gray-400 uppercase">Customer Orders Placed</span>
                <p id="stat-total-orders" class="text-3xl font-black text-emerald-400 mt-1">0</p>
            </div>
        </div>

        <!-- Admin Sub-Tabs -->
        <div class="flex gap-4 border-b border-brand-border mb-6">
            <button id="admin-tab-products-btn" onclick="switchAdminTab('products')" class="pb-3 text-sm font-bold uppercase border-b-2 border-blue-500 text-white">Product Inventory</button>
            <button id="admin-tab-orders-btn" onclick="switchAdminTab('orders')" class="pb-3 text-sm font-bold uppercase border-b-2 border-transparent text-gray-400 hover:text-white">Order Logs</button>
        </div>

        <!-- Admin Tab 1: Product Inventory Table -->
        <div id="admin-tab-products" class="bg-brand-card border border-brand-border rounded-2xl overflow-hidden">
            <div class="overflow-x-auto">
                <table class="w-full text-left text-xs text-gray-300">
                    <thead class="bg-brand-black text-gray-400 uppercase font-mono border-b border-brand-border">
                        <tr>
                            <th class="p-4">Item</th>
                            <th class="p-4">Category</th>
                            <th class="p-4">Price ($)</th>
                            <th class="p-4">Stock</th>
                            <th class="p-4 text-right">Actions</th>
                        </tr>
                    </thead>
                    <tbody id="admin-product-table-body" class="divide-y divide-brand-border font-mono">
                        <!-- Populated via JavaScript -->
                    </tbody>
                </table>
            </div>
        </div>

        <!-- Admin Tab 2: Customer Order History -->
        <div id="admin-tab-orders" class="bg-brand-card border border-brand-border rounded-2xl overflow-hidden hidden">
            <div class="overflow-x-auto">
                <table class="w-full text-left text-xs text-gray-300">
                    <thead class="bg-brand-black text-gray-400 uppercase font-mono border-b border-brand-border">
                        <tr>
                            <th class="p-4">Order ID</th>
                            <th class="p-4">Customer</th>
                            <th class="p-4">Items</th>
                            <th class="p-4">Total</th>
                            <th class="p-4">Date</th>
                        </tr>
                    </thead>
                    <tbody id="admin-order-table-body" class="divide-y divide-brand-border font-mono">
                        <!-- Populated via JavaScript -->
                    </tbody>
                </table>
            </div>
        </div>
    </main>

    <!-- Slide-Out Cart Drawer -->
    <div id="cart-drawer" class="fixed inset-0 z-50 overflow-hidden hidden">
        <div onclick="toggleCartDrawer(false)" class="absolute inset-0 bg-black/70 backdrop-blur-sm"></div>
        <div class="fixed inset-y-0 right-0 max-w-full flex pl-10">
            <div class="w-screen max-w-md bg-brand-card border-l border-brand-border flex flex-col justify-between shadow-2xl">
                <div class="p-6 border-b border-brand-border flex items-center justify-between">
                    <div class="flex items-center gap-2">
                        <i class="fas fa-shopping-bag text-blue-400"></i>
                        <h3 class="text-lg font-black uppercase text-white">YOUR CART</h3>
                    </div>
                    <button onclick="toggleCartDrawer(false)" class="text-gray-400 hover:text-white p-2 text-xl"><i class="fas fa-times"></i></button>
                </div>

                <div id="cart-items-container" class="p-6 flex-1 overflow-y-auto space-y-4">
                    <!-- Dynamic cart items -->
                </div>

                <div class="p-6 border-t border-brand-border bg-brand-black space-y-4">
                    <div class="flex justify-between text-white font-mono font-bold text-base">
                        <span>TOTAL:</span>
                        <span id="cart-total-price" class="text-blue-400">$0.00</span>
                    </div>
                    <button onclick="openCheckoutModal()" class="w-full bg-blue-600 hover:bg-blue-500 text-white font-extrabold uppercase text-xs tracking-widest py-3.5 rounded-xl transition">
                        Proceed To Checkout
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Product Create / Edit Modal -->
    <div id="product-modal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/80 backdrop-blur-md hidden">
        <div class="bg-brand-card border border-brand-border rounded-2xl max-w-md w-full p-6 relative">
            <button onclick="closeProductModal()" class="absolute top-4 right-4 text-gray-400 hover:text-white text-xl"><i class="fas fa-times"></i></button>
            
            <h3 id="product-modal-title" class="text-xl font-black uppercase text-white mb-4">ADD NEW PRODUCT</h3>
            
            <form id="product-form" onsubmit="saveProduct(event)" class="space-y-4 text-xs">
                <input type="hidden" id="form-product-id">
                
                <div>
                    <label class="block text-gray-400 mb-1 font-mono uppercase">Product Title</label>
                    <input type="text" id="form-product-name" required placeholder="e.g. Tactical Cargo Hoodie" class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:border-blue-500 focus:outline-none">
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-gray-400 mb-1 font-mono uppercase">Category</label>
                        <select id="form-product-category" class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:border-blue-500 focus:outline-none">
                            <option value="Apparel">Apparel</option>
                            <option value="Shoes">Shoes</option>
                            <option value="Accessories">Accessories</option>
                            <option value="Tech">Tech</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-gray-400 mb-1 font-mono uppercase">Price ($ USD)</label>
                        <input type="number" step="0.01" id="form-product-price" required placeholder="120.00" class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:border-blue-500 focus:outline-none">
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-gray-400 mb-1 font-mono uppercase">Stock Units</label>
                        <input type="number" id="form-product-stock" required placeholder="25" class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:border-blue-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-gray-400 mb-1 font-mono uppercase">Rating (1-5)</label>
                        <input type="number" step="0.1" id="form-product-rating" value="4.8" class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:border-blue-500 focus:outline-none">
                    </div>
                </div>

                <div>
                    <label class="block text-gray-400 mb-1 font-mono uppercase">Image URL</label>
                    <input type="url" id="form-product-image" required placeholder="https://images.unsplash.com/..." class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:border-blue-500 focus:outline-none">
                </div>

                <div>
                    <label class="block text-gray-400 mb-1 font-mono uppercase">Description</label>
                    <textarea id="form-product-desc" rows="3" required placeholder="Item specifications..." class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:border-blue-500 focus:outline-none"></textarea>
                </div>

                <button type="submit" class="w-full bg-blue-600 hover:bg-blue-500 text-white font-extrabold uppercase py-3 rounded-xl transition">
                    Save Product To Database
                </button>
            </form>
        </div>
    </div>

    <!-- Quick View Modal -->
    <div id="quickview-modal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/80 backdrop-blur-md hidden">
        <div class="bg-brand-card border border-brand-border rounded-2xl max-w-2xl w-full p-6 relative">
            <button onclick="closeQuickView()" class="absolute top-4 right-4 text-gray-400 hover:text-white text-xl"><i class="fas fa-times"></i></button>
            <div id="quickview-content" class="grid grid-cols-1 sm:grid-cols-2 gap-6"></div>
        </div>
    </div>

    <!-- Checkout Modal -->
    <div id="checkout-modal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/85 backdrop-blur-md hidden">
        <div class="bg-brand-card border border-brand-border rounded-2xl max-w-lg w-full p-6 relative">
            <button onclick="closeCheckoutModal()" class="absolute top-4 right-4 text-gray-400 hover:text-white text-xl"><i class="fas fa-times"></i></button>
            <h3 class="text-xl font-black uppercase text-white mb-4">COMPLETE ORDER</h3>
            <form onsubmit="processCheckout(event)" class="space-y-4 text-xs">
                <input type="text" id="cust-name" required placeholder="Full Name" class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:outline-none">
                <input type="email" id="cust-email" required placeholder="Email Address" class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:outline-none">
                <input type="text" id="cust-address" required placeholder="Shipping Address" class="w-full bg-brand-black text-white p-3 rounded-lg border border-brand-border focus:outline-none">
                <button type="submit" class="w-full bg-emerald-600 hover:bg-emerald-500 text-white font-extrabold uppercase py-3 rounded-xl transition">
                    Confirm & Submit Order
                </button>
            </form>
        </div>
    </div>

    <!-- Database Configuration Modal -->
    <div id="config-modal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/90 backdrop-blur-md hidden">
        <div class="bg-brand-card border border-amber-500/40 rounded-2xl max-w-lg w-full p-6 relative">
            <button onclick="closeConfigModal()" class="absolute top-4 right-4 text-gray-400 hover:text-white text-xl"><i class="fas fa-times"></i></button>
            <div class="flex items-center gap-2 mb-2">
                <i class="fas fa-database text-amber-400 text-xl"></i>
                <h3 class="text-xl font-black uppercase text-white">FIREBASE REALTIME DATABASE CONFIG</h3>
            </div>
            <p class="text-xs text-gray-400 mb-4">Plug in your custom Firebase keys below to connect your store to your free online database project.</p>
            
            <form onsubmit="saveFirebaseConfig(event)" class="space-y-3 text-xs font-mono">
                <div>
                    <label class="block text-gray-400 mb-1">Database URL (databaseURL)</label>
                    <input type="text" id="cfg-db-url" placeholder="https://your-project-default-rtdb.firebaseio.com" class="w-full bg-brand-black text-amber-300 p-2.5 rounded-lg border border-brand-border">
                </div>
                <div>
                    <label class="block text-gray-400 mb-1">API Key (apiKey)</label>
                    <input type="text" id="cfg-api-key" placeholder="AIzaSy..." class="w-full bg-brand-black text-white p-2.5 rounded-lg border border-brand-border">
                </div>
                <div>
                    <label class="block text-gray-400 mb-1">Project ID (projectId)</label>
                    <input type="text" id="cfg-project-id" placeholder="your-project-id" class="w-full bg-brand-black text-white p-2.5 rounded-lg border border-brand-border">
                </div>
                <div class="pt-2 flex gap-2">
                    <button type="submit" class="w-full bg-amber-600 hover:bg-amber-500 text-white font-bold uppercase py-3 rounded-xl transition">
                        Connect Custom Firebase
                    </button>
                    <button type="button" onclick="clearFirebaseConfig()" class="bg-brand-black hover:bg-gray-800 text-gray-400 border border-brand-border font-bold uppercase px-4 py-3 rounded-xl transition">
                        Use Local
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- GitHub Hosting Guide Modal -->
    <div id="github-modal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/90 backdrop-blur-md hidden">
        <div class="bg-brand-card border border-blue-500/40 rounded-2xl max-w-2xl w-full p-6 relative max-h-[85vh] overflow-y-auto">
            <button onclick="toggleGithubGuide(false)" class="absolute top-4 right-4 text-gray-400 hover:text-white text-xl"><i class="fas fa-times"></i></button>
            <h3 class="text-xl font-black uppercase text-white mb-4"><i class="fab fa-github text-blue-400"></i> Free GitHub Pages & Database Guide</h3>
            <div class="space-y-4 text-xs text-gray-300">
                <div class="p-3 bg-brand-black rounded-lg border border-brand-border">
                    <h4 class="font-bold text-white mb-1">1. Deploy Store on GitHub Pages</h4>
                    <p class="text-gray-400">Create a repository on GitHub named <code>my-store</code>, upload this <code>index.html</code> file, go to Repository Settings &gt; Pages, and select <code>main</code> branch to publish instantly!</p>
                </div>
                <div class="p-3 bg-brand-black rounded-lg border border-brand-border">
                    <h4 class="font-bold text-white mb-1">2. Enable Permanent Free Online Database</h4>
                    <p class="text-gray-400">Go to <a href="https://console.firebase.google.com/" target="_blank" class="text-blue-400 underline">firebase.google.com</a>, create a free project, create a <strong>Realtime Database</strong>, set rules to <code>".read": true, ".write": true</code>, copy your config credentials, and click <strong>"Firebase Config"</strong> in the navbar to connect!</p>
                </div>
            </div>
        </div>
    </div>

    <!-- Toast Notification -->
    <div id="toast" class="fixed bottom-6 right-6 z-50 glass-panel border border-blue-500/40 px-5 py-3.5 rounded-xl shadow-2xl flex items-center gap-3 transform translate-y-20 opacity-0 transition-all duration-300">
        <i class="fas fa-circle-check text-emerald-400 text-lg"></i>
        <span id="toast-message" class="text-xs font-semibold text-white">Notification</span>
    </div>

    <script>
        // Default Mock Data in case Firebase Config is not set
        const DEFAULT_PRODUCTS = [
            { id: "p1", name: "Cyberpunk Tech Hoodie", category: "Apparel", price: 125, stock: 18, rating: 4.9, image: "https://images.unsplash.com/photo-1556905055-8f358a7a47b2?auto=format&fit=crop&w=800&q=80", desc: "450 GSM Heavyweight cotton hoodie with custom metallic zip." },
            { id: "p2", name: "Modular Industrial Sneaker", category: "Shoes", price: 195, stock: 8, rating: 5.0, image: "https://images.unsplash.com/photo-1552346154-21d32810aba3?auto=format&fit=crop&w=800&q=80", desc: "High-top techwear sneaker with quick-lace closure." },
            { id: "p3", name: "Tactical Cordura Chest Bag", category: "Accessories", price: 65, stock: 25, rating: 4.7, image: "https://images.unsplash.com/photo-1553062407-98eeb64c6a62?auto=format&fit=crop&w=800&q=80", desc: "Waterproof compact pouch with military buckles." },
            { id: "p4", name: "Oversized Acid Tee", category: "Apparel", price: 55, stock: 30, rating: 4.8, image: "https://images.unsplash.com/photo-1521572267360-ee0c2909d518?auto=format&fit=crop&w=800&q=80", desc: "280 GSM premium vintage washed heavy cotton." }
        ];

        // Global State
        let products = [];
        let orders = [];
        let cart = JSON.parse(localStorage.getItem('STORE_CART')) || [];
        let activeCategory = 'All';
        let firebaseDb = null;

        // Initialize Firebase SDK or Local Fallback
        window.onload = function() {
            initDatabase();
        };

        function initDatabase() {
            const savedCfg = JSON.parse(localStorage.getItem('FIREBASE_CONFIG'));
            
            if (savedCfg && savedCfg.databaseURL) {
                try {
                    const { initializeApp, getDatabase, ref, onValue } = window.FirebaseSDK;
                    const app = initializeApp(savedCfg);
                    firebaseDb = getDatabase(app);
                    
                    document.getElementById('db-status-text').innerText = "Firebase Connected";
                    document.getElementById('db-status-badge').className = "bg-emerald-950/60 border border-emerald-500/40 text-emerald-400 text-[10px] font-mono px-2.5 py-1.5 rounded-lg flex items-center gap-1.5";

                    // Sync Products in Realtime
                    onValue(ref(firebaseDb, 'products'), (snapshot) => {
                        const data = snapshot.val();
                        if (data) {
                            products = Object.keys(data).map(key => ({ id: key, ...data[key] }));
                        } else {
                            products = DEFAULT_PRODUCTS;
                        }
                        renderStore();
                        renderAdminTable();
                    });

                    // Sync Orders in Realtime
                    onValue(ref(firebaseDb, 'orders'), (snapshot) => {
                        const data = snapshot.val();
                        orders = data ? Object.keys(data).map(key => ({ id: key, ...data[key] })) : [];
                        renderAdminOrders();
                    });

                    return;
                } catch(e) {
                    console.warn("Firebase init error, using local database:", e);
                }
            }

            // LocalStorage Fallback
            products = JSON.parse(localStorage.getItem('LOCAL_PRODUCTS')) || DEFAULT_PRODUCTS;
            orders = JSON.parse(localStorage.getItem('LOCAL_ORDERS')) || [];
            document.getElementById('db-status-text').innerText = "Local Database";
            document.getElementById('db-status-badge').className = "bg-amber-950/60 border border-amber-500/40 text-amber-400 text-[10px] font-mono px-2.5 py-1.5 rounded-lg flex items-center gap-1.5";
            
            renderStore();
            renderAdminTable();
            renderAdminOrders();
        }

        // View Switcher (Storefront vs Admin Dashboard)
        function switchView(view) {
            document.getElementById('view-store').classList.toggle('hidden', view !== 'store');
            document.getElementById('view-admin').classList.toggle('hidden', view !== 'admin');

            document.getElementById('nav-btn-store').className = view === 'store' ? 'px-4 py-1.5 rounded-lg text-xs font-bold uppercase transition bg-blue-600 text-white shadow-md' : 'px-4 py-1.5 rounded-lg text-xs font-bold uppercase transition text-gray-400 hover:text-white';
            document.getElementById('nav-btn-admin').className = view === 'admin' ? 'px-4 py-1.5 rounded-lg text-xs font-bold uppercase transition bg-blue-600 text-white shadow-md' : 'px-4 py-1.5 rounded-lg text-xs font-bold uppercase transition text-gray-400 hover:text-white';
        }

        // Render Public Catalog Grid
        function renderStore() {
            const grid = document.getElementById('product-grid');
            const searchVal = (document.getElementById('search-input')?.value || '').toLowerCase();

            const filtered = products.filter(p => {
                const catMatch = activeCategory === 'All' || p.category === activeCategory;
                const searchMatch = p.name.toLowerCase().includes(searchVal) || p.desc.toLowerCase().includes(searchVal);
                return catMatch && searchMatch;
            });

            document.getElementById('catalog-count-text').innerText = `SHOWING ${filtered.length} OF ${products.length} CLOUD ITEMS`;

            if (filtered.length === 0) {
                grid.innerHTML = `<div class="col-span-full py-12 text-center text-gray-500 font-mono">No matching products found.</div>`;
                return;
            }

            grid.innerHTML = filtered.map(p => `
                <div class="bg-brand-card border border-brand-border rounded-2xl overflow-hidden hover:border-blue-500/50 transition flex flex-col justify-between">
                    <div class="relative h-60 bg-black">
                        <img src="${p.image}" alt="${p.name}" class="w-full h-full object-cover">
                        <span class="absolute top-3 left-3 bg-blue-600/90 text-white font-mono text-[10px] px-2 py-0.5 rounded font-bold uppercase">${p.category}</span>
                        <span class="absolute top-3 right-3 bg-black/80 text-gray-300 font-mono text-[10px] px-2 py-0.5 rounded border border-brand-border">Stock: ${p.stock}</span>
                    </div>
                    <div class="p-5 flex-1 flex flex-col justify-between space-y-3">
                        <div>
                            <div class="text-[10px] text-amber-400 font-bold mb-1"><i class="fas fa-star"></i> ${p.rating}</div>
                            <h3 class="font-bold text-white uppercase text-sm">${p.name}</h3>
                            <p class="text-xs text-gray-400 line-clamp-2 mt-1">${p.desc}</p>
                        </div>
                        <div class="pt-3 border-t border-brand-border flex items-center justify-between">
                            <span class="text-lg font-black font-mono text-white">$${parseFloat(p.price).toFixed(2)}</span>
                            <button onclick="addToCart('${p.id}')" ${p.stock <= 0 ? 'disabled' : ''} class="bg-blue-600 hover:bg-blue-500 disabled:bg-gray-800 text-white text-xs font-bold uppercase px-3 py-2 rounded-lg transition">
                                ${p.stock > 0 ? '+ Add' : 'Out of Stock'}
                            </button>
                        </div>
                    </div>
                </div>
            `).join('');

            updateCartUI();
        }

        // Render Admin Inventory Table
        function renderAdminTable() {
            const tbody = document.getElementById('admin-product-table-body');
            tbody.innerHTML = products.map(p => `
                <tr class="hover:bg-brand-black/50 transition">
                    <td class="p-4 flex items-center gap-3">
                        <img src="${p.image}" class="w-10 h-10 object-cover rounded-lg border border-brand-border">
                        <div>
                            <p class="font-bold text-white uppercase">${p.name}</p>
                            <p class="text-[10px] text-gray-500">ID: ${p.id}</p>
                        </div>
                    </td>
                    <td class="p-4"><span class="bg-blue-950/60 text-blue-400 border border-blue-500/30 px-2 py-0.5 rounded">${p.category}</span></td>
                    <td class="p-4 font-bold text-white">$${parseFloat(p.price).toFixed(2)}</td>
                    <td class="p-4">${p.stock} units</td>
                    <td class="p-4 text-right space-x-2">
                        <button onclick="editProduct('${p.id}')" class="text-blue-400 hover:text-white p-1"><i class="fas fa-edit"></i></button>
                        <button onclick="deleteProduct('${p.id}')" class="text-red-400 hover:text-white p-1"><i class="fas fa-trash"></i></button>
                    </td>
                </tr>
            `).join('');

            // Update Admin Stats
            document.getElementById('stat-total-items').innerText = products.length;
            document.getElementById('stat-total-stock').innerText = products.reduce((sum, p) => sum + Number(p.stock), 0);
        }

        // Render Admin Orders
        function renderAdminOrders() {
            const tbody = document.getElementById('admin-order-table-body');
            document.getElementById('stat-total-orders').innerText = orders.length;

            if (orders.length === 0) {
                tbody.innerHTML = `<tr><td colspan="5" class="p-4 text-center text-gray-500">No customer orders logged yet.</td></tr>`;
                return;
            }

            tbody.innerHTML = orders.map(o => `
                <tr>
                    <td class="p-4 font-bold text-blue-400">${o.id}</td>
                    <td class="p-4"><p class="text-white font-bold">${o.customer}</p><p class="text-[10px] text-gray-500">${o.email}</p></td>
                    <td class="p-4 text-[10px]">${o.items}</td>
                    <td class="p-4 font-bold text-emerald-400">$${o.total.toFixed(2)}</td>
                    <td class="p-4 text-gray-500">${o.date}</td>
                </tr>
            `).join('');
        }

        function openProductModal(id = null) {
            document.getElementById('product-form').reset();
            document.getElementById('form-product-id').value = id || '';
            document.getElementById('product-modal-title').innerText = id ? 'EDIT PRODUCT' : 'ADD NEW PRODUCT';

            if (id) {
                const p = products.find(item => item.id === id);
                if (p) {
                    document.getElementById('form-product-name').value = p.name;
                    document.getElementById('form-product-category').value = p.category;
                    document.getElementById('form-product-price').value = p.price;
                    document.getElementById('form-product-stock').value = p.stock;
                    document.getElementById('form-product-rating').value = p.rating || 4.8;
                    document.getElementById('form-product-image').value = p.image;
                    document.getElementById('form-product-desc').value = p.desc;
                }
            }
            document.getElementById('product-modal').classList.remove('hidden');
        }

        function closeProductModal() {
            document.getElementById('product-modal').classList.add('hidden');
        }

        function saveProduct(e) {
            e.preventDefault();
            const id = document.getElementById('form-product-id').value || 'p_' + Date.now();
            const productData = {
                name: document.getElementById('form-product-name').value,
                category: document.getElementById('form-product-category').value,
                price: parseFloat(document.getElementById('form-product-price').value),
                stock: parseInt(document.getElementById('form-product-stock').value),
                rating: parseFloat(document.getElementById('form-product-rating').value),
                image: document.getElementById('form-product-image').value,
                desc: document.getElementById('form-product-desc').value
            };

            if (firebaseDb) {
                const { ref, set } = window.FirebaseSDK;
                set(ref(firebaseDb, 'products/' + id), productData);
            } else {
                const idx = products.findIndex(p => p.id === id);
                if (idx > -1) products[idx] = { id, ...productData };
                else products.push({ id, ...productData });

                localStorage.setItem('LOCAL_PRODUCTS', JSON.stringify(products));
                renderStore();
                renderAdminTable();
            }

            closeProductModal();
            showToast("Product saved to database!");
        }

        function editProduct(id) {
            openProductModal(id);
        }

        function deleteProduct(id) {
            if (!confirm("Delete this product from database?")) return;

            if (firebaseDb) {
                const { ref, remove } = window.FirebaseSDK;
                remove(ref(firebaseDb, 'products/' + id));
            } else {
                products = products.filter(p => p.id !== id);
                localStorage.setItem('LOCAL_PRODUCTS', JSON.stringify(products));
                renderStore();
                renderAdminTable();
            }
            showToast("Product deleted");
        }

        function resetDefaultDatabase() {
            if (!confirm("Reset database to default mock items?")) return;
            if (firebaseDb) {
                const { ref, set } = window.FirebaseSDK;
                const dbObj = {};
                DEFAULT_PRODUCTS.forEach(p => dbObj[p.id] = p);
                set(ref(firebaseDb, 'products'), dbObj);
            } else {
                products = DEFAULT_PRODUCTS;
                localStorage.setItem('LOCAL_PRODUCTS', JSON.stringify(products));
                renderStore();
                renderAdminTable();
            }
            showToast("Database reset to defaults");
        }

        // Cart Actions
        function addToCart(id) {
            const p = products.find(item => item.id === id);
            if (!p) return;

            const existing = cart.find(c => c.id === id);
            if (existing) {
                existing.qty += 1;
            } else {
                cart.push({ id: p.id, name: p.name, price: p.price, image: p.image, qty: 1 });
            }

            localStorage.setItem('STORE_CART', JSON.stringify(cart));
            updateCartUI();
            showToast(`Added ${p.name} to cart`);
        }

        function updateCartUI() {
            const countBadge = document.getElementById('cart-count-badge');
            const totalCount = cart.reduce((s, i) => s + i.qty, 0);
            countBadge.innerText = totalCount;

            const container = document.getElementById('cart-items-container');
            if (cart.length === 0) {
                container.innerHTML = `<p class="text-center text-gray-500 font-mono text-xs">Cart is empty</p>`;
            } else {
                container.innerHTML = cart.map(i => `
                    <div class="flex items-center gap-3 bg-brand-black p-3 rounded-xl border border-brand-border">
                        <img src="${i.image}" class="w-12 h-12 object-cover rounded-lg">
                        <div class="flex-1">
                            <h4 class="text-xs font-bold text-white uppercase">${i.name}</h4>
                            <p class="text-xs text-blue-400 font-mono font-bold">$${i.price} x ${i.qty}</p>
                        </div>
                    </div>
                `).join('');
            }

            const totalSum = cart.reduce((s, i) => s + (i.price * i.qty), 0);
            document.getElementById('cart-total-price').innerText = `$${totalSum.toFixed(2)}`;
        }

        function toggleCartDrawer(show) {
            document.getElementById('cart-drawer').classList.toggle('hidden', !show);
        }

        function openCheckoutModal() {
            if (cart.length === 0) return showToast("Cart is empty");
            toggleCartDrawer(false);
            document.getElementById('checkout-modal').classList.remove('hidden');
        }

        function closeCheckoutModal() {
            document.getElementById('checkout-modal').classList.add('hidden');
        }

        function processCheckout(e) {
            e.preventDefault();
            const orderId = 'ORD-' + Math.floor(100000 + Math.random() * 900000);
            const totalSum = cart.reduce((s, i) => s + (i.price * i.qty), 0);
            
            const newOrder = {
                customer: document.getElementById('cust-name').value,
                email: document.getElementById('cust-email').value,
                items: cart.map(i => `${i.name} (${i.qty})`).join(', '),
                total: totalSum,
                date: new Date().toLocaleDateString()
            };

            if (firebaseDb) {
                const { ref, set } = window.FirebaseSDK;
                set(ref(firebaseDb, 'orders/' + orderId), newOrder);
            } else {
                orders.push({ id: orderId, ...newOrder });
                localStorage.setItem('LOCAL_ORDERS', JSON.stringify(orders));
                renderAdminOrders();
            }

            cart = [];
            localStorage.removeItem('STORE_CART');
            updateCartUI();
            closeCheckoutModal();
            showToast(`Order ${orderId} successfully placed!`);
        }

        // Firebase Config Modal Handlers
        function openConfigModal() {
            const saved = JSON.parse(localStorage.getItem('FIREBASE_CONFIG')) || {};
            document.getElementById('cfg-db-url').value = saved.databaseURL || '';
            document.getElementById('cfg-api-key').value = saved.apiKey || '';
            document.getElementById('cfg-project-id').value = saved.projectId || '';
            document.getElementById('config-modal').classList.remove('hidden');
        }

        function closeConfigModal() {
            document.getElementById('config-modal').classList.add('hidden');
        }

        function saveFirebaseConfig(e) {
            e.preventDefault();
            const cfg = {
                databaseURL: document.getElementById('cfg-db-url').value.trim(),
                apiKey: document.getElementById('cfg-api-key').value.trim(),
                projectId: document.getElementById('cfg-project-id').value.trim()
            };

            localStorage.setItem('FIREBASE_CONFIG', JSON.stringify(cfg));
            closeConfigModal();
            initDatabase();
            showToast("Firebase Config Saved!");
        }

        function clearFirebaseConfig() {
            localStorage.removeItem('FIREBASE_CONFIG');
            closeConfigModal();
            initDatabase();
            showToast("Switched to Local Database Mode");
        }

        function toggleGithubGuide(show) {
            document.getElementById('github-modal').classList.toggle('hidden', !show);
        }

        function filterCategory(cat) {
            activeCategory = cat;
            document.querySelectorAll('.cat-btn').forEach(btn => {
                btn.className = btn.innerText.toLowerCase() === cat.toLowerCase()
                    ? "cat-btn active bg-blue-600 text-white text-xs font-bold px-4 py-2 rounded-lg transition"
                    : "cat-btn bg-brand-card hover:bg-gray-800 text-gray-300 text-xs font-bold px-4 py-2 rounded-lg border border-brand-border transition";
            });
            renderStore();
        }

        function filterProducts() {
            renderStore();
        }

        function switchAdminTab(tab) {
            document.getElementById('admin-tab-products').classList.toggle('hidden', tab !== 'products');
            document.getElementById('admin-tab-orders').classList.toggle('hidden', tab !== 'orders');
            document.getElementById('admin-tab-products-btn').className = tab === 'products' ? 'pb-3 text-sm font-bold uppercase border-b-2 border-blue-500 text-white' : 'pb-3 text-sm font-bold uppercase border-b-2 border-transparent text-gray-400 hover:text-white';
            document.getElementById('admin-tab-orders-btn').className = tab === 'orders' ? 'pb-3 text-sm font-bold uppercase border-b-2 border-blue-500 text-white' : 'pb-3 text-sm font-bold uppercase border-b-2 border-transparent text-gray-400 hover:text-white';
        }

        function showToast(msg) {
            const toast = document.getElementById('toast');
            document.getElementById('toast-message').innerText = msg;
            toast.classList.remove('translate-y-20', 'opacity-0');
            setTimeout(() => toast.classList.add('translate-y-20', 'opacity-0'), 3000);
        }
    </script>
</body>
</html>