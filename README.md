<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title id="siteTitle">Medela Supermercado</title>
    <style id="dynamicStyles">
        :root { --primary: #e53935; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }
    </style>
    <style>
        body { margin:0; font-family: 'Segoe UI', Arial, sans-serif; background: var(--bg); }
        .tela { display:none; min-height: 100vh; }
        .ativa { display:block; }
        .container { padding: 15px; max-width: 600px; margin: auto; }
        .card { background: white; padding: 15px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); margin-bottom: 15px; }
        
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        button { border: none; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; color: white; width: 100%; }
        .btn-main { background: var(--primary); }
        .btn-support { background: var(--whatsapp); margin-top: 10px; display: flex; align-items: center; justify-content: center; gap: 8px; }
        
        .header { background: var(--primary); color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; }
        
        /* Estilo WhatsApp */
        .chat-screen { display: flex; flex-direction: column; height: 100vh; background: #e5ddd5; }
        .chat-messages { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 8px; }
        .msg { max-width: 80%; padding: 10px; border-radius: 10px; font-size: 14px; position: relative; }
        .msg.sent { align-self: flex-end; background: #dcf8c6; }
        .msg.received { align-self: flex-start; background: white; }
        
        .chat-footer { padding: 10px; background: #f0f0f0; display: flex; gap: 5px; }
        .chat-footer input { margin: 0; border-radius: 20px; }
        .btn-send { width: 50px; border-radius: 50%; background: var(--whatsapp); }

        .cat-nav { display: flex; gap: 10px; overflow-x: auto; padding: 10px 0; margin-bottom: 10px; }
        .cat-btn { background: white; border: 1px solid #ddd; padding: 8px 15px; border-radius: 20px; white-space: nowrap; width: auto; }
        .cat-btn.active { background: var(--primary); color: white; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
    </style>
</head>
<body>

<div id="login" class="tela ativa">
    <div class="container" style="text-align:center; padding-top:60px;">
        <h1 id="brandName">🛒 Medela</h1>
        <div class="card">
            <input id="lCpf" placeholder="CPF ou 'admin'">
            <input id="lSenha" type="password" placeholder="Senha">
            <button class="btn-main" onclick="entrar()">Entrar</button>
            <button class="btn-support" onclick="abrirSuporteVisitante()">💬 Suporte</button>
            <p onclick="ir('cadastro')" style="color:var(--primary); cursor:pointer; margin-top:20px;">Criar Conta</p>
        </div>
    </div>
</div>

<div id="cadastro" class="tela">
    <div class="container">
        <h2>Criar Conta</h2>
        <input id="cNome" placeholder="Nome Completo">
        <input id="cCpf" placeholder="CPF">
        <input id="cSenha" type="password" placeholder="Senha">
        <button class="btn-main" onclick="registrar()">Finalizar Cadastro</button>
        <button onclick="ir('login')" style="background:none; color:gray; margin-top:10px;">Voltar</button>
    </div>
</div>

<div id="home" class="tela">
    <div class="header">
        <span id="welcome">Olá</span>
        <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <div class="cat-nav" id="catNav"></div>
        <div class="grid" id="listaLoja"></div>
    </div>
    <button onclick="ir('chat')" style="position:fixed; bottom:20px; right:20px; width:60px; height:60px; border-radius:50%; background:var(--whatsapp); border:none; font-size:25px; color:white; box-shadow: 0 4px 10px rgba(0,0,0,0.3);">💬</button>
</div>

<div id="admin" class="tela">
    <div class="header" style="background:var(--admin-bg)">
        <span>⚙️ Painel Gestor</span>
        <button style="width:auto; background:rgba(255,255,255,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <div class="card">
            <h3>🎨 Modelagem do Site</h3>
            <input id="cfgNome" placeholder="Nome do Mercado">
            <input type="color" id="cfgCor">
            <button onclick="salvarConfig()" class="btn-main">Salvar</button>
        </div>

        <div class="card">
            <h3>💬 Conversas com Clientes</h3>
            <div id="lista_chats_admin"></div>
        </div>

        <div class="card">
            <h3>📦 Novo Produto</h3>
            <input id="pNome" placeholder="Produto">
            <input id="pPreco" type="number" placeholder="Preço">
            <select id="pCat">
                <option>Açougue</option><option>Bebida</option><option>Limpeza</option><option>Padaria</option><option>Mercearia</option>
            </select>
            <button onclick="addProduto()" style="background:var(--admin-bg)">Cadastrar</button>
        </div>

        <div class="card">
            <h3>👥 Clientes e Senhas</h3>
            <div id="lista_usuarios_admin"></div>
        </div>
    </div>
</div>

<div id="chat" class="tela">
    <div class="chat-screen">
        <div class="header" style="background:#075e54">
            <button onclick="voltarDoChat()" style="width:auto; background:none; font-size:20px">←</button>
            <span id="chatTitulo">Chat</span>
            <div style="width:30px"></div>
        </div>
        <div class="chat-messages" id="box_msgs"></div>
        <div class="chat-footer">
            <input id="msg_input" placeholder="Mensagem...">
            <button class="btn-send" onclick="enviarMensagem()">➤</button>
        </div>
    </div>
</div>

<script>
let usuarios = JSON.parse(localStorage.getItem("m_users")) || {};
let produtos = JSON.parse(localStorage.getItem("m_prod")) || [];
let config = JSON.parse(localStorage.getItem("m_cfg")) || { nome: "Medela Supermercado", cor: "#e53935" };
let mensagens = JSON.parse(localStorage.getItem("m_chat")) || {};

let sessaoAtiva = ""; // CPF do usuário logado ou "admin"
let clienteNoChat = ""; // CPF do cliente que está sendo atendido (pelo admin)
let modoAdminChat = false;

function aplicarConfig() {
    document.getElementById("brandName").innerText = "🛒 " + config.nome;
    document.getElementById("siteTitle").innerText = config.nome;
    document.getElementById("dynamicStyles").innerHTML = `:root { --primary: ${config.cor}; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }`;
}

function ir(tela) {
    document.querySelectorAll('.tela').forEach(t => t.classList.remove('ativa'));
    document.getElementById(tela).classList.add('ativa');
    if(tela === 'home') renderizarLoja();
    if(tela === 'admin') renderizarAdmin();
    if(tela === 'chat') renderizarMensagens();
}

/* AUTH */
function entrar() {
    let cpf = document.getElementById("lCpf").value;
    let senha = document.getElementById("lSenha").value;
    if(cpf === 'admin' && senha === 'admin123') { sessaoAtiva = "admin"; return ir('admin'); }
    if(usuarios[cpf] && usuarios[cpf].senha === senha) { sessaoAtiva = cpf; ir('home'); }
    else { alert("Login inválido!"); }
}

function registrar() {
    let n = document.getElementById("cNome").value;
    let c = document.getElementById("cCpf").value;
    let s = document.getElementById("cSenha").value;
    usuarios[c] = { nome: n, senha: s };
    localStorage.setItem("m_users", JSON.stringify(usuarios));
    ir('login');
}

/* CHAT LOGIC */
function abrirSuporteVisitante() {
    sessaoAtiva = "visitante_" + Math.floor(Math.random()*100);
    clienteNoChat = sessaoAtiva;
    modoAdminChat = false;
    document.getElementById("chatTitulo").innerText = "Suporte Medela";
    ir('chat');
}

function abrirChatAdmin(cpfCliente) {
    clienteNoChat = cpfCliente;
    modoAdminChat = true;
    document.getElementById("chatTitulo").innerText = "Atendendo: " + (usuarios[cpfCliente] ? usuarios[cpfCliente].nome : "Visitante");
    ir('chat');
}

function enviarMensagem() {
    let input = document.getElementById("msg_input");
    if(!input.value) return;
    
    let cpf = modoAdminChat ? clienteNoChat : sessaoAtiva;
    if(!mensagens[cpf]) mensagens[cpf] = [];

    mensagens[cpf].push({
        texto: input.value,
        autor: modoAdminChat ? 'admin' : 'cliente'
    });

    localStorage.setItem("m_chat", JSON.stringify(mensagens));
    input.value = "";
    renderizarMensagens();
}

function renderizarMensagens() {
    let cpf = modoAdminChat ? clienteNoChat : sessaoAtiva;
    let msgs = mensagens[cpf] || [];
    let box = document.getElementById("box_msgs");
    
    box.innerHTML = msgs.map(m => `
        <div class="msg ${m.autor === (modoAdminChat ? 'admin' : 'cliente') ? 'sent' : 'received'}">
            ${m.texto}
        </div>
    `).join('');
    box.scrollTop = box.scrollHeight;
}

function voltarDoChat() {
    if(modoAdminChat) { modoAdminChat = false; ir('admin'); }
    else if(sessaoAtiva.includes("visitante")) ir('login');
    else ir('home');
}

/* ADMIN */
function renderizarAdmin() {
    // Listar Senhas
    document.getElementById("lista_usuarios_admin").innerHTML = Object.keys(usuarios).map(c => `
        <div style="border-bottom:1px solid #eee; padding:5px"><b>${usuarios[c].nome}</b> | CPF: ${c} | Senha: <b style="color:red">${usuarios[c].senha}</b></div>
    `).join('');

    // Listar Conversas
    document.getElementById("lista_chats_admin").innerHTML = Object.keys(mensagens).map(c => `
        <div class="card" onclick="abrirChatAdmin('${c}')" style="cursor:pointer; border-left: 4px solid var(--whatsapp)">
            Responder: <b>${usuarios[c] ? usuarios[c].nome : 'Visitante ('+c+')'}</b>
        </div>
    `).join('') || "Sem conversas novas.";
}

function addProduto() {
    let n = document.getElementById("pNome").value;
    let p = document.getElementById("pPreco").value;
    let c = document.getElementById("pCat").value;
    produtos.push({nome: n, preco: parseFloat(p), cat: c});
    localStorage.setItem("m_prod", JSON.stringify(produtos));
    alert("Adicionado!");
}

function salvarConfig() {
    config.nome = document.getElementById("cfgNome").value || config.nome;
    config.cor = document.getElementById("cfgCor").value;
    localStorage.setItem("m_cfg", JSON.stringify(config));
    location.reload();
}

/* LOJA CLIENTE */
function renderizarLoja() {
    document.getElementById("welcome").innerText = "Olá, " + (usuarios[sessaoAtiva]?.nome || "Cliente");
    document.getElementById("catNav").innerHTML = ["Todos", "Açougue", "Bebida", "Limpeza", "Padaria", "Mercearia"].map(c => `
        <button class="cat-btn" onclick="renderizarLojaFiltrada('${c}')">${c}</button>
    `).join('');
    renderizarLojaFiltrada("Todos");
}

function renderizarLojaFiltrada(cat) {
    let lista = cat === "Todos" ? produtos : produtos.filter(p => p.cat === cat);
    document.getElementById("listaLoja").innerHTML = lista.map(p => `
        <div class="card" style="text-align:center">
            <strong>${p.nome}</strong><br>
            <span style="color:green">R$ ${p.preco.toFixed(2)}</span>
        </div>
    `).join('') || "Nenhum produto aqui.";
}

function sair() { location.reload(); }
window.onload = aplicarConfig;
</script>
</body>
</html>
