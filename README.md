<!DOCTYPE html>

<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="theme-color" content="#151515">
  <meta name="description" content="FORNO — Pizzas artesanais, sabores irresistíveis e pedidos online.">
  <title>FORNO | Pizzaria</title>
  <style>
    *{box-sizing:border-box;margin:0;padding:0}
    :root{--bg:#111;--card:#1d1d1d;--red:#e63932;--gold:#f4bd60;--white:#fff;--muted:#b5b5b5}
    body{font-family:Arial,Helvetica,sans-serif;background:var(--bg);color:var(--white);line-height:1.5}
    button,input,select,textarea{font:inherit}
    button{cursor:pointer}
    header{background:#171717;border-bottom:1px solid #333;padding:18px 6%;display:flex;align-items:center;justify-content:space-between;gap:16px;position:sticky;top:0;z-index:5}
    .logo{font-size:30px;font-weight:900;letter-spacing:4px;color:var(--gold)}
    .logo span{color:var(--red)}
    .cart-button,.primary{border:0;background:var(--red);color:white;padding:12px 18px;border-radius:9px;font-weight:bold}
    .hero{padding:75px 6%;background:linear-gradient(90deg,#111 10%,#111d 60%,#1118),url('https://images.unsplash.com/photo-1513104890138-7c749659a591?auto=format&fit=crop&w=1800&q=85') center/cover;min-height:390px;display:flex;align-items:center}
    .hero-content{max-width:600px}
    .eyebrow{color:var(--gold);font-weight:bold;text-transform:uppercase;letter-spacing:3px;font-size:13px}
    h1{font-size:clamp(40px,7vw,70px);line-height:1.05;margin:15px 0}
    h1 span{color:var(--red)}
    .hero p{color:#ddd;max-width:480px;margin-bottom:24px;font-size:17px}
    .section{padding:45px 6%}
    .section-title{font-size:30px;margin-bottom:8px}
    .subtext{color:var(--muted);margin-bottom:22px}
    .toolbar{display:flex;flex-wrap:wrap;gap:12px;margin-bottom:25px}
    .search{flex:1;min-width:220px;background:#202020;color:white;border:1px solid #444;border-radius:9px;padding:13px}
    .filters{display:flex;gap:9px;flex-wrap:wrap;margin-bottom:25px}
    .filter{background:#222;color:#ddd;border:1px solid #444;padding:9px 15px;border-radius:30px}
    .filter.active,.filter:hover{background:var(--gold);color:#171717;border-color:var(--gold)}
    .grid{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:20px}
    .product{background:var(--card);border:1px solid #303030;border-radius:14px;overflow:hidden;display:flex;flex-direction:column}
    .product img{width:100%;height:190px;object-fit:cover;background:#292929}
    .product-body{padding:17px;display:flex;flex-direction:column;flex:1}
    .tag{font-size:11px;color:var(--gold);text-transform:uppercase;letter-spacing:1px;font-weight:bold}
    .product h3{font-size:20px;margin:5px 0}
    .product p{font-size:14px;color:var(--muted);flex:1;margin-bottom:17px}
    .product-bottom{display:flex;justify-content:space-between;align-items:center;gap:8px}
    .price{font-size:20px;font-weight:bold;color:var(--gold)}
    .add{background:var(--red);color:#fff;border:0;border-radius:8px;padding:10px 13px;font-weight:bold}
    .empty{color:var(--muted);padding:25px 0}
    .info{background:#1b1b1b;border-block:1px solid #333;padding:35px 6%;display:grid;grid-template-columns:repeat(3,1fr);gap:22px}
    .info h3{color:var(--gold);margin-bottom:6px}
    .info p{color:var(--muted);font-size:14px}
    footer{text-align:center;padding:28px 15px;color:#999;font-size:13px}
    .modal{display:none;position:fixed;inset:0;background:#000c;z-index:10;padding:20px;overflow:auto}
    .modal.open{display:flex;align-items:flex-start;justify-content:center}
    .modal-content{background:#1b1b1b;border:1px solid #444;border-radius:14px;padding:24px;width:100%;max-width:560px;margin:auto}
    .modal-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:18px;gap:10px}
    .close{background:#333;color:#fff;border:0;border-radius:8px;padding:8px 12px}
    .cart-line{display:flex;justify-content:space-between;gap:10px;padding:12px 0;border-bottom:1px solid #383838}
    .qty{display:flex;align-items:center;gap:9px}
    .qty button{background:#333;color:#fff;border:0;border-radius:5px;width:27px;height:27px}
    .total{display:flex;justify-content:space-between;margin:20px 0;font-size:21px;font-weight:bold}
    .form-field{display:flex;flex-direction:column;gap:6px;margin:13px 0}
    .form-field label{font-size:14px;color:#ddd}
    .form-field input,.form-field select,.form-field textarea{width:100%;padding:12px;background:#111;color:white;border:1px solid #444;border-radius:8px}
    .full{width:100%;margin-top:10px}
    .notice{font-size:12px;color:#aaa;margin-top:12px}
    @media(max-width:850px){.grid{grid-template-columns:repeat(2,minmax(0,1fr))}}
    @media(max-width:560px){header{padding:14px 5%}.logo{font-size:25px}.hero{padding:55px 5%;min-height:350px}.section{padding:35px 5%}.grid{grid-template-columns:1fr}.product img{height:220px}.info{grid-template-columns:1fr;padding:30px 5%}.section-title{font-size:26px}}
  </style>
</head>
<body>
  <header>
    <div class="logo">FOR<span>NO</span>.</div>
    <button class="cart-button" onclick="openCart()">🛒 Sacola (<span id="cart-count">0</span>)</button>
  </header>

  <section class="hero">
    <div class="hero-content">
      <div class="eyebrow">Feito com paixão, servido quentinho</div>
      <h1>Sabor que sai<br>do <span>FORNO.</span></h1>
      <p>Pizzas deliciosas, ingredientes selecionados e aquele sabor especial para compartilhar com quem você gosta.</p>
      <button class="primary" onclick="document.getElementById('cardapio').scrollIntoView({behavior:'smooth'})">Ver cardápio ↓</button>
    </div>
  </section>

  <main>
    <section class="section" id="cardapio">
      <h2 class="section-title">Nosso cardápio</h2>
      <p class="subtext">Escolha seus sabores favoritos e monte seu pedido.</p>
      <div class="toolbar">
        <input class="search" id="search" type="search" placeholder="🔎 Buscar pizza ou bebida..." oninput="renderProducts()">
      </div>
      <div class="filters" id="filters">
        <button class="filter active" data-category="Todas">Tudo</button>
        <button class="filter" data-category="Salgadas">Pizzas salgadas</button>
        <button class="filter" data-category="Doces">Pizzas doces</button>
        <button class="filter" data-category="Bebidas">Bebidas</button>
      </div>
      <div class="grid" id="products"></div>
      <p class="empty" id="empty" hidden>Nenhum produto encontrado.</p>
    </section>

```
<section class="info">
  <div><h3>🍕 Ingredientes selecionados</h3><p>Sabores preparados com cuidado para você.</p></div>
  <div><h3>🛵 Peça sem complicação</h3><p>Monte sua sacola e envie seu pedido pelo WhatsApp.</p></div>
  <div><h3>❤️ Feito para compartilhar</h3><p>Uma boa pizza deixa qualquer momento melhor.</p></div>
</section>
```

  </main>

  <footer>© <span id="year"></span> FORNO Pizzaria. Todos os direitos reservados.</footer>

  <div class="modal" id="cart-modal" role="dialog" aria-modal="true" aria-labelledby="cart-title">
    <div class="modal-content">
      <div class="modal-head"><h2 id="cart-title">Sua sacola</h2><button class="close" onclick="closeCart()">Fechar ✕</button></div>
      <div id="cart-items"></div>
      <div class="total"><span>Total</span><span id="cart-total">R$ 0,00</span></div>
      <form id="order-form">
        <div class="form-field"><label for="customer">Seu nome</label><input id="customer" required placeholder="Como podemos te chamar?"></div>
        <div class="form-field"><label for="phone">Telefone para contato</label><input id="phone" required type="tel" placeholder="(DDD) 99999-9999"></div>
        <div class="form-field"><label for="delivery">Como deseja receber?</label><select id="delivery"><option value="Entrega">Entrega</option><option value="Retirada no balcão">Retirada no balcão</option></select></div>
        <div class="form-field" id="address-field"><label for="address">Endereço de entrega</label><input id="address" placeholder="Rua, número e bairro"></div>
        <div class="form-field"><label for="payment">Forma de pagamento</label><select id="payment"><option>Pix</option><option>Dinheiro</option><option>Cartão de crédito</option><option>Cartão de débito</option></select></div>
        <div class="form-field"><label for="notes">Observações (opcional)</label><textarea id="notes" rows="2" placeholder="Ex.: tirar cebola, troco para..."></textarea></div>
        <button class="primary full" type="submit">Enviar pedido pelo WhatsApp</button>
        <p class="notice">Os preços deste exemplo precisam ser confirmados pela pizzaria. Configure o número do WhatsApp antes de receber pedidos.</p>
      </form>
    </div>
  </div>

  <script>
    // PERSONALIZE AQUI: use o WhatsApp comercial com código do país e DDD, somente números.
    const WHATSAPP_NUMBER = "";
    const DELIVERY_FEE = 0;

    const products = [
      {id:1,name:"Calabresa",category:"Salgadas",description:"Muçarela, calabresa fatiada, cebola e orégano.",price:39.90,image:"https://images.unsplash.com/photo-1628840042765-356cda07504e?auto=format&fit=crop&w=700&q=80"},
      {id:2,name:"Muçarela",category:"Salgadas",description:"Molho de tomate, muçarela derretida e orégano.",price:36.90,image:"https://images.unsplash.com/photo-1579751626657-72bc17010498?auto=format&fit=crop&w=700&q=80"},
      {id:3,name:"Frango com Catupiry",category:"Salgadas",description:"Frango temperado, catupiry e muçarela.",price:44.90,image:"https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?auto=format&fit=crop&w=700&q=80"},
      {id:4,name:"Portuguesa",category:"Salgadas",description:"Presunto, ovo, cebola, azeitona e muçarela.",price:42.90,image:"https://images.unsplash.com/photo-1513104890138-7c749659a591?auto=format&fit=crop&w=700&q=80"},
      {id:5,name:"Chocolate",category:"Doces",description:"Chocolate cremoso com uma deliciosa cobertura.",price:39.90,image:"https://images.unsplash.com/photo-1571407970349-bc81e7e96d47?auto=format&fit=crop&w=700&q=80"},
      {id:6,name:"Romeu e Julieta",category:"Doces",description:"A combinação clássica de queijo e goiabada.",price:38.90,image:"https://images.unsplash.com/photo-1579751626657-72bc17010498?auto=format&fit=crop&w=700&q=80"},
      {id:7,name:"Refrigerante lata",category:"Bebidas",description:"Lata gelada de 350 ml. Consulte os sabores.",price:6.00,image:"https://images.unsplash.com/photo-1581636625402-29b2a704ef13?auto=format&fit=crop&w=700&q=80"},
      {id:8,name:"Refrigerante 2 litros",category:"Bebidas",description:"Refrigerante para compartilhar com a família.",price:12.00,image:"https://images.unsplash.com/photo-1629203851122-3726ecdf080e?auto=format&fit=crop&w=700&q=80"},
      {id:9,name:"Água mineral",category:"Bebidas",description:"Água mineral para acompanhar seu pedido.",price:4.00,image:"https://images.unsplash.com/photo-1564419320461-6870880221ad?auto=format&fit=crop&w=700&q=80"}
    ];

    let activeCategory = "Todas";
    const cart = {};

    const money = value => value.toLocaleString("pt-BR",{style:"currency",currency:"BRL"});

    function renderProducts(){
      const term = document.getElementById("search").value.trim().toLowerCase();
      const list = products.filter(p =>
        (activeCategory === "Todas" || p.category === activeCategory) &&
        (p.name.toLowerCase().includes(term) || p.description.toLowerCase().includes(term))
      );
      document.getElementById("products").innerHTML = list.map(p => `
        <article class="product">
          <img src="${p.image}" alt="${p.name}" loading="lazy" onerror="this.style.display='none'">
          <div class="product-body">
            <span class="tag">${p.category}</span>
            <h3>${p.name}</h3>
            <p>${p.description}</p>
            <div class="product-bottom">
              <span class="price">${money(p.price)}</span>
              <button class="add" onclick="addToCart(${p.id})">+ Adicionar</button>
            </div>
          </div>
        </article>`).join("");
      document.getElementById("empty").hidden = list.length > 0;
    }

    document.getElementById("filters").addEventListener("click",event=>{
      const button = event.target.closest("button[data-category]");
      if(!button)return;
      activeCategory = button.dataset.category;
      document.querySelectorAll(".filter").forEach(b=>b.classList.toggle("active",b===button));
      renderProducts();
    });

    function addToCart(id){
      cart[id] = (cart[id] || 0) + 1;
      updateCart();
    }

    function changeQty(id,amount){
      cart[id] = (cart[id] || 0) + amount;
      if(cart[id] <= 0) delete cart[id];
      updateCart();
    }

    function updateCart(){
      const entries = products.filter(p=>cart[p.id]);
      const count = Object.values(cart).reduce((sum,q)=>sum+q,0);
      document.getElementById("cart-count").textContent = count;
      document.getElementById("cart-items").innerHTML = entries.length ? entries.map(p=>`
        <div class="cart-line">
          <div><strong>${p.name}</strong><br><span>${money(p.price)} cada</span></div>
          <div class="qty">
            <button type="button" onclick="changeQty(${p.id},-1)" aria-label="Diminuir ${p.name}">−</button>
            <span>${cart[p.id]}</span>
            <button type="button" onclick="changeQty(${p.id},1)" aria-label="Aumentar ${p.name}">+</button>
          </div>
        </div>`).join("") : '<p class="empty">Sua sacola está vazia. Adicione algo do cardápio!</p>';
      const subtotal = products.reduce((sum,p)=>sum+p.price*(cart[p.id]||0),0);
      document.getElementById("cart-total").textContent = money(subtotal + (count && document.getElementById("delivery").value==="Entrega" ? DELIVERY_FEE : 0));
    }

    function openCart(){document.getElementById("cart-modal").classList.add("open");updateCart()}
    function closeCart(){document.getElementById("cart-modal").classList.remove("open")}
    document.getElementById("delivery").addEventListener("change",()=>{
      document.getElementById("address-field").style.display = document.getElementById("delivery").value==="Entrega" ? "flex":"none";
      document.getElementById("address").required = document.getElementById("delivery").value==="Entrega";
      updateCart();
    });
    document.getElementById("cart-modal").addEventListener("click",e=>{if(e.target.id==="cart-modal")closeCart()});

    document.getElementById("order-form").addEventListener("submit",event=>{
      event.preventDefault();
      if(!Object.values(cart).some(q=>q>0)){
        alert("Adicione pelo menos um produto à sacola.");
        return;
      }
      if(!/^\d{10,15}$/.test(WHATSAPP_NUMBER)){
        alert("O cardápio ainda precisa do número de WhatsApp da pizzaria. Configure WHATSAPP_NUMBER no código antes de usar os pedidos.");
        return;
      }
      const subtotal = products.reduce((sum,p)=>sum+p.price*(cart[p.id]||0),0);
      const delivery = document.getElementById("delivery").value;
      const total = subtotal + (delivery==="Entrega" ? DELIVERY_FEE : 0);
      const lines = products.filter(p=>cart[p.id]).map(p=>`${cart[p.id]}x ${p.name} — ${money(p.price*cart[p.id])}`);
      const message = [
        "🍕 *NOVO PEDIDO — FORNO*",
        "",
        `Cliente: ${document.getElementById("customer").value}`,
        `Telefone: ${document.getElementById("phone").value}`,
        "",
        "*Pedido:*",
        ...lines,
        "",
        `Recebimento: ${delivery}`,
        ...(delivery==="Entrega" ? [`Endereço: ${document.getElementById("address").value}`] : []),
        `Pagamento: ${document.getElementById("payment").value}`,
        `Observações: ${document.getElementById("notes").value || "Nenhuma"}`,
        `Total estimado: ${money(total)}`,
        "",
        "Por favor, confirme o pedido e o valor final."
      ].join("\n");
      window.open(`https://wa.me/${WHATSAPP_NUMBER}?text=${encodeURIComponent(message)}`,"_blank","noopener");
    });

    document.getElementById("year").textContent = new Date().getFullYear();
    renderProducts();
    updateCart();
  </script>

</body>
</html>
