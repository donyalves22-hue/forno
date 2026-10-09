
import React, { useEffect, useMemo, useState } from "react";

const WHATSAPP = "5583999999999"; // Troque pelo WhatsApp da pizzaria
const ADMIN_PASSWORD = "1234"; // Demonstração: substitua por autenticação segura
const CATEGORIES = ["Pizzas", "Espetinhos", "Refrigerantes", "Porções"];
const STATUSES = [
  "Recebido",
  "Confirmado",
  "Em preparo",
  "Saiu para entrega",
  "Entregue",
  "Cancelado",
];

const INITIAL_PRODUCTS = [
  { id: 1, name: "Pizza Calabresa", category: "Pizzas", price: 39.90, description: "Calabresa, cebola, queijo e orégano.", image: "https://images.unsplash.com/photo-1571407970349-bc81e7e96d47?w=700", available: true },
  { id: 2, name: "Pizza Mussarela", category: "Pizzas", price: 35.90, description: "Mussarela, tomate e orégano.", image: "https://images.unsplash.com/photo-1579751626657-72bc17010498?w=700", available: true },
  { id: 3, name: "Pizza Portuguesa", category: "Pizzas", price: 44.90, description: "Presunto, ovos, cebola, queijo e azeitona.", image: "https://images.unsplash.com/photo-1513104890138-7c749659a591?w=700", available: true },
  { id: 4, name: "Espetinho de Carne", category: "Espetinhos", price: 10, description: "Espetinho assado na hora.", image: "https://images.unsplash.com/photo-1529193591184-b1d58069ecdd?w=700", available: true },
  { id: 5, name: "Espetinho de Frango", category: "Espetinhos", price: 8, description: "Frango temperado e grelhado.", image: "https://images.unsplash.com/photo-1532634896-26909d0d4b0c?w=700", available: true },
  { id: 6, name: "Coca-Cola 2 litros", category: "Refrigerantes", price: 12, description: "Refrigerante gelado de 2 litros.", image: "https://images.unsplash.com/photo-1554866585-cd94860890b7?w=700", available: true },
  { id: 7, name: "Guaraná 2 litros", category: "Refrigerantes", price: 10, description: "Refrigerante gelado de 2 litros.", image: "https://images.unsplash.com/photo-1629203851122-3726ecdf080e?w=700", available: true },
  { id: 8, name: "Batata frita", category: "Porções", price: 18, description: "Porção crocante de batatas.", image: "https://images.unsplash.com/photo-1573080496219-bb080dd4f877?w=700", available: true },
  { id: 9, name: "Calabresa acebolada", category: "Porções", price: 25, description: "Calabresa acebolada para compartilhar.", image: "https://images.unsplash.com/photo-1544025162-d76694265947?w=700", available: true },
];

const money = (n) =>
  Number(n || 0).toLocaleString("pt-BR", {
    style: "currency",
    currency: "BRL",
  });

function load(key, fallback) {
  try {
    const value = localStorage.getItem(key);
    return value ? JSON.parse(value) : fallback;
  } catch {
    return fallback;
  }
}

