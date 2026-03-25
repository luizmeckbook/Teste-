<! ÁGUA E GELO DO JORGE>
<html>
<head>
  <title>Água e Gelo do Jorge</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <style>
    body {
      margin: 0;
      font-family: Arial;
      background: #f5f5f5;
    }

    header {
      background: linear-gradient(90deg, #00bfff, #00ffff);
      padding: 20px;
      text-align: center;
      color: black;
      font-weight: bold;
      font-size: 22px;
    }

    .banner {
      background: #00bfff;
      color: black;
      text-align: center;
      padding: 10px;
      font-weight: bold;
    }

    .container {
      padding: 15px;
    }

    .produto {
      background: white;
      margin-bottom: 15px;
      padding: 15px;
      border-radius: 12px;
      display: flex;
      align-items: center;
      gap: 10px;
      box-shadow: 0 0 10px #ccc;
      animation: fadeUp 0.8s ease;
    }

    .produto img {
      width: 80px;
      border-radius: 10px;
      background: white;
      padding: 10px;
    }

    .info {
      flex: 1;
    }

    .preco {
      color: green;
      font-weight: bold;
    }

    .controls {
      display: flex;
      align-items: center;
      gap: 5px;
    }

    .controls button {
      padding: 5px 10px;
      border-radius: 5px;
      border: none;
      background: #00bfff;
      color: black;
      font-weight: bold;
    }

    .qtd {
      min-width: 20px;
      text-align: center;
    }

    .form {
      padding: 15px;
    }

    input {
      width: 100%;
      padding: 12px;
      margin-bottom: 10px;
      border-radius: 8px;
      border: 1px solid #ccc;
    }

    .carrinho {
      position: fixed;
      bottom: 15px;
      right: 15px;
      background: #00bfff;
      color: black;
      padding: 15px 20px;
      border-radius: 30px;
      font-weight: bold;
      box-shadow: 0 0 10px #000;
    }

    @keyframes fadeUp {
      from {opacity:0; transform:translateY(20px);}
      to {opacity:1; transform:translateY(0);}
    }
  </style>
</head>

<body>

<header>
  🧊 Água e Gelo do Jorge
</header>

<div class="banner">
  🚚 Entrega rápida na sua região
</div>

<div class="container">

  <!-- GELO -->
  <div class="produto">
    <img src="https://cdn-icons-png.flaticon.com/512/1046/1046857.png">
    <div class="info">
      <h3>Saco de Gelo</h3>
      <p class="preco">R$10</p>
    </div>
    <div class="controls">
      <button onclick="remover('gelo')">-</button>
      <div id="qtd-gelo" class="qtd">0</div>
      <button onclick="adicionar('gelo',10)">+</button>
    </div>
  </div>

  <!-- ÁGUA -->
  <div class="produto">
    <img src="https://cdn-icons-png.flaticon.com/512/3103/3103446.png">
    <div class="info">
      <h3>Galão de Água</h3>
      <p class="preco">R$5</p>
    </div>
    <div class="controls">
      <button onclick="remover('agua')">-</button>
      <div id="qtd-agua" class="qtd">0</div>
      <button onclick="adicionar('agua',5)">+</button>
    </div>
  </div>

</div>

<div class="form">
  <input id="nome" placeholder="Seu nome">
  <input id="endereco" placeholder="Seu endereço">
</div>

<div class="carrinho" onclick="finalizar()">
  🛒 <span id="itens">0</span> itens | R$<span id="total">0</span>
</div>

<script>
  let carrinho = {
    gelo: {qtd: 0, preco: 10, nome: "Saco de Gelo"},
    agua: {qtd: 0, preco: 5, nome: "Galão de Água"}
  };

  function adicionar(item, preco) {
    carrinho[item].qtd++;
    atualizar();
  }

  function remover(item) {
    if (carrinho[item].qtd > 0) {
      carrinho[item].qtd--;
    }
    atualizar();
  }

  function atualizar() {
    let total = 0;
    let itens = 0;

    for (let item in carrinho) {
      document.getElementById("qtd-" + item).innerText = carrinho[item].qtd;
      total += carrinho[item].qtd * carrinho[item].preco;
      itens += carrinho[item].qtd;
    }

    document.getElementById("total").innerText = total;
    document.getElementById("itens").innerText = itens;
  }

  function finalizar() {
    let nome = document.getElementById("nome").value;
    let endereco = document.getElementById("endereco").value;

    let mensagem = "Pedido:%0A";

    for (let item in carrinho) {
      if (carrinho[item].qtd > 0) {
        mensagem += `${carrinho[item].nome} x${carrinho[item].qtd}%0A`;
      }
    }

    mensagem += `%0ATotal: R$${document.getElementById("total").innerText}%0A`;
    mensagem += `Nome: ${nome}%0AEndereço: ${endereco}`;

    window.open("https://wa.me/5521994450547?text=" + mensagem);
  }
</script>

</body>
</html>
