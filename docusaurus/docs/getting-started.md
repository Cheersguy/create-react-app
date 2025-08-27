import React, { useState } from "react";

// ClothingStore.jsx
// Single-file React component (Tailwind CSS classes used)
// Usage: drop into a React app (Vite / CRA / Next). Ensure Tailwind is configured or replace classes with your own CSS.

export default function ClothingStore() {
  const [products] = useState([
    { id: 1, name: "Classic White Tee", price: 499, size: ["S", "M", "L", "XL"], img: "https://via.placeholder.com/300x300?text=White+Tee" },
    { id: 2, name: "Denim Jacket", price: 1999, size: ["M", "L", "XL"], img: "https://via.placeholder.com/300x300?text=Denim+Jacket" },
    { id: 3, name: "Running Shorts", price: 799, size: ["S", "M", "L"], img: "https://via.placeholder.com/300x300?text=Shorts" },
    { id: 4, name: "Hooded Sweatshirt", price: 1499, size: ["M", "L", "XL"], img: "https://via.placeholder.com/300x300?text=Hoodie" },
  ]);

  const [cart, setCart] = useState([]);
  const [selectedSize, setSelectedSize] = useState({});

  function addToCart(product) {
    const size = selectedSize[product.id] || product.size[0];
    setCart((prev) => {
      const existing = prev.find((p) => p.id === product.id && p.size === size);
      if (existing) {
        return prev.map((p) => (p === existing ? { ...p, qty: p.qty + 1 } : p));
      }
      return [...prev, { ...product, qty: 1, size }];
    });
  }

  function changeQty(item, delta) {
    setCart((prev) =>
      prev
        .map((p) => (p.id === item.id && p.size === item.size ? { ...p, qty: Math.max(1, p.qty + delta) } : p))
        .filter((p) => p.qty > 0)
    );
  }

  function removeItem(item) {
    setCart((prev) => prev.filter((p) => !(p.id === item.id && p.size === item.size)));
  }

  const subtotal = cart.reduce((s, c) => s + c.price * c.qty, 0);
  const shipping = subtotal > 2000 || subtotal === 0 ? 0 : 99;
  const total = subtotal + shipping;

  return (
    <div className="min-h-screen bg-gray-50 p-6">
      <header className="max-w-6xl mx-auto flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold">Bajarangi Clothing — Online Store</h1>
        <div className="flex items-center gap-4">
          <div className="text-sm text-gray-600">Free returns • Easy checkout</div>
          <div className="bg-white p-2 rounded shadow">Cart: {cart.length} item(s)</div>
        </div>
      </header>

      <main className="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-3 gap-6">
        <section className="md:col-span-2">
          <div className="grid grid-cols-1 sm:grid-cols-2 gap-6">
            {products.map((p) => (
              <article key={p.id} className="bg-white rounded-2xl shadow p-4 flex flex-col">
                <img src={p.img} alt={p.name} className="w-full h-56 object-cover rounded-lg mb-4" />
                <div className="flex justify-between items-start">
                  <div>
                    <h2 className="font-semibold">{p.name}</h2>
                    <div className="text-sm text-gray-500">₹{p.price.toLocaleString()}</div>
                  </div>
                  <div>
                    <label className="text-xs text-gray-500 block">Size</label>
                    <select
                      value={selectedSize[p.id] || p.size[0]}
                      onChange={(e) => setSelectedSize((s) => ({ ...s, [p.id]: e.target.value }))}
                      className="mt-1 rounded border px-2 py-1"
                    >
                      {p.size.map((sz) => (
                        <option key={sz} value={sz}>
                          {sz}
                        </option>
                      ))}
                    </select>
                  </div>
                </div>

                <div className="mt-4 flex gap-2">
                  <button className="flex-1 rounded-lg px-4 py-2 bg-blue-600 text-white font-medium" onClick={() => addToCart(p)}>
                    Add to cart
                  </button>
                  <button className="rounded-lg px-4 py-2 border">Quick view</button>
                </div>
              </article>
            ))}
          </div>
        </section>

        <aside className="bg-white rounded-2xl shadow p-4">
          <h3 className="font-semibold mb-4">Your Cart</h3>
          {cart.length === 0 ? (
            <div className="text-sm text-gray-500">Cart is empty — add some items!</div>
          ) : (
            <div className="space-y-3">
              {cart.map((item) => (
                <div key={`${item.id}-${item.size}`} className="flex items-center justify-between">
                  <div>
                    <div className="font-medium">{item.name}</div>
                    <div className="text-xs text-gray-500">Size: {item.size}</div>
                    <div className="text-sm">₹{item.price.toLocaleString()}</div>
                  </div>
                  <div className="flex items-center gap-2">
                    <button className="px-2" onClick={() => changeQty(item, -1)}>-</button>
                    <div className="w-6 text-center">{item.qty}</div>
                    <button className="px-2" onClick={() => changeQty(item, 1)}>+</button>
                    <button className="ml-2 text-red-500 text-sm" onClick={() => removeItem(item)}>Remove</button>
                  </div>
                </div>
              ))}

              <div className="border-t pt-3">
                <div className="flex justify-between text-sm text-gray-600">
                  <div>Subtotal</div>
                  <div>₹{subtotal.toLocaleString()}</div>
                </div>
                <div className="flex justify-between text-sm text-gray-600">
                  <div>Shipping</div>
                  <div>{shipping === 0 ? "Free" : `₹${shipping}`}</div>
                </div>
                <div className="flex justify-between font-semibold mt-2">
                  <div>Total</div>
                  <div>₹{total.toLocaleString()}</div>
                </div>

                <button
                  className="w-full mt-4 rounded-lg px-4 py-2 bg-green-600 text-white font-medium"
                  onClick={() => alert('Checkout flow placeholder — integrate payment gateway (Razorpay/Stripe)')}
                >
                  Proceed to Checkout
                </button>
              </div>
            </div>
          )}
        </aside>
      </main>

      <footer className="max-w-6xl mx-auto mt-8 text-xs text-gray-500">© {new Date().getFullYear()} Bajarangi Clothing</footer>
    </div>
  );
}
