# A-LA-VUELTA
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>A LA VUELTA - Marketplace</title>
<script src="https://cdn.tailwindcss.com"></script>
<style>
  /* Fondo degradado animado */
  body {
    background: linear-gradient(120deg, #f6d365, #fda085);
    animation: gradient 10s ease infinite;
  }
  @keyframes gradient {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }

  /* Pop-up animado */
  #popup {
    position: fixed;
    top: 20px;
    right: 20px;
    background: #22c55e;
    color: white;
    padding: 10px 20px;
    border-radius: 8px;
    opacity: 0;
    transform: translateY(-20px);
    transition: all 0.3s ease-in-out;
    z-index: 50;
  }
  #popup.show {
    opacity: 1;
    transform: translateY(0);
  }

  /* Tarjetas productos */
  .product-card {
    transition: transform 0.3s, box-shadow 0.3s, opacity 0.5s;
    opacity: 0;
    transform: translateY(20px);
  }
  .product-card.show {
    opacity: 1;
    transform: translateY(0);
  }
  .product-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 12px 25px rgba(0,0,0,0.2);
  }

  /* Scroll suave */
  html {
    scroll-behavior: smooth;
  }
</style>
</head>
<body class="font-sans min-h-screen">

<!-- Header -->
<header class="bg-gray-900 text-white p-4 flex justify-between items-center shadow-lg">
  <h1 class="text-3xl font-bold">A LA VUELTA</h1>
  <nav class="space-x-4 text-lg">
    <a href="#" class="hover:text-gray-300">Inicio</a>
    <a href="#" class="hover:text-gray-300">Categorías</a>
    <span>🛒 <span id="cart-count">0</span></span>
  </nav>
</header>

<!-- Main Container -->
<div class="container mx-auto p-6 flex flex-col md:flex-row gap-6">

  <!-- Products Section -->
  <div class="flex-1">
    <div class="flex gap-2 mb-4 flex-wrap">
      <button onclick="filterCategory('all')" class="px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800">Todos</button>
      <button onclick="filterCategory('ropa')" class="px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800">Ropa</button>
      <button onclick="filterCategory('electronica')" class="px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800">Electrónica</button>
      <button onclick="filterCategory('hogar')" class="px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800">Hogar</button>
    </div>
    <div id="products" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6"></div>
  </div>

  <!-- Cart -->
  <div class="w-full md:w-80 bg-white rounded shadow-lg p-4 sticky top-6 h-fit">
    <h2 class="text-xl font-bold mb-4">🛒 Carrito</h2>
    <div id="cart-items" class="space-y-2"></div>
    <button onclick="checkout()" class="mt-4 w-full bg-blue-600 text-white py-2 rounded hover:bg-blue-700 transition">Pagar ahora</button>
  </div>

</div>

<!-- Pop-up -->
<div id="popup">¡Agregado al carrito! ✅</div>

<script>
const products = [
  { id:1, name:"Camisa Blanca", price:15, category:"ropa", img:"https://images.unsplash.com/photo-1585386959984-a4155222b1b3?crop=entropy&cs=tinysrgb&fit=max&h=200&w=300" },
  { id:2, name:"Laptop Dell", price:500, category:"electronica", img:"https://images.unsplash.com/photo-1587825140502-9d44a05c7a06?crop=entropy&cs=tinysrgb&fit=max&h=200&w=300" },
  { id:3, name:"Cafetera", price:40, category:"hogar", img:"https://images.unsplash.com/photo-1600020437088-8c9d1c7c07b4?crop=entropy&cs=tinysrgb&fit=max&h=200&w=300" },
  { id:4, name:"Zapatillas", price:30, category:"ropa", img:"https://images.unsplash.com/photo-1528701800484-21d7200eeaaf?crop=entropy&cs=tinysrgb&fit=max&h=200&w=300" },
  { id:5, name:"Auriculares", price:25, category:"electronica", img:"https://images.unsplash.com/photo-1580894894513-4a7f0d8ebef3?crop=entropy&cs=tinysrgb&fit=max&h=200&w=300" }
];

let cart = [];

function renderProducts(list) {
  const container = document.getElementById("products");
  container.innerHTML = "";
  list.forEach((p, i) => {
    const div = document.createElement("div");
    div.className = "product-card bg-white rounded shadow p-4 flex flex-col items-center";
    div.innerHTML = `
      <img src="${p.img}" alt="${p.name}" class="w-full h-40 object-cover rounded mb-2">
      <h3 class="text-lg font-semibold">${p.name}</h3>
      <p class="text-gray-700 font-medium">$${p.price}</p>
      <button onclick="addToCart(${p.id})" class="mt-2 bg-green-500 text-white px-4 py-1 rounded hover:bg-green-600 transition">Agregar</button>
    `;
    container.appendChild(div);
    setTimeout(()=> div.classList.add("show"), i*100); // animación escalonada
  });
}

function filterCategory(cat) {
  if(cat==="all") renderProducts(products);
  else renderProducts(products.filter(p=>p.category===cat));
}

function addToCart(id) {
  const product = products.find(p=>p.id===id);
  cart.push(product);
  renderCart();
  showPopup();
}

function renderCart() {
  const container = document.getElementById("cart-items");
  container.innerHTML = "";
  cart.forEach(item => {
    const div = document.createElement("div");
    div.className = "border-b py-2";
    div.textContent = `${item.name} - $${item.price}`;
    container.appendChild(div);
  });
  document.getElementById("cart-count").textContent = cart.length;
}

function checkout() {
  if(cart.length===0){ alert("Tu carrito está vacío 😅"); return; }
  alert("Gracias por tu compra (simulada) 😎");
  cart = [];
  renderCart();
}

function showPopup() {
  const popup = document.getElementById("popup");
  popup.classList.add("show");
  setTimeout(()=> popup.classList.remove("show"), 1500);
}

renderProducts(products);
</script>

</body>
</html>
