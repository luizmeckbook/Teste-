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
        .container { padding: 15px; max-width: 500px; margin: auto; }
        .card { background: white; padding: 20px; border-radius: 15px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); margin-bottom: 15px; }
        input { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        
        button { border: none; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; color: white; width: 100%; transition: 0.3s; }
        .btn-main { background: var(--primary); }
        .btn-support { background: var(--whatsapp); margin-top: 10px; display: flex; align-items: center; justify-content: center; gap: 8px; }
        .btn-support:hover { opacity: 0.9; }
        
        .header { background: var(--primary); color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; }
        .header-admin { background: var(--admin-bg); }

        /* Chat */
        .chat-screen { display: flex; flex-direction: column; height: 100vh; background: #e5ddd5; }
        .chat-messages { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 8px; }
        .msg { max-width: 80%; padding: 10px; border-radius: 10px; font-size: 14px; }
        .msg.sent { align-self: flex-end; background: #dcf8c6; }
        .msg.received { align-self: flex-start; background: white; }
    </style>
</head>
<body>

<div id="login" class="tela ativa">
    <div class="container" style="text-align:center; padding-top:60px;">
        <h1 id="brandName">🛒 Medela</h1>
        <div class="card">
            <input id="lCpf" placeholder="CPF ou 'admin'">
            <input id="lSenha" type="password" placeholder="Senha">
            <button class="btn-main" onclick="entrar()">Entrar no App</button>
            
            <div style="margin: 15px 0; color: #888; font-size: 14px;">ou</div>

            <button class="btn-support" onclick="abrirSuporteRapido()">
                <span>💬</span> Falar com Suporte
            </button>

            <p onclick="ir('cadastro')" style="color:var(--primary); cursor:pointer; margin-top:20px; font-size: 14px;">Ainda não tem conta? <b>Cadastre-se</b></p>
        </div>
    </div>
</div>

<div id="cadastro" class="tela">
    <div class="container">
        <h2>Criar Conta</h2>
        <input id="cNome" placeholder="Nome Completo">
        <input id="cCpf" placeholder="CPF">
        <input id="cSenha" type="password" placeholder="Crie uma Senha">
        <button class="btn-main" onclick="registrar()">Finalizar Cadastro</button>
        <button onclick="ir('login')" style="background:none; color:gray; margin-top:10px;">Voltar ao Login</button>
    </div>
</div>

<div id="home" class="tela">
    <div class="header">
        <span id="welcome">Olá</span>
        <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <div id="listaLoja"></div>
    </div>
    <button onclick="abrirChatCliente()" style="position:fixed; bottom:20px; right:20px; width:60px; height:60px; border-radius:50%; background:var(--whatsapp); border:none; font-size:25px; box-shadow: 0 4px 8px rgba(0,0,0,0.2); color:white; cursor:pointer;">💬</button>
</div>

<div id="chat_suporte" class="tela">
    <div class="chat-screen">
        <div class="header" style="background:#075e54">
            <button onclick="voltarDoChat()" style="width:auto; background:none; font-size:20px">←</button>
            <span>Suporte Medela</span>
            <div style="width:30px"></div>
        </div>
        <div class="chat-messages" id="box_msgs"></div>
        <div style="padding:10px; background:#f0f0f0; display:flex; gap:5px;">
            <input id="msg_input" placeholder="Digite sua dúvida..." style="margin:0; border-radius:20px;">
            <button onclick="enviarMsg()" style="width:50px; border-radius:50%; background:var(--whatsapp)">➤</button>
        </div>
    </div>
</div>

<div id="admin" class="tela">
    <div class="header header-admin">
        <span>⚙️ Painel Administrativo</span>
        <button style="width:auto; background:rgba(255,255,255,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <div class="card">
            <h3>🎨 Modelagem do Site</h3>
            <input id="cfgNome" placeholder="Novo Nome do Mercado">
            <label>Cor do Tema:</label>
            <input type="color" id="cfgCor">
            <button onclick="salvarConfig()" class="btn-main">Salvar Alterações</button>
        </div>

        <div class="card">
            <h3>👥 Clientes e Senhas</h3>
            <div id="lista_usuarios_admin"></div>
        </div>

        <div class="card">
            <h3>💬 Conversas Ativas</h3>
            <div id="lista_chats_admin"></div>
        </div>
    </div>
</div>

<script>
let usuarios = JSON.parse(localStorage.getItem("med_users")) || {};
let config = JSON.parse(localStorage.getItem("med_config")) || { nome: "Medela Supermercado", cor: "#e53935" };
let mensagens = JSON.parse(localStorage.getItem("med_chat")) || {};
let usuarioAtivo = "visitante";

function aplicarConfig() {
    document.getElementById("brandName").innerText = "🛒 " + config.nome;
    document.getElementById("siteTitle").innerText = config.nome;
    document.getElementById("dynamicStyles").innerHTML = `:root { --primary: ${config.cor}; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }`;
}

function ir(tela) {
    document.querySelectorAll('.tela').forEach(t => t.classList.remove('ativa'));
    document.getElementById(tela).classList.add('ativa');
    if(tela === 'admin') renderizarAdmin();
}

/* LOGIN E SUPORTE */
function entrar() {
    let cpf = document.getElementById("lCpf").value;
    let senha = document.getElementById("lSenha").value;
    if(cpf === 'admin' && senha === 'admin123') return ir('admin');
    if(usuarios[cpf] && usuarios[cpf].senha === senha) {
        usuarioAtivo = cpf;
        ir('home');
    } else { alert("Usuário ou senha incorretos!"); }
}

function abrirSuporteRapido() {
    usuarioAtivo = "visitante_" + Math.floor(Math.random() * 1000);
    ir('chat_suporte');
    renderizarMsgs();
}

function abrirChatCliente() {
    ir('chat_suporte');
    renderizarMsgs();
}

function voltarDoChat() {
    if(usuarioAtivo.includes("visitante")) ir('login');
    else ir('home');
}

/* CHAT LOGIC */
function enviarMsg() {
    let input = document.getElementById("msg_input");
    if(!input.value) return;
    if(!mensagens[usuarioAtivo]) mensagens[usuarioAtivo] = [];
    
    mensagens[usuarioAtivo].push({ texto: input.value, autor: 'cliente' });
    localStorage.setItem("med_chat", JSON.stringify(mensagens));
    input.value = "";
    renderizarMsgs();
}

function renderizarMsgs() {
    let box = document.getElementById("box_msgs");
    let msgs = mensagens[usuarioAtivo] || [];
    box.innerHTML = msgs.map(m => `
        <div class="msg ${m.autor === 'cliente' ? 'sent' : 'received'}">${m.texto}</div>
    `).join('');
    box.scrollTop = box.scrollHeight;
}

/* ADMIN LOGIC */
function renderizarAdmin() {
    // Listar usuários e senhas
    document.getElementById("lista_usuarios_admin").innerHTML = Object.keys(usuarios).map(cpf => `
        <div style="border-bottom:1px solid #eee; padding:10px 0">
            <b>${usuarios[cpf].nome}</b><br>
            <small>CPF: ${cpf} | Senha: <span style="color:red">${usuarios[cpf].senha}</span></small>
        </div>
    `).join('') || "Nenhum cliente cadastrado.";

    // Listar Chats
    document.getElementById("lista_chats_admin").innerHTML = Object.keys(mensagens).map(cpf => `
        <div class="card" style="cursor:pointer; background:#f9f9f9" onclick="abrirRespostaAdmin('${cpf}')">
            Responder: <b>${usuarios[cpf] ? usuarios[cpf].nome : cpf}</b>
        </div>
    `).join('') || "Sem conversas.";
}

function salvarConfig() {
    config.nome = document.getElementById("cfgNome").value || config.nome;
    config.cor = document.getElementById("cfgCor").value;
    localStorage.setItem("med_config", JSON.stringify(config));
    location.reload();
}

function registrar() {
    let nome = document.getElementById("cNome").value;
    let cpf = document.getElementById("cCpf").value;
    let senha = document.getElementById("cSenha").value;
    usuarios[cpf] = { nome, senha };
    localStorage.setItem("med_users", JSON.stringify(usuarios));
    alert("Cadastrado com sucesso!");
    ir('login');
}

function sair() { location.reload(); }

window.onload = aplicarConfig;
</script>
</body>
</html>
