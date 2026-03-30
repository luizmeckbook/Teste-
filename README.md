<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Açougue Medela</title>

<style>
body { font-family: Arial; margin: 0; background: #f5f5f5; }

header {
  background: #b30000;
  color: white;
  padding: 15px;
  font-size: 20px;
}

.container { padding: 15px; }

.produto {
  background: white;
  margin: 10px 0;
  padding: 15px;
  border-radius: 10px;
}

button {
  background: #b30000;
  color: white;
  border: none;
  padding: 10px;
  border-radius: 5px;
}

input {
  padding: 10px;
  margin: 5px 0;
  width: 100%;
}

/* CHAT */
#chatBox {
  background: #e5ddd5;
  height: 300px;
  overflow-y: auto;
  padding: 10px;
  border-radius: 10px;
}

.msg {
  padding: 10px;
  margin: 5px;
  border-radius: 10px;
  max-width: 70%;
}

.cliente {
  background: #dcf8c6;
  margin-left: auto;
  text-align: right;
}

.admin {
  background: white;
}
</style>

</head>
<body>

<header>🥩 Açougue Medela</header>

<div class="container">

<h2>Login</h2>
<input id="cpf" placeholder="CPF">
<input id="senha" type="password" placeholder="Senha">
<button onclick="login()">Entrar</button>

<hr>

<h2>Produtos</h2>
<div id="produtos"></div>

<h2>Carrinho</h2>
<div id="carrinho"></div>
<p id="total"></p>
<button onclick="finalizar()">Finalizar Pedido</button>

<h2>Status do Pedido</h2>
<p id="status"></p>

<h2>Chat</h2>
<div id="chatBox"></div>
<input id="msg" placeholder="Mensagem">
<button onclick="enviarMensagem()">Enviar</button>

</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import {
  getFirestore,
  collection,
  getDocs,
  addDoc,
  query,
  where,
  onSnapshot,
  orderBy
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

// 🔥 CONFIG FIREBASE
const firebaseConfig = {
  apiKey: "SUA_KEY",
  authDomain: "SEU_DOMINIO",
  projectId: "SEU_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

// LOGIN
window.login = async () => {
  const cpf = document.getElementById("cpf").value;
  const senha = document.getElementById("senha").value;

  const q = query(
    collection(db, "usuarios"),
    where("cpf", "==", cpf),
    where("senha", "==", senha)
  );

  const res = await getDocs(q);

  if (res.empty) {
    alert("Login inválido");
  } else {
    localStorage.setItem("cpf", cpf);
    alert("Logado!");
    carregarProdutos();
    ouvirPedidos();
    iniciarChat();
  }
};

// CARRINHO
let carrinho = [];
const cpf = localStorage.getItem("cpf");

window.add = (nome, preco) => {
  carrinho.push({ nome, preco });
  render();
};

function render() {
  let html = "";
  let total = 0;

  carrinho.forEach(p => {
    total += p.preco;
    html += `<p>${p.nome} - R$${p.preco}</p>`;
  });

  document.getElementById("carrinho").innerHTML = html;
  document.getElementById("total").innerText = "Total: R$" + total;
}

// PRODUTOS
async function carregarProdutos() {
  const snap = await getDocs(collection(db, "produtos"));
  let html = "";

  snap.forEach(doc => {
    let p = doc.data();
    html += `
      <div class="produto">
        ${p.nome} - R$${p.preco}
        <button onclick="add('${p.nome}', ${p.preco})">+</button>
      </div>
    `;
  });

  document.getElementById("produtos").innerHTML = html;
}

// PEDIDO
window.finalizar = async () => {
  const cpf = localStorage.getItem("cpf");

  await addDoc(collection(db, "pedidos"), {
    cpf,
    carrinho,
    status: "Em preparo"
  });

  alert("Pedido feito!");
};

function ouvirPedidos() {
  const cpf = localStorage.getItem("cpf");

  onSnapshot(collection(db, "pedidos"), snap => {
    snap.forEach(doc => {
      let d = doc.data();
      if (d.cpf === cpf) {
        document.getElementById("status").innerText = d.status;
      }
    });
  });
}

// CHAT
window.enviarMensagem = async () => {
  const texto = document.getElementById("msg").value;
  const cpf = localStorage.getItem("cpf");

  if (!texto) return;

  await addDoc(collection(db, "chats"), {
    cpf,
    mensagem: texto,
    tipo: "cliente",
    data: new Date()
  });

  document.getElementById("msg").value = "";
};

function iniciarChat() {
  const cpf = localStorage.getItem("cpf");

  const q = query(
    collection(db, "chats"),
    where("cpf", "==", cpf),
    orderBy("data")
  );

  onSnapshot(q, snap => {
    let html = "";

    snap.forEach(doc => {
      let msg = doc.data();

      if (msg.tipo === "cliente") {
        html += `<div class="msg cliente">${msg.mensagem}</div>`;
      } else {
        html += `<div class="msg admin">${msg.mensagem}</div>`;
      }
    });

    const chatBox = document.getElementById("chatBox");
    chatBox.innerHTML = html;
    chatBox.scrollTop = chatBox.scrollHeight;
  });
}

// AUTO LOGIN
if (cpf) {
  carregarProdutos();
  ouvirPedidos();
  iniciarChat();
}
</script>

</body>
</html>
