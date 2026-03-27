<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - FIX TELA BRANCA</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg-gray); margin: 0; color: var(--text-dark); overflow-x: hidden; }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        .header-orange { background: var(--orange-header); padding: 15px 20px; color: white; border-radius: 0 0 20px 20px; text-align: center; position: sticky; top:0; z-index:100; }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 1000; left: 50%; transform: translateX(-50%); }
        .nav-item { font-size: 11px; color: #aaa; text-align: center; border: none; background: none; font-weight: bold; cursor: pointer; }
        .cat-container { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; }
        .cat-pill { padding: 8px 16px; background: #f0f0f0; border-radius: 20px; font-size: 12px; font-weight: bold; cursor: pointer; border: 1px solid #ddd; }
        .cat-pill.active-cat { background: var(--primary-green); color: white; border-color: var(--primary-green); }
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 150px; }
        .item-card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .input-group { margin-bottom: 12px; text-align: left; }
        .input-group input, .input-group select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; }
        .payment-methods { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin: 10px 0; }
        .pay-btn { padding: 10px; border: 1px solid #ddd; border-radius: 8px; background: #fff; cursor: pointer; font-size: 12px; }
        .pay-btn.selected { background: #e8f5e9; border-color: var(--primary-green); color: var(--primary-green); font-weight: bold; }
        .info-box { background: #fff; padding: 15px; border-radius: 10px; border: 1px solid #ddd; margin-top: 10px; display: none; }
    </style>
</head>
<body>

<section id="screen-login" class="app-screen active">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--primary-green); margin:0; font-size: 40px;">MEDELA</h1>
            <p style="color:var(--orange-header); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <div class="input-group">
            <input type="text" id="login-cpf" placeholder="Seu CPF (000.000.000-00)" oninput="mascaraCPF(this)" maxlength="14">
        </div>
        <button class="btn-green" onclick="fazerLogin()">ENTRAR</button>
        <p style="text-align:center; margin-top:20px;">Novo? <b style="color:var(--orange-header); cursor:pointer" onclick="irPara('screen-register')">Cadastre-se</b></p>
        <p style="text-align:center; margin-top:40px; font-size:11px; color:#ccc; cursor:pointer;" onclick="acessoAdmin()">⚙️ ADMINISTRAÇÃO</p>
    </div>
</section>

<section id="screen-register" class="app-screen">
    <div class="header-orange"><h2>Cadastro</h2></div>
    <div class="container">
        <div class="input-group"><input type="text" id="reg-nome" placeholder="Nome Completo"></div>
        <div class="input-group"><input type="text" id="reg-cpf" placeholder="CPF" oninput="mascaraCPF(this)" maxlength="14"></div>
        <div class="input-group"><input type="text" id="reg-end" placeholder="Endereço de Entrega"></div>
        <button class="btn-green" onclick="salvarCadastro()">CADASTRAR E ENTRAR</button>
        <button class="btn-green" style="background:#888; margin-top:10px;" onclick="irPara('screen-login')">VOLTAR</button>
    </div>
</section>

<section id="screen-home" class="app-screen">
    <div class="header-orange">
        <h3 id="user-display" style="margin:0">Olá!</h3>
        <input type="text" placeholder="Buscar produto..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="cat-container" id="cat-list"></div>
    <div class="market-grid" id="product-grid"></div>
</section>

<section id="screen-cart" class="app-screen">
    <div class="header-orange"><h2>Meu Carrinho</h2></div>
    <div class="container" id="cart-items-list" style="padding-bottom: 300px;"></div>
    
    <div id="cart-footer" class="container" style="position:fixed; bottom:60px; background:white; width:100%; border-top:2px solid #eee; display:none; left:50%; transform:translateX(-50%); z-index: 500;">
        <h3 style="color:var(--primary-green); margin:10px 0;">Total: R$ <span id="total-val">0,00</span></h3>
        <div class="payment-methods">
            <button class="pay-btn" id="btn-pix" onclick="setMetodo('pix')">PIX</button>
            <button class="pay-btn" id="btn-credito" onclick="setMetodo('credito')">CRÉDITO</button>
            <button class="pay-btn" id="btn-debito" onclick="setMetodo('debito')">DÉBITO</button>
            <button class="pay-btn" id="btn-dinheiro" onclick="setMetodo('dinheiro')">DINHEIRO</button>
        </div>
        <div id="info-pix" class="info-box">Chave: <b id="chave-pix-txt"></b></div>
        <div id="info-credito" class="info-box">Parcelas: <select id="select-parcelas" style="width:100%"></select></div>
        <div id="info-dinheiro" class="info-box"><input type="number" id="troco-input" placeholder="Troco para quanto?"></div>
        <button class="btn-green" style="margin-top:10px; background:#25d366" onclick="enviarWhatsApp()">FINALIZAR WHATSAPP</button>
    </div>
</section>

<section id="screen-admin" class="app-screen">
    <div class="header-orange"><h2>Painel Admin</h2></div>
    <div class="container" style="padding-bottom: 50px;">
        <input type="text" id="adm-chave-pix" placeholder="Chave Pix da Loja" style="width:100%; padding:10px; margin-bottom:10px;">
        <button class="btn-green" style="padding:10px; margin-bottom:20px" onclick="salvarConfig()">SALVAR CONFIGURAÇÕES</button>
        <hr>
        <div class="input-group"><input type="text" id="adm-nome" placeholder="Nome do Produto"></div>
        <div class="input-group"><select id="adm-cat-select"></select></div>
        <div class="input-group"><select id="adm-tipo"><option value="un">Unidade (UN)</option><option value="kg">Quilo (KG)</option></select></div>
        <div class="input-group"><input type="number" id="adm-preco" placeholder="Preço (Ex: 10.50)"></div>
        <button class="btn-green" onclick="admAdicionar()">ADICIONAR PRODUTO</button>
        <div id="adm-lista-geral" style="margin-top:20px"></div>
        <button class="btn-green" style="background:#333; margin-top:20px" onclick="sairAdmin()">SAIR DO PAINEL</button>
    </div>
</section>

<nav class="bottom-nav" id="main-nav" style="display:none;">
    <button class="nav-item" onclick="irPara('screen-home')">🛒 LOJA</button>
    <button class="nav-item" onclick="irPara('screen-cart')">🛍️ CESTA (<span id="count">0</span>)</button>
    <button class="nav-item" onclick="logout()">🚪 SAIR</button>
</nav>

<script>
    const SENHA_MESTRA = "123";
    const CATEGORIAS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria", "Fiambreria"];
    
    let estoque = JSON.parse(localStorage.getItem('medela_estoque')) || [];
    let carrinho = [];
    let userAtivo = JSON.parse(localStorage.getItem('medela_user'));
    let isAdmin = localStorage.getItem('admin_session') === 'true';
    let chavePix = localStorage.getItem('medela_pix') || "Não definida";
    let catAtual = CATEGORIAS[0];
    let metodoAtivo = "";

    // INICIALIZAÇÃO - CORREÇÃO DE TELA BRANCA
    window.onload = () => {
        document.getElementById('adm-cat-select').innerHTML = CATEGORIAS.map(c => `<option value="${c}">${c}</option>`).join('');
        document.getElementById('adm-chave-pix').value = chavePix;
        
        if (isAdmin) {
            irPara('screen-admin');
        } else if (userAtivo && userAtivo.cpf) {
            logarInterface();
            irPara('screen-home');
        } else {
            irPara('screen-login');
        }
    };

    function irPara(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        const target = document.getElementById(id);
        if(target) target.classList.add('active');
        
        document.getElementById('main-nav').style.display = (id === 'screen-home' || id === 'screen-cart') ? 'flex' : 'none';
        
        if(id === 'screen-home') renderHome();
        if(id === 'screen-cart') renderCart();
        if(id === 'screen-admin') renderAdmin();
        window.scrollTo(0,0);
    }

    // --- FUNÇÕES DE USUÁRIO ---
    function fazerLogin() {
        const cpf = document.getElementById('login-cpf').value;
        const dados = localStorage.getItem(`c_${cpf}`);
        if(dados) {
            userAtivo = JSON.parse(dados);
            localStorage.setItem('medela_user', JSON.stringify(userAtivo));
            logarInterface();
            irPara('screen-home');
        } else { alert("CPF não cadastrado!"); }
    }

    function salvarCadastro() {
        const n = document.getElementById('reg-nome').value;
        const c = document.getElementById('reg-cpf').value;
        const e = document.getElementById('reg-end').value;
        if(!n || c.length < 14) return alert("Preencha tudo!");
        userAtivo = {nome: n, cpf: c, end: e};
        localStorage.setItem(`c_${c}`, JSON.stringify(userAtivo));
        localStorage.setItem('medela_user', JSON.stringify(userAtivo));
        logarInterface();
        irPara('screen-home');
    }

    function logarInterface() { 
        if(userAtivo) document.getElementById('user-display').innerText = `Olá, ${userAtivo.nome.split(' ')[0]}!`;
    }

    // --- LOJA ---
    function renderHome(busca = "") {
        const containerCat = document.getElementById('cat-list');
        containerCat.innerHTML = CATEGORIAS.map(c => `<div class="cat-pill ${catAtual===c?'active-cat':''}" onclick="setCat('${c}')">${c}</div>`).join('');
        
        const grid = document.getElementById('product-grid');
        const lista = estoque.filter(p => p.cat === catAtual && p.n.toLowerCase().includes(busca.toLowerCase()));
        
        grid.innerHTML = lista.map(p => `
            <div class="item-card">
                <b style="font-size:13px">${p.n}</b><br>
                <span style="color:var(--primary-green); font-weight:bold">R$ ${p.p.toFixed(2)}</span>
                <div style="margin:5px 0"><input type="number" id="q-${p.id}" value="1" style="width:40px"> <small>${p.tipo}</small></div>
                <button onclick="addCart(${p.id})" style="background:var(--orange-header); color:white; border:none; padding:5px; border-radius:5px; width:100%; cursor:pointer">ADICIONAR</button>
            </div>
        `).join('');
    }

    function setCat(c) { catAtual = c; renderHome(); }

    function addCart(id) {
        const p = estoque.find(x => x.id === id);
        const q = parseFloat(document.getElementById(`q-${id}`).value);
        carrinho.push({...p, qtd: q, sub: p.p * q});
        document.getElementById('count').innerText = carrinho.length;
        alert("Adicionado!");
    }

    // --- CARRINHO E PAGAMENTO ---
    function renderCart() {
        const container = document.getElementById('cart-items-list');
        if(carrinho.length === 0) {
            container.innerHTML = "<p style='text-align:center; margin-top:50px'>Carrinho vazio.</p>";
            document.getElementById('cart-footer').style.display = "none";
            return;
        }
        let total = carrinho.reduce((acc, i) => acc + i.sub, 0) + 7; // Taxa de entrega fixa
        container.innerHTML = carrinho.map((it, idx) => `
            <div style="background:white; padding:10px; margin-bottom:5px; border-radius:8px; display:flex; justify-content:space-between">
                <span>${it.n} (${it.qtd}${it.tipo})</span>
                <b>R$ ${it.sub.toFixed(2)} <span onclick="remCart(${idx})" style="color:red; margin-left:10px">X</span></b>
            </div>
        `).join('');
        document.getElementById('total-val').innerText = total.toFixed(2);
        document.getElementById('cart-footer').style.display = "block";
    }

    function setMetodo(m) {
        metodoAtivo = m;
        document.querySelectorAll('.pay-btn').forEach(b => b.classList.remove('selected'));
        document.getElementById(`btn-${m}`).classList.add('selected');
        document.querySelectorAll('.info-box').forEach(i => i.style.display = 'none');
        
        if(m === 'pix') {
            document.getElementById('info-pix').style.display = 'block';
            document.getElementById('chave-pix-txt').innerText = chavePix;
        } else if(m === 'credito') {
            document.getElementById('info-credito').style.display = 'block';
            const sel = document.getElementById('select-parcelas');
            const total = parseFloat(document.getElementById('total-val').innerText);
            sel.innerHTML = "";
            for(let i=1; i<=12; i++) {
                let juros = i === 1 ? 1 : (1 + (0.0398 * (i-1)));
                let valorFinal = total * juros;
                sel.innerHTML += `<option value="${i}">${i}x de R$ ${(valorFinal/i).toFixed(2)} (Total R$ ${valorFinal.toFixed(2)})</option>`;
            }
        } else if(m === 'dinheiro') {
            document.getElementById('info-dinheiro').style.display = 'block';
        }
    }

    function remCart(i) { carrinho.splice(i,1); renderCart(); document.getElementById('count').innerText = carrinho.length; }

    function enviarWhatsApp() {
        if(!metodoAtivo) return alert("Escolha o pagamento!");
        let totalFinal = document.getElementById('total-val').innerText;
        let detPag = `Metodo: ${metodoAtivo.toUpperCase()}`;
        
        if(metodoAtivo === 'credito') detPag += ` - ${document.getElementById('select-parcelas').value}x no Cartão`;
        if(metodoAtivo === 'dinheiro') detPag += ` - Troco para R$ ${document.getElementById('troco-input').value}`;

        let msg = `*PEDIDO - MEDELA*\nCliente: ${userAtivo.nome}\nEndereço: ${userAtivo.end}\n\n`;
        carrinho.forEach(i => msg += `- ${i.n} (${i.qtd}${i.tipo}): R$ ${i.sub.toFixed(2)}\n`);
        msg += `\n*TOTAL COM TAXA: R$ ${totalFinal}*\n*${detPag}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    // --- ADMIN ---
    function acessoAdmin() {
        if(prompt("Senha:") === SENHA_MESTRA) {
            localStorage.setItem('admin_session', 'true');
            isAdmin = true;
            irPara('screen-admin');
        } else alert("Senha incorreta");
    }

    function sairAdmin() {
        localStorage.removeItem('admin_session');
        isAdmin = false;
        location.reload();
    }

    function salvarConfig() {
        chavePix = document.getElementById('adm-chave-pix').value;
        localStorage.setItem('medela_pix', chavePix);
        alert("Configuração Salva!");
    }

    function admAdicionar() {
        const n = document.getElementById('adm-nome').value;
        const c = document.getElementById('adm-cat-select').value;
        const t = document.getElementById('adm-tipo').value;
        const p = parseFloat(document.getElementById('adm-preco').value);
        if(!n || !p) return alert("Preencha tudo");
        estoque.push({id: Date.now(), n, cat: c, tipo: t, p});
        localStorage.setItem('medela_estoque', JSON.stringify(estoque));
        renderAdmin();
    }

    function renderAdmin() {
        const lista = document.getElementById('adm-lista-geral');
        lista.innerHTML = estoque.map(p => `
            <div style="background:#fff; padding:10px; margin-bottom:5px; border-radius:5px; display:flex; justify-content:space-between">
                <span>${p.n} (${p.cat})</span>
                <button onclick="admRemover(${p.id})" style="background:red; color:white; border:none; border-radius:4px">X</button>
            </div>
        `).join('');
    }

    function admRemover(id) {
        estoque = estoque.filter(p => p.id !== id);
        localStorage.setItem('medela_estoque', JSON.stringify(estoque));
        renderAdmin();
    }

    // --- UTIL ---
    function mascaraCPF(i) {
        let v = i.value.replace(/\D/g, "").replace(/(\d{3})(\d)/, "$1.$2").replace(/(\d{3})(\d)/, "$1.$2").replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }
    function logout() { localStorage.removeItem('medela_user'); location.reload(); }
</script>
</body>
</html>
