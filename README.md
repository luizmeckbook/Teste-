<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - V16</title>
    <style>
        :root { --green: #00a859; --orange: #f37021; --gray: #f4f4f4; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); margin: 0; padding: 0; }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; box-sizing: border-box; }
        .header { background: var(--orange); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        
        /* Câmera e Chat */
        .camera-box { width: 100%; background: #000; border-radius: 12px; height: 200px; overflow: hidden; margin-bottom: 10px; border: 2px solid #ddd; }
        #video { width: 100%; height: 100%; object-fit: cover; transform: scaleX(-1); }
        .chat-area { flex: 1; overflow-y: auto; padding: 15px; background: #e5ddd5; display: flex; flex-direction: column; gap: 8px; margin-bottom: 80px; }
        .msg { padding: 10px; border-radius: 10px; max-width: 80%; font-size: 14px; }
        .msg-u { background: #dcf8c6; align-self: flex-end; }
        .msg-a { background: #fff; align-self: flex-start; }
        .msg img { max-width: 100%; border-radius: 5px; margin-top: 5px; display: block; }

        /* Loja */
        .cat-bar { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; scrollbar-width: none; }
        .cat-item { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 11px; font-weight: bold; cursor: pointer; }
        .cat-item.active-cat { background: var(--green); color: white; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 120px; }
        .card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); display: flex; flex-direction: column; justify-content: space-between; }
        .card img { width: 100%; height: 80px; object-fit: contain; }

        .btn { background: var(--green); color: white; border: none; padding: 12px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; }
        .btn-ban { background: #ff4444; color: white; border: none; padding: 5px 10px; border-radius: 5px; font-size: 10px; cursor: pointer; margin-top: 5px; }
        .input-group { margin-bottom: 10px; width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        
        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .nav-btn { font-size: 11px; color: #aaa; border: none; background: none; font-weight: bold; cursor: pointer; }
        .admin-item { background: white; padding: 10px; margin-bottom: 8px; border-radius: 10px; display: flex; align-items: center; border-left: 5px solid var(--orange); }
        .user-img-adm { width: 55px; height: 55px; border-radius: 50%; object-fit: cover; margin-right: 12px; border: 2px solid #ddd; }
    </style>
</head>
<body>

<section id="scr-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <input type="text" id="l-cpf" placeholder="CPF" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="l-pass" placeholder="Senha" class="input-group">
        <button class="btn" onclick="login()">ENTRAR NO MERCADO</button>
        <p style="text-align:center; font-size:13px; margin-top:20px;">Primeira vez? <span style="color:var(--orange); cursor:pointer; font-weight:bold;" onclick="go('scr-reg')">Criar minha Conta</span></p>
        <button class="btn" style="background:#444; margin-top:15px;" onclick="abrirSuporte('visitante')">PRECISO DE AJUDA</button>
        <p style="text-align:center; font-size:10px; color:#ccc; margin-top:60px;" onclick="askAdmin()">ACESSO MESTRE</p>
    </div>
</section>

<section id="scr-reg" class="app-screen">
    <div class="header"><h2>Cadastro de Cliente</h2></div>
    <div class="container">
        <div class="camera-box" id="cam-area"><video id="video" autoplay playsinline></video></div>
        <img id="photo-preview" style="width:100%; border-radius:12px; display:none; margin-bottom:10px; border: 3px solid var(--green);">
        <button id="btn-snap" class="btn" style="background:#444; margin-bottom:10px;" onclick="takeSnap()">TIRAR SELFIE PARA VALIDAR</button>
        
        <input type="text" id="r-nome" placeholder="Nome Completo" class="input-group">
        <input type="text" id="r-cpf" placeholder="CPF" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="r-pass" placeholder="Crie uma Senha" class="input-group">
        <input type="text" id="r-tel" placeholder="WhatsApp" class="input-group" oninput="maskTel(this)" maxlength="15">
        <input type="text" id="r-end" placeholder="Endereço de Entrega" class="input-group">
        <button class="btn" onclick="register()">CADASTRAR E ENTRAR</button>
        <button class="btn" style="background:#888; margin-top:5px;" onclick="go('scr-login')">VOLTAR AO LOGIN</button>
    </div>
</section>

<section id="scr-home" class="app-screen">
    <div class="header">
        <h3 id="u-name" style="margin:0">Olá!</h3>
        <input type="text" placeholder="🔍 O que você procura hoje?" style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div id="cat-loja" class="cat-bar"></div>
    <div id="p-grid" class="grid"></div>
</section>

<section id="scr-suporte" class="app-screen">
    <div class="header"><h2 id="sup-title">Suporte Medela</h2></div>
    <div id="chat-box" class="chat-area"></div>
    <div class="container" style="position:fixed; bottom:60px; background:#f0f0f0; width:100%; max-width:450px; left:50%; transform:translateX(-50%); display:flex; padding:10px; gap:5px;">
        <label style="background:var(--orange); color:white; padding:10px; border-radius:50%; cursor:pointer;">
            📷 <input type="file" accept="image/*" style="display:none" onchange="enviarMsg(true, this)">
        </label>
        <input type="text" id="msg-input" placeholder="Diga sua dúvida..." style="flex:1; border-radius:20px; border:1px solid #ccc; padding:10px;">
        <button onclick="enviarMsg(false)" style="background:var(--green); color:white; border:none; border-radius:50%; width:40px;">➔</button>
    </div>
</section>

<section id="scr-admin" class="app-screen">
    <div class="header"><h2>Painel de Controle</h2></div>
    <div class="cat-bar">
        <div class="cat-item active-cat" id="tab-p" onclick="abaAdmin('produtos')">Estoque</div>
        <div class="cat-item" id="tab-u" onclick="abaAdmin('usuarios')">Nuvem Usuários</div>
        <div class="cat-item" id="tab-s" onclick="abaAdmin('suporte')">Chats</div>
    </div>
    <div class="container" id="adm-content"></div>
</section>

<nav class="nav" id="bot-nav" style="display:none;">
    <button class="nav-btn" onclick="go('scr-home')">MERCADO</button>
    <button class="nav-btn" onclick="abrirSuporte()">SUPORTE</button>
    <button class="nav-btn" onclick="logout()">SAIR</button>
</nav>

<canvas id="canvas" style="display:none;"></canvas>

<script>
    const CATS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria", "Fiambreria"];
    let db = JSON.parse(localStorage.getItem('m_db')) || [];
    let chats = JSON.parse(localStorage.getItem('m_chats')) || {};
    let user = JSON.parse(localStorage.getItem('m_user'));
    let isAdmin = localStorage.getItem('m_is_admin') === 'true';
    let stream = null;
    let fotoRosto = "";

    window.onload = () => {
        if (isAdmin) { go('scr-admin'); abaAdmin('produtos'); }
        else if (user) { go('scr-home'); }
        else { go('scr-login'); }
    };

    function go(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('bot-nav').style.display = (id === 'scr-home' || id === 'scr-suporte') ? 'flex' : 'none';
        
        if(id === 'scr-reg') startCam();
        else if(stream) { stream.getTracks().forEach(t => t.stop()); stream = null; }
        if(id === 'scr-home') renderHome();
    }

    // --- CÂMERA ---
    async function startCam() {
        document.getElementById('cam-area').style.display = 'block';
        document.getElementById('photo-preview').style.display = 'none';
        try { stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } }); document.getElementById('video').srcObject = stream; } 
        catch (e) { alert("Permita a câmera para continuar."); }
    }
    function takeSnap() {
        const v = document.getElementById('video'); const c = document.getElementById('canvas');
        c.width = v.videoWidth; c.height = v.videoHeight;
        c.getContext('2d').drawImage(v, 0, 0);
        fotoRosto = c.toDataURL('image/jpeg');
        document.getElementById('photo-preview').src = fotoRosto;
        document.getElementById('photo-preview').style.display = 'block';
        document.getElementById('cam-area').style.display = 'none';
        if(stream) stream.getTracks().forEach(t => t.stop());
    }

    // --- LOJA ---
    function setCatLoja(c) { curCat = c; document.getElementById('cat-loja').innerHTML = CATS.map(cat => `<div class="cat-item ${cat===curCat?'active-cat':''}" onclick="setCatLoja('${cat}')">${cat}</div>`).join(''); renderHome(); }
    function renderHome(s = "") {
        if(!document.getElementById('cat-loja').innerHTML) setCatLoja("Mercearia");
        document.getElementById('u-name').innerText = "Olá, " + user.nome.split(' ')[0];
        const filtered = db.filter(p => p.cat === (window.curCat || "Mercearia") && p.nome.toLowerCase().includes(s.toLowerCase()));
        document.getElementById('p-grid').innerHTML = filtered.map(p => `
            <div class="card">
                <img src="${p.foto || 'https://via.placeholder.com/80'}">
                <b style="font-size:11px;">${p.nome}</b>
                <span style="color:var(--green); font-size:13px;">R$ ${p.preco.toFixed(2)} / ${p.tipo}</span>
                <button class="btn" style="margin-top:5px; padding:5px; font-size:10px;" onclick="alert('Funcionalidade de compra V15 Gold ativa')">ADICIONAR</button>
            </div>`).join('');
    }

    // --- ADMIN NUVEM ---
    function abaAdmin(tipo) {
        const cont = document.getElementById('adm-content');
        if(tipo === 'usuarios') {
            cont.innerHTML = `<input type="text" placeholder="🔍 Buscar nome ou CPF..." class="input-group" oninput="renderUsersAdm(this.value)"><div id="list-u"></div>`;
            renderUsersAdm();
        } else if(tipo === 'suporte') {
            cont.innerHTML = `<div id="list-s"></div>`;
            document.getElementById('list-s').innerHTML = Object.keys(chats).map(id => `<div class="admin-item" onclick="respChat('${id}')">Conversa com: ${id} ➔</div>`).join('') || "Sem mensagens.";
        } else {
            cont.innerHTML = `<h4>Estoque</h4><button class="btn" onclick="sairAdmin()">SAIR DO ADM</button>`;
        }
    }
    function renderUsersAdm(f = "") {
        let html = "";
        for (let i = 0; i < localStorage.length; i++) {
            let k = localStorage.key(i);
            if(k.startsWith("u_")) {
                let u = JSON.parse(localStorage.getItem(k));
                if(u.nome.toLowerCase().includes(f.toLowerCase()) || u.cpf.includes(f)) {
                    html += `<div class="admin-item">
                        <img src="${u.rosto}" class="user-img-adm">
                        <div style="flex:1; font-size:12px;">
                            <b>${u.nome}</b><br>Senha: <b>${u.senha}</b><br>
                            <button class="btn-ban" onclick="banir('${u.cpf}')">BANIR USUÁRIO</button>
                        </div>
                    </div>`;
                }
            }
        }
        document.getElementById('list-u').innerHTML = html || "Nenhum resultado.";
    }
    function banir(cpf) { if(confirm("Deseja realmente apagar e banir este cliente?")) { localStorage.removeItem('u_'+cpf); renderUsersAdm(); } }

    // --- SISTEMA ---
    function login() {
        const c = document.getElementById('l-cpf').value, p = document.getElementById('l-pass').value;
        const s = localStorage.getItem(`u_${c}`);
        if(s && JSON.parse(s).senha === p) { user = JSON.parse(s); localStorage.setItem('m_user', s); go('scr-home'); }
        else alert("CPF ou Senha incorretos!");
    }
    function register() {
        const c = document.getElementById('r-cpf').value, p = document.getElementById('r-pass').value;
        if(!fotoRosto || !c || !p) return alert("Tire a foto e preencha os dados!");
        const d = {nome:document.getElementById('r-nome').value, cpf:c, senha:p, rosto:fotoRosto, tel:document.getElementById('r-tel').value};
        localStorage.setItem(`u_${c}`, JSON.stringify(d)); user = d; localStorage.setItem('m_user', JSON.stringify(d)); go('scr-home');
    }
    function maskCPF(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2"); i.value = v; }
    function askAdmin() { if(prompt("Senha Mestra:")==="123") { localStorage.setItem('m_is_admin','true'); location.reload(); } }
    function logout() { localStorage.removeItem('m_user'); location.reload(); }
    function sairAdmin() { localStorage.removeItem('m_is_admin'); location.reload(); }
</script>
</body>
</html>
