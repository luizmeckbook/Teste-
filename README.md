<!MEDELA SUPERMERCADO>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Medela Supermercado</title>

<style>
body {
  margin: 0;
  font-family: Arial;
  background: #f5f5f5;
}

/* TOPO */
header {
  background: #1a8f3c;
  color: white;
  padding: 15px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

header h1 {
  margin: 0;
}

header input {
  padding: 8px;
  border-radius: 8px;
  border: none;
  width: 200px;
}

/* BANNER */
.banner {
  background: url('https://images.unsplash.com/photo-1607083206968-13611e3d76db');
  background-size: cover;
  background-position: center;
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 28px;
  font-weight: bold;
  text-shadow: 0 0 10px black;
}

/* PRODUTOS */
.produtos {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px,1fr));
  gap: 15px;
  padding: 15px;
}

.produto {
  background: white;
  padding: 10px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
}

.produto img {
  width: 100%;
  height: 120px;
  object-fit: cover;
  border-radius: 10px;
}

.produto button {
  background: #1a8f3c;
  color: white;
  border: none;
  padding: 8px;
  border-radius: 8px;
  cursor: pointer;
}

/* CARRINHO */
.carrinho {
  position: fixed;
  bottom: 0;
  width: 100%;
  background: #1a8f3c;
  color: white;
  text-align: center;
  padding: 10px;
}

</style>
</head>

<body>

<header>
  <h1>🛒 Medela Supermercado</h1>
  <input placeholder="Buscar produto...">
</header>

<div class="banner">
  Ofertas Imperdíveis 🔥
</div>

<div class="produtos">

  <div class="produto">
    <img src="https://images.unsplash.com/photo-1586201375761-83865001e31c">
    <h3>Arroz 5kg</h3>
    <p>R$ 25,00</p>
    <button onclick="add('Arroz',25)">Adicionar</button>
  </div>

  <div class="produto">
    <img src="https://images.unsplash.com/photo-1585238342028-4c6a7c64c6d2">
    <h3>Feijão</h3>
    <p>R$ 8,00</p>
    <button onclick="add('Feijão',8)">Adicionar</button>
  </div>

  <div class="produto">
    <img src="https://images.unsplash.com/photo-1604908177522-432d3a1fa90b">
    <h3>Leite</h3>
    <p>R$ 5,00</p>
    <button onclick="add('Leite',5)">Adicionar</button>
  </div>

</div>

<div class="carrinho">
  🛒 Total: R$ <span id="total">0</span>
  <br>
  <button onclick="comprar()">Finalizar no WhatsApp</button>
</div>

<script>
let carrinho = [];
let total = 0;

function add(nome, preco){
  carrinho.push({nome, preco});
  total += preco;
  document.getElementById("total").innerText = total;
}

function comprar(){
  let msg = "Pedido Medela:%0A";

  carrinho.forEach(p=>{
    msg += `${p.nome} - R$${p.preco}%0A`;
  });

  msg += `Total: R$${total}`;

  window.open("https://wa.me/5521999999999?text=" + msg);
}
</script>

</body>
</html>