export default function App() {
  const [products, setProducts] = useState(() =>
    load("forno_products", INITIAL_PRODUCTS)
  );
  const [cart, setCart] = useState([]);
  const [category, setCategory] = useState("Pizzas");
  const [search, setSearch] = useState("");
  const [admin, setAdmin] = useState(false);
  const [deliveryFee, setDeliveryFee] = useState(() =>
    load("forno_delivery", 5)
  );
  const [pixKey, setPixKey] = useState(() =>
    load("forno_pix", "")
  );
  const [orders, setOrders] = useState(() =>
    load("forno_orders", [])
  );
  const [trackingId, setTrackingId] = useState("");
  const [customer, setCustomer] = useState({
    name: "",
    phone: "",
    address: "",
    payment: "Pix",
    notes: "",
  });
  const [view, setView] = useState("menu");

  useEffect(() => {
    localStorage.setItem("forno_products", JSON.stringify(products));
  }, [products]);

  useEffect(() => {
    localStorage.setItem("forno_delivery", JSON.stringify(deliveryFee));
  }, [deliveryFee]);

  useEffect(() => {
    localStorage.setItem("forno_pix", JSON.stringify(pixKey));
  }, [pixKey]);

  useEffect(() => {
    localStorage.setItem("forno_orders", JSON.stringify(orders));
  }, [orders]);

  const visibleProducts = useMemo(
    () =>
      products.filter(
        (p) =>
          p.available &&
          p.category === category &&
          p.name.toLowerCase().includes(search.toLowerCase())
      ),
    [products, category, search]
  );

  const subtotal = cart.reduce(
    (total, item) => total + item.price * item.quantity,
    0
  );
  const total = subtotal + (cart.length ? Number(deliveryFee) : 0);

  function addToCart(product) {
    setCart((old) => {
      const found = old.find((item) => item.id === product.id);
      return found
        ? old.map((item) =>
            item.id === product.id
              ? { ...item, quantity: item.quantity + 1 }
              : item
          )
        : [...old, { ...product, quantity: 1, notes: "" }];
    });
  }

  function changeQuantity(id, amount) {
    setCart((old) =>
      old
        .map((item) =>
          item.id === id
            ? { ...item, quantity: item.quantity + amount }
            : item
        )
        .filter((item) => item.quantity > 0)
    );
  }

  function editProduct(product) {
    const name = prompt("Nome do produto:", product?.name || "");
    if (name === null || !name.trim()) return;

    const categoryChoice = prompt(
      `Categoria (${CATEGORIES.join(", ")}):`,
      product?.category || category
    );
    if (!CATEGORIES.includes(categoryChoice)) {
      alert("Escolha uma categoria válida.");
      return;
    }

    const priceText = prompt(
      "Preço em reais:",
      String(product?.price ?? "0")
    );
    if (priceText === null) return;
    const price = Number(priceText.replace(",", "."));
    if (!Number.isFinite(price) || price < 0) {
      alert("Preço inválido.");
      return;
    }

    const description = prompt(
      "Descrição:",
      product?.description || ""
    );
    if (description === null) return;

    const image = prompt(
      "URL da foto do produto:",
      product?.image || ""
    );
    if (image === null) return;

    if (product) {
      setProducts((old) =>
        old.map((p) =>
          p.id === product.id
            ? { ...p, name: name.trim(), category: categoryChoice,
                price, description, image }
            : p
        )
      );
    } else {
      setProducts((old) => [
        ...old,
        {
          id: Date.now(),
          name: name.trim(),
          category: categoryChoice,
          price,
          description,
          image,
          available: true,
        },
      ]);
    }
  }

  function toggleAvailability(product) {
    setProducts((old) =>
      old.map((p) =>
        p.id === product.id ? { ...p, available: !p.available } : p
      )
    );
  }

  function deleteProduct(product) {
    if (confirm(`Excluir "${product.name}"?`)) {
      setProducts((old) => old.filter((p) => p.id !== product.id));
      setCart((old) => old.filter((p) => p.id !== product.id));
    }
  }

  function openAdmin() {
    const password = prompt("Senha administrativa:");
    if (password === ADMIN_PASSWORD) {
      setAdmin(true);
      setView("admin");
    } else if (password !== null) {
      alert("Senha incorreta.");
    }
  }

  function checkout() {
    if (!cart.length) return alert("Adicione produtos ao carrinho.");
    if (!customer.name.trim() || !customer.phone.trim() ||
        !customer.address.trim()) {
      return alert("Preencha nome, telefone e endereço.");
    }

    const order = {
      id: String(Date.now()).slice(-8),
      customer: { ...customer },
      items: cart.map((item) => ({ ...item })),
      subtotal,
      deliveryFee: Number(deliveryFee),
      total,
      status: "Recebido",
      createdAt: new Date().toLocaleString("pt-BR"),
    };

    const lines = order.items
      .map(
        (item) =>
          `• ${item.quantity}x ${item.name} — ${money(item.price * item.quantity)}`
      )
      .join("\n");

    const message = [
      "*NOVO PEDIDO — FORNO*",
      `Pedido: #${order.id}`,
      `Cliente: ${customer.name}`,
      `Telefone: ${customer.phone}`,
      `Endereço: ${customer.address}`,
      "",
      "*Itens:*",
      lines,
      "",
      `Subtotal: ${money(subtotal)}`,
      `Entrega: ${money(deliveryFee)}`,
      `*TOTAL: ${money(total)}*`,
      `Pagamento: ${customer.payment}`,
      customer.notes ? `Observações: ${customer.notes}` : "",
      customer.payment === "Pix" && pixKey
        ? `Chave Pix: ${pixKey}`
        : "",
      "",
      `Acompanhe seu pedido usando o código #${order.id}.`,
    ]
      .filter(Boolean)
      .join("\n");

    setOrders((old) => [order, ...old]);
    setTrackingId(order.id);
    setCart([]);
    setView("tracking");

    window.open(
      `https://wa.me/${WHATSAPP}?text=${encodeURIComponent(message)}`,
      "_blank"
    );
  }

  function updateStatus(orderId, status) {
    setOrders((old) =>
      old.map((o) => (o.id === orderId ? { ...o, status } : o))
    );
  }

  function configureDelivery() {
    const value = prompt("Taxa de entrega em reais:", String(deliveryFee));
    if (value === null) return;
    const amount = Number(value.replace(",", "."));
    if (!Number.isFinite(amount) || amount < 0) {
      return alert("Informe uma taxa válida.");
    }
    setDeliveryFee(amount);
  }

  function configurePix() {
    const value = prompt("Chave Pix da pizzaria:", pixKey);
    if (value !== null) setPixKey(value.trim());
  }

  const trackedOrder = orders.find(
    (o) => o.id === trackingId.trim().replace(/^#/, "")
  );

  return (
    <div className="app">
      <style>{`
        * { box-sizing: border-box; }
        body { margin: 0; background: #101010; color: #f8f5ef;
          font-family: Arial, sans-serif; }
        button, input, select, textarea { font: inherit; }
        button { cursor: pointer; }
        .app { min-height: 100vh; }
        .top { background: #171717; padding: 20px 5%;
          display: flex; justify-content: space-between; align-items: center;
          gap: 12px; border-bottom: 1px solid #333; flex-wrap: wrap; }
        .logo { font-size: 30px; font-weight: 900; color: #f5a623; }
        .sub { color: #bbb; font-size: 13px; margin-top: 5px; }
        .actions { display: flex; gap: 8px; flex-wrap: wrap; }
        .btn { border: 0; padding: 11px 15px; border-radius: 9px;
          background: #f5a623; color: #171717; font-weight: 700; }
        .btn.secondary { background: #292929; color: white;
          border: 1px solid #444; }
        .hero { padding: 44px 5%; background:
          linear-gradient(90deg, #19130c, #2a1b0b);
          border-bottom: 1px solid #382919; }
        .hero h1 { font-size: clamp(28px, 5vw, 48px); margin: 0 0 12px; }
        .hero p { color: #d2c6b7; max-width: 600px; line-height: 1.6; }
        .container { max-width: 1250px; padding: 28px 18px;
          margin: auto; }
        .tabs { display: flex; gap: 10px; overflow-x: auto;
          padding-bottom: 18px; }
        .tab { white-space: nowrap; background: #202020; color: #ddd;
          border: 1px solid #393939; padding: 12px 17px; border-radius: 25px; }
        .tab.active { background: #f5a623; color: #161616;
          border-color: #f5a623; font-weight: 800; }
        .search { width: 100%; max-width: 430px; background: #202020;
          border: 1px solid #444; color: white; border-radius: 9px;
          padding: 13px; margin-bottom: 22px; }
        .layout { display: grid; grid-template-columns: minmax(0, 1fr) 330px;
          gap: 24px; align-items: start; }
        .grid { display: grid; grid-template-columns:
          repeat(auto-fill,minmax(210px,1fr)); gap: 16px; }
        .product { background: #1b1b1b; border: 1px solid #333;
          border-radius: 14px; overflow: hidden; }
        .product img { width: 100%; height: 165px; object-fit: cover;
          background: #292929; }
        .product-body { padding: 15px; }
        .product h3 { margin: 0 0 8px; font-size: 18px; }
        .desc { color: #aaa; font-size: 13px; line-height: 1.5;
          min-height: 39px; }
        .price { color: #f5a623; font-weight: 900; font-size: 21px;
          margin: 14px 0; }
        .cart, .panel { background: #1b1b1b; border: 1px solid #333;
          border-radius: 14px; padding: 18px; }
        .cart { position: sticky; top: 15px; }
        .cart h2, .panel h2 { margin-top: 0; }
        .cart-item { border-bottom: 1px solid #383838; padding: 12px 0; }
        .qty { display: flex; align-items: center; gap: 12px;
          margin-top: 9px; }
        .qty button { background: #333; color: white; border: 0;
          border-radius: 6px; padding: 5px 11px; }
        .field { display: block; width: 100%; margin: 10px 0;
          padding: 12px; color: white; background: #252525;
          border: 1px solid #444; border-radius: 8px; }
        .total { display: flex; justify-content: space-between;
          padding: 9px 0; color: #ccc; }
        .grand { color: #f5a623; font-weight: 900; font-size: 22px; }
        .wide { width: 100%; margin-top: 12px; }
        .notice { color: #aaa; font-size: 12px; line-height: 1.5; }
        .order { border: 1px solid #393939; border-radius: 10px;
          padding: 15px; margin: 12px 0; overflow-wrap: anywhere; }
        .admin-row { display: flex; gap: 10px; flex-wrap: wrap;
          align-items: center; justify-content: space-between; }
        .admin-row select { background: #252525; color: white;
          border: 1px solid #444; border-radius: 7px; padding: 9px; }
        .footer { padding: 30px; text-align: center; color: #888;
          border-top: 1px solid #333; margin-top: 35px; }
        @media(max-width: 850px) {
          .layout { grid-template-columns: 1fr; }
          .cart { position: static; }
        }
        @media(max-width: 480px) {
          .top { padding: 16px; }
          .logo { font-size: 25px; }
          .hero { padding: 30px 18px; }
          .grid { grid-template-columns: repeat(2,minmax(0,1fr)); gap: 10px; }
          .product img { height: 120px; }
          .product-body { padding: 10px; }
          .product h3 { font-size: 15px; }
          .price { font-size: 17px; }
          .product .btn { width: 100%; padding: 10px 5px; }
        }
      `}</style>

      <header className="top">
        <div>
          <div className="logo">🔥 FORNO</div>
          <div className="sub">Pizzas, espetinhos e muito sabor</div>
        </div>
        <div className="actions">
          <button className="btn secondary" onClick={() => setView("menu")}>
            Cardápio
          </button>
          <button className="btn secondary" onClick={() => setView("tracking")}>
            Acompanhar pedido
          </button>
          <button className="btn" onClick={openAdmin}>
            ⚙ Administração
          </button>
        </div>
      </header>

      <section className="hero">
        <h1>Seu pedido favorito, feito na hora.</h1>
        <p>
          Escolha suas pizzas, espetinhos, bebidas e porções.
          Monte seu pedido e envie diretamente para nosso WhatsApp.
        </p>
        <button className="btn" onClick={() => setView("menu")}>
          Ver cardápio ↓
        </button>
      </section>

      <main className="container">
        {view === "menu" && (
          <>
            <h2>Nosso cardápio</h2>
            <div className="tabs">
              {CATEGORIES.map((c) => (
                <button
                  key={c}
                  className={`tab ${category === c ? "active" : ""}`}
                  onClick={() => setCategory(c)}
                >
                  {c === "Pizzas" ? "🍕" : c === "Espetinhos" ? "🍢" :
                    c === "Refrigerantes" ? "🥤" : "🍟"} {c}
                </button>
              ))}
            </div>
            <input
              className="search"
              placeholder="Buscar produto..."
              value={search}
              onChange={(e) => setSearch(e.target.value)}
            />
            <div className="layout">
              <section className="grid">
                {visibleProducts.map((p) => (
                  <article className="product" key={p.id}>
                    <img
                      src={p.image}
                      alt={p.name}
                      onError={(e) => {
                        e.currentTarget.style.visibility = "hidden";
                      }}
                    />
                    <div className="product-body">
                      <h3>{p.name}</h3>
                      <div className="desc">{p.description}</div>
                      <div className="price">{money(p.price)}</div>
                      <button className="btn wide" onClick={() => addToCart(p)}>
                        + Adicionar
                      </button>
                    </div>
                  </article>
                ))}
                {!visibleProducts.length && (
                  <p>Nenhum produto disponível nesta categoria.</p>
                )}
              </section>

              <aside className="cart">
                <h2>🛒 Seu pedido ({cart.reduce((s, i) => s + i.quantity, 0)})</h2>
                {!cart.length && <p className="notice">Seu carrinho está vazio.</p>}
                {cart.map((item) => (
                  <div className="cart-item" key={item.id}>
                    <strong>{item.name}</strong>
                    <div className="notice">{money(item.price)} cada</div>
                    <div className="qty">
                      <button onClick={() => changeQuantity(item.id, -1)}>−</button>
                      <span>{item.quantity}</span>
                      <button onClick={() => changeQuantity(item.id, 1)}>+</button>
                      <span>{money(item.price * item.quantity)}</span>
                    </div>
                  </div>
                ))}

                <h3>Seus dados</h3>
                <input className="field" placeholder="Nome completo"
                  value={customer.name}
                  onChange={(e) => setCustomer({ ...customer, name: e.target.value })} />
                <input className="field" placeholder="Telefone com DDD"
                  value={customer.phone}
                  onChange={(e) => setCustomer({ ...customer, phone: e.target.value })} />
                <textarea className="field" placeholder="Endereço completo e ponto de referência"
                  value={customer.address}
                  onChange={(e) => setCustomer({ ...customer, address: e.target.value })} />
                <select className="field" value={customer.payment}
                  onChange={(e) => setCustomer({ ...customer, payment: e.target.value })}>
                  <option>Pix</option>
                  <option>Dinheiro</option>
                  <option>Cartão na entrega</option>
                </select>
                <textarea className="field" placeholder="Observações do pedido"
                  value={customer.notes}
                  onChange={(e) => setCustomer({ ...customer, notes: e.target.value })} />

                <div className="total"><span>Subtotal</span><span>{money(subtotal)}</span></div>
                <div className="total"><span>Entrega</span><span>{money(cart.length ? deliveryFee : 0)}</span></div>
                <div className="total grand"><span>Total</span><span>{money(total)}</span></div>
                {customer.payment === "Pix" && pixKey && (
                  <p className="notice">Chave Pix: <strong>{pixKey}</strong></p>
                )}
                <button className="btn wide" onClick={checkout}>
                  Enviar pedido pelo WhatsApp
                </button>
                <p className="notice">
                  O pedido será registrado neste navegador e enviado ao WhatsApp
                  para confirmação pela pizzaria.
                </p>
              </aside>
            </div>
          </>
        )}

        {view === "tracking" && (
          <section className="panel">
            <h2>📦 Acompanhe seu pedido</h2>
            <p className="notice">
              Digite o código que apareceu depois de finalizar seu pedido.
            </p>
            <input className="field" placeholder="Código do pedido"
              value={trackingId}
              onChange={(e) => setTrackingId(e.target.value.replace(/^#/, ""))} />
            <button className="btn" onClick={() => setTrackingId(trackingId.trim())}>
              Consultar pedido
            </button>
            {trackedOrder ? (
              <div className="order">
                <h3>Pedido #{trackedOrder.id}</h3>
                <p>Cliente: {trackedOrder.customer.name}</p>
                <p>Realizado em: {trackedOrder.createdAt}</p>
                <p>Total: <strong>{money(trackedOrder.total)}</strong></p>
                <p>Status atual: <strong>{trackedOrder.status}</strong></p>
                <div className="tabs">
                  {STATUSES.map((s, i) => (
                    <span key={s} className="tab"
                      style={{
                        background: STATUSES.indexOf(trackedOrder.status) >= i &&
                          trackedOrder.status !== "Cancelado" ? "#356a43" : "#252525",
                        fontSize: 12,
                      }}>
                      {s}
                    </span>
                  ))}
                </div>
              </div>
            ) : (
              trackingId && <p>Pedido não encontrado neste navegador.</p>
            )}
          </section>
        )}

        {view === "admin" && admin && (
          <section className="panel">
            <div className="admin-row">
              <h2>⚙ Painel administrativo</h2>
              <button className="btn secondary" onClick={() => {
                setAdmin(false);
                setView("menu");
              }}>Sair</button>
            </div>

            <div className="actions">
              <button className="btn" onClick={() => editProduct(null)}>
                + Adicionar produto
              </button>
              <button className="btn secondary" onClick={configureDelivery}>
                Taxa de entrega: {money(deliveryFee)}
              </button>
              <button className="btn secondary" onClick={configurePix}>
                Configurar chave Pix
              </button>
            </div>

            <h3>Gerenciar produtos</h3>
            {products.map((p) => (
              <div className="order admin-row" key={p.id}>
                <div>
                  <strong>{p.name}</strong>
                  <p className="notice">{p.category} · {money(p.price)}</p>
                  <span className="notice">
                    {p.available ? "Disponível" : "Indisponível"}
                  </span>
                </div>
                <div className="actions">
                  <button className="btn secondary" onClick={() => editProduct(p)}>
                    Editar
                  </button>
                  <button className="btn secondary" onClick={() => toggleAvailability(p)}>
                    {p.available ? "Pausar" : "Ativar"}
                  </button>
                  <button className="btn secondary" onClick={() => deleteProduct(p)}>
                    Excluir
                  </button>
                </div>
              </div>
            ))}

            <h3>📋 Pedidos recebidos neste navegador</h3>
            {!orders.length && <p className="notice">Nenhum pedido registrado.</p>}
            {orders.map((o) => (
              <div className="order" key={o.id}>
                <div className="admin-row">
                  <h3>Pedido #{o.id}</h3>
                  <strong>{money(o.total)}</strong>
                </div>
                <p>Cliente: {o.customer.name} · {o.customer.phone}</p>
                <p>Endereço: {o.customer.address}</p>
                <p>Pagamento: {o.customer.payment}</p>
                <p className="notice">{o.createdAt}</p>
                <ul>
                  {o.items.map((item) => (
                    <li key={item.id}>
                      {item.quantity}x {item.name} — {money(item.price * item.quantity)}
                    </li>
                  ))}
                </ul>
                <label htmlFor={`status-${o.id}`}>Atualizar status:</label>
                <select id={`status-${o.id}`} className="field"
                  value={o.status}
                  onChange={(e) => updateStatus(o.id, e.target.value)}>
                  {STATUSES.map((s) => <option key={s}>{s}</option>)}
                </select>
                <button className="btn secondary" onClick={() => {
                  const msg = `Olá ${o.customer.name}, seu pedido #${o.id} está: ${o.status}.`;
                  window.open(`https://wa.me/${o.customer.phone.replace(/\D/g, "")}?text=${encodeURIComponent(msg)}`, "_blank");
                }}>
                  Avisar cliente pelo WhatsApp
                </button>
              </div>
            ))}
            <p className="notice">
              Importante: esta versão salva os dados apenas no navegador atual.
              Não utilize a senha de demonstração em produção.
            </p>
          </section>
        )}
      </main>

      <footer className="footer">
        <strong>🔥 FORNO</strong>
        <p>Feito com sabor. Seu pedido, do nosso forno até você.</p>
      </footer>
    </div>
  );
}
