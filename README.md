# React-Performance-Optimization
App.jsx
import { useState, useMemo, useCallback } from "react";
import ProductList from "./ProductList";

const initialProducts = [
  { id: 1, name: "Laptop", price: 50000 },
  { id: 2, name: "Headphones", price: 2000 },
  { id: 3, name: "Mouse", price: 800 }
];

export default function App() {
  const [products, setProducts] = useState(initialProducts);
  const [count, setCount] = useState(0);

  console.log("App re-rendered");

  // ❌ Without useMemo → runs every render
  // ✅ With useMemo → runs ONLY when products change
  const totalPrice = useMemo(() => {
    console.log("Total price calculated...");
    return products.reduce((total, p) => total + p.price, 0);
  }, [products]);

  // ❌ Without useCallback → new function every render
  // ProductList re-renders unnecessarily
  //
  // ✅ With useCallback → same function reference unless products change
  const handleSelectProduct = useCallback((product) => {
    console.log("Selected:", product.name);
  }, []);

  return (
    <div>
      <h2>React Performance Optimization</h2>

      <h3>Total Price: ₹{totalPrice}</h3>

      <button onClick={() => setCount(count + 1)}>
        Counter: {count}
      </button>

      <ProductList
        products={products}
        onSelect={handleSelectProduct}
      />
    </div>
  );
}

ProductList.jsx
export default function ProductList({ products, onSelect }) {
  console.log("ProductList rendered");

  return (
    <div>
      <h3>Products</h3>

      {products.map((p) => (
        <div key={p.id}>
          <span>
            {p.name} — ₹{p.price}
          </span>
          <button onClick={() => onSelect(p)}>Select</button>
        </div>
      ))}
    </div>
  );
}

🧠 What This Demonstrates
✔ useMemo — avoids expensive recalculations

Without useMemo:

total price recalculates on every render — even when only counter changes.

With useMemo:

useMemo(() => calc, [products])


Runs ONLY when products array changes.

✔ useCallback — prevents function re-creation

Without useCallback:

const handleSelectProduct = (p) => {}


A new function is created each render → ProductList re-renders.

With useCallback:

const handleSelectProduct = useCallback((p) => {}, []);


Same function reference → ProductList does not re-render unnecessarily.
