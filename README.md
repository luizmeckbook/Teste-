<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - V9 PRO</title>
    <style>
        :root { --green: #00a859; --orange: #f37021; --gray: #f4f4f4; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); margin: 0; padding: 0; }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; box-sizing: border-box; }
        .header { background: var(--orange); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        .cat-bar { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; scrollbar-width: none; }
        .cat-item { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 12px; font-weight: bold; cursor: pointer; }
        .cat-item.active-cat { background: var(--green); color: white; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 120px; }
        .card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .card img { width: 100%; height: 90px; object-fit: contain; margin-bottom: 5px; }
        .btn { background: var(--green); color: white; border: none; padding: 15px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; margin-top: 10px; }
        .input-group { margin-bottom: 10px; }
        .input-group input, .input-group select, .input-group textarea { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; font-family: inherit; }
        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .nav-btn { font-size: 11px; color: #aaa; border: none; background: none; font-weight: bold; cursor: pointer; }
        .admin-item { background: white; padding: 10px; margin-bottom: 5px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange); }
        
        /* Estilo do Chat */
        .chat-box { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px; background: #e5ddd5; margin-bottom: 80px; }
        .msg { padding: 10px; border-radius: 10px; max-width: 80%; font-size: 14px; position: relative; }
        .msg-client { background: #dcf8c6; align-self: flex-end; }
        .msg-admin { background: #fff; align-self: flex-start; }
        .msg-time { font-size: 9px; color: #999; display: block; text-align: right; }
    </style>
</head>
<body>

<section id="scr-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 50px 0;">
            <h1 style="color: var(--green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <div class="input-group"><input type="text" id="l-cpf" placeholder="CPF (000.000.000-00)" oninput="maskCPF(this)" maxlength="14"></div>
        <button class="btn" onclick="login()">ENTRAR</button>
        <p style="text-align:center;">Não tem conta? <span style="color:var(--orange); cursor:pointer" onclick="go('scr-reg')">Cadastre-se</span></p>
        <p style="text-align:center; font-size:10px; color:#ccc; margin-top:50px;" onclick="askAdmin()">ADMINISTRAÇÃO</p>
    </div>
</section>

<section id="scr-reg" class="app-screen">
    <div class="header"><h2>Cadastro</h2></div>
    <div class="container">
        <div class="input-group"><input type="text" id="r-nome" placeholder="Nome Completo"></div>
        <div class="input-group"><input type="text" id="r-cpf" placeholder="CPF" oninput="maskCPF(this)" maxlength="14"></div>
        <div class="input-group"><input type="text" id="r-end" placeholder="Endereço"></div>
        <button class="btn" onclick="register()">SALVAR E ENTRAR</button>
        <button class="btn" style="background:#888" onclick="go('scr-login')">VOLTAR</button>
    </div>
</section>

<section id="scr-home" class="app-screen">
    <div class="header">
        <h3 id="u-name" style="margin:0">Olá!</h3>
        <input type="text" placeholder="Buscar produto..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="cat-bar" id="cat-loja"></div>
    <div id="p-grid" class="grid"></div>
</section>

<section id="scr-cart" class="app-screen">
    <div class="header"><h2>Meu Carrinho</h2></div>
    <div class="container" id="c-list" style="padding-bottom: 200px;"></div>
    <div id="c-foot" class="container" style="position:fixed; bottom:60px; background:white; width:100%; border-top:1px solid #eee; display:none; left:50%; transform:translateX(-50%);">
        <h3 style="color:var(--green)">Total: R$ <span id="t-val">0,00</span></h3>
        <button class="btn" style="background:#25d366" onclick="sendZap()">PEDIR NO WHATSAPP</button>
    </div>
</section>

<section id="scr-suporte" class="app-screen">
    <div class="header"><h2>Suporte Privado</h2></div>
    <div id="chat-cliente" class="chat-box"></div>
    <div class="container" style="position:fixed; bottom:60px; background:#f0f0f0; width:100%; max-width:450px; left:50%; transform:translateX(-50%); display:flex; gap:5px; padding:10px;">
        <input type="text" id="msg-input" placeholder="Digite sua dúvida..." style="flex:1; padding:10px; border-radius:20px; border:1px solid #ccc;">
        <button onclick="enviarMensagem()" style="background:var(--green); color:white; border:none; border-radius:50%; width:40px; height:40px; cursor:pointer;">➔</button>
    </div>
</section>

<section id="scr-admin" class="app-screen">
    <div class="header"><h2>Painel Admin</h2></div>
    <div class="cat-bar">
        <div class="cat-item active-cat" id="tab-p" onclick="abaAdmin('produtos')">Produtos</div>
        <div class="cat-item" id="tab-m" onclick="abaAdmin('mensagens')">Mensagens</div>
    </div>
    <div class="container" id="adm-content"></div>
</section>

<nav class="nav" id="bot-nav" style="display:none;">
    <button class="nav-btn" onclick="go('scr-home')">LOJA</button>
    <button class="nav-btn" onclick="go('scr-cart')">CESTA (<span id="count">0</span>)</button>
    <button class="nav-btn" onclick="go('scr-suporte')">SUPORTE</button>
    <button class="nav-btn" onclick="logout()">SAIR</button>
</nav>

<script>
    const CATS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria", "Fiambreria"];
    let db = JSON.parse(localStorage.getItem('m_db')) || [];
    let chats = JSON.parse(localStorage.getItem('m_chats')) || {};
    let user = JSON.parse(localStorage.getItem('m_user'));
    let isAdmin = localStorage.getItem('m_is_admin') === 'true';
    let cart = [];
    let curCat = "Mercearia";

    window.onload = () => {
        if (isAdmin) { go('scr-admin'); abaAdmin('produtos'); }
        else if (user) { document.getElementById('u-name').innerText = `Olá, ${user.nome.split(' ')[0]}`; go('scr-home'); }
        else { go('scr-login'); }
    };

    function go(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('bot-nav').style.display = (id !== 'scr-login' && id !== 'scr-reg' && !isAdmin) ? 'flex' : (isAdmin ? 'none' : 'none');
        if(id === 'scr-home') renderHome();
        if(id === 'scr-cart') renderCart();
        if(id === 'scr-suporte') renderChatCliente();
    }

    // --- CHAT SISTEMA ---
    function renderChatCliente() {
        const box = document.getElementById('chat-cliente');
        const minhaConversa = chats[user.cpf] || [];
        box.innerHTML = minhaConversa.map(m => `
            <div class="msg ${m.from === 'user' ? 'msg-client' : 'msg-admin'}">
                ${m.text}<span class="msg-time">${m.hora}</span>
            </div>
        `).join('');
        box.scrollTop = box.scrollHeight;
    }

    function enviarMensagem() {
        const inp = document.getElementById('msg-input');
        if(!inp.value) return;
        if(!chats[user.cpf]) chats[user.cpf] = [];
        chats[user.cpf].push({ from: 'user', text: inp.value, hora: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) });
        localStorage.setItem('m_chats', JSON.stringify(chats));
        inp.value = "";
        renderChatCliente();
    }

    // --- ADMIN ABAS ---
    function abaAdmin(tipo) {
        const cont = document.getElementById('adm-content');
        document.getElementById('tab-p').classList.toggle('active-cat', tipo === 'produtos');
        document.getElementById('tab-m').classList.toggle('active-cat', tipo === 'mensagens');
        
        if(tipo === 'produtos') {
            cont.innerHTML = `
                <div style="background:white; padding:15px; border-radius:10px;">
                    <input type="text" id="a-nome" placeholder="Nome" class="input-group"><br>
                    <input type="number" id="a-preco" placeholder="Preço" class="input-group"><br>
                    <select id="a-cat" class="input-group">${CATS.map(c => `<option>${c}</option>`).join('')}</select>
                    <button class="btn" onclick="addProd()">SALVAR</button>
                </div><div id="a-list" style="margin-top:20px"></div>
                <button class="btn" style="background:#333" onclick="logout()">SAIR</button>`;
            renderAdmin();
        } else {
            cont.innerHTML = `<h4>Conversas Ativas</h4><div id="adm-chats-list"></div><button class="btn" style="background:#333" onclick="logout()">SAIR</button>`;
            renderListaChatsAdmin();
        }
    }

    function renderListaChatsAdmin() {
        const list = document.getElementById('adm-chats-list');
        list.innerHTML = Object.keys(chats).map(cpf => {
            const lastMsg = chats[cpf][chats[cpf].length - 1];
            return `<div class="admin-item" onclick="abrirChatAdmin('${cpf}')" style="cursor:pointer">
                <span><b>CPF: ${cpf}</b><br><small>${lastMsg.text.substring(0,20)}...</small></span>
                <span>➔</span>
            </div>`;
        }).join('') || "Nenhuma mensagem.";
    }

    function abrirChatAdmin(cpf) {
        const resp = prompt(`Mensagens de ${cpf}:\n${chats[cpf].map(m => (m.from==='user'?'CLIENTE: ':'EU: ')+m.text).join('\n')}\n\nDigite sua resposta:`);
        if(resp) {
            chats[cpf].push({ from: 'admin', text: resp, hora: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) });
            localStorage.setItem('m_chats', JSON.stringify(chats));
            abaAdmin('mensagens');
        }
    }

    // --- FUNÇÕES BASE (SEM ALTERAÇÃO) ---
    function renderHome(s = "") {
        if(!document.getElementById('cat-loja').innerHTML) setCatLoja(curCat);
        const filtered = db.filter(p => p.cat === curCat && p.nome.toLowerCase().includes(s.toLowerCase()));
        document.getElementById('p-grid').innerHTML = filtered.map(p => `<div class="card"><img src="${p.foto || 'https://via.placeholder.com/80'}"><b>${p.nome}</b><br><span style="color:var(--green)">R$ ${p.preco.toFixed(2)}</span><button class="btn" style="padding:5px" onclick="addToCart(${p.id})">ADD</button></div>`).join('');
    }
    function setCatLoja(c) { curCat = c; document.getElementById('cat-loja').innerHTML = CATS.map(cat => `<div class="cat-item ${cat===curCat?'active-cat':''}" onclick="setCatLoja('${cat}')">${cat}</div>`).join(''); renderHome(); }
    function addToCart(id) { cart.push(db.find(x => x.id === id)); document.getElementById('count').innerText = cart.length; }
    function renderCart() {
        const cL = document.getElementById('c-list');
        if(cart.length===0) { cL.innerHTML="Vazio"; document.getElementById('c-foot').style.display="none"; return; }
        cL.innerHTML = cart.map((p,i) => `<div class="admin-item">${p.nome} <b>R$ ${p.preco.toFixed(2)} <span onclick="remCart(${i})" style="color:red">X</span></b></div>`).join('');
        document.getElementById('t-val').innerText = (cart.reduce((a,b)=>a+b.preco,0)+7).toFixed(2);
        document.getElementById('c-foot').style.display="block";
    }
    function remCart(i) { cart.splice(i,1); renderCart(); document.getElementById('count').innerText = cart.length; }
    function addProd() {
        const n = document.getElementById('a-nome').value, p = parseFloat(document.getElementById('a-preco').value), c = document.getElementById('a-cat').value;
        if(!n || !p) return;
        db.push({id: Date.now(), nome: n, preco: p, cat: c});
        localStorage.setItem('m_db', JSON.stringify(db));
        renderAdmin();
    }
    function renderAdmin(s="") {
        document.getElementById('a-list').innerHTML = db.filter(p=>p.nome.toLowerCase().includes(s.toLowerCase())).map(p=>`<div class="admin-item">${p.nome} (${p.cat}) <button onclick="remProd(${p.id})">X</button></div>`).join('');
    }
    function remProd(id) { db = db.filter(p=>p.id!==id); localStorage.setItem('m_db', JSON.stringify(db)); renderAdmin(); }
    function login() { const cpf = document.getElementById('l-cpf').value, s = localStorage.getItem(`u_${cpf}`); if(s) { user = JSON.parse(s); localStorage.setItem('m_user', JSON.stringify(user)); location.reload(); } else alert("Erro"); }
    function register() { const n = document.getElementById('r-nome').value, c = document.getElementById('r-cpf').value, e = document.getElementById('r-end').value; if(!n||c.length<14) return; localStorage.setItem(`u_${c}`, JSON.stringify({nome:n,cpf:c,end:e})); localStorage.setItem('m_user', JSON.stringify({nome:n,cpf:c,end:e})); location.reload(); }
    function askAdmin() { if(prompt("Senha:")==="123") { localStorage.setItem('m_is_admin','true'); location.reload(); } }
    function logout() { localStorage.clear(); location.reload(); }
    function maskCPF(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2"); i.value = v; }
    function sendZap() { let t = document.getElementById('t-val').innerText; window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=Pedido Medela - Total R$ ${t}`); }
</script>
</body>
</html>
