<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - ADMIN</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; color: var(--text-dark); }
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; flex-direction: column; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* HEADER & NAV */
        .header-orange { background: var(--orange-header); padding: 20px; color: white; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 1000; left: 50%; transform: translateX(-50%); }
        .nav-item { font-size: 11px; color: #aaa; text-align: center; border: none; background: none; font-weight: bold; cursor: pointer; }
        .nav-active { color: var(--primary-green); }

        /* INPUTS & BUTTONS */
        .input-group { margin-bottom: 15px; }
        .input-group label { display: block; font-size: 12px; font-weight: bold; margin-bottom: 5px; }
        .input-group input, .input-group select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 10px; box-sizing: border-box; }
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; }
        .btn-add { background: var(--orange-header); color: white; border: none; width: 100%; padding: 8px; border-radius: 6px; cursor: pointer; margin-top: 10px; font-weight: bold; }

        /* GRID PRODUTOS */
        .category-scroll { display: flex; overflow-x: auto; padding: 10px; gap: 10px; background: #fff; white-space: nowrap; }
        .cat-pill { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 12px; cursor: pointer; }
        .cat-active { background: var(--primary-green); color: white; }
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 15px; padding-bottom: 120px; }
        .item-card { background: white; border-radius: 12px; padding: 12px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }

        /* ADMIN SPECIFIC */
        .admin-item { background: white; padding: 10px; margin-bottom: 8px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange-header); }
        .btn-del { background: #ff4d4d; color: white; border: none; padding: 5px 10px; border-radius: 5px; cursor: pointer; }
    </style>
</head>
<body>

<section id="screen-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--primary-green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange-header); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <div class="input-group"><label>CPF</label><input type="text" id="login-cpf" placeholder="000.000.000-00"></div>
        <div class="input-group"><label>Senha</label><input type="password" id="login-pass"></div>
        <button class="btn-green" onclick="fazerLogin()">ENTRAR</button>
        <p style="text-align:center; margin-top:20px; font-size:14px;">Não tem conta? <b onclick="irPara('screen-register')" style="color:var(--primary-green); cursor:pointer;">Cadastre-se</b></p>
        <p style="text-align:center; margin-top:40px; font-size:12px; color:#aaa; cursor:pointer;" onclick="acessoAdmin()">Acesso Administrativo</p>
    </div>
</section>

<section id="screen-register" class="app-screen">
    <div class="container">
        <h2>Criar Conta</h2>
        <div class="input-group"><label>Nome Completo</label><input type="text" id="reg-nome"></div>
        <div class="input-group"><label>CPF</label><input type="text" id="reg-cpf" oninput="mascaraCPF(this)"></div>
        <div class="input-group"><label>Endereço Completo</label><input type="text" id="reg-end"></div>
        <div class="input-group"><label>Senha</label><input type="password" id="reg-pass"></div>
        <button class="btn-green" onclick="salvarCadastro()">CADASTRAR</button>
        <p style="text-align:center; margin-top:20px;" onclick="irPara('screen-login')">Voltar ao login</p>
    </div>
</section>

<section id="screen-home" class="app-screen">
    <div class="header-orange">
        <h3 id="user-display" style="margin:0">Olá!</h3>
        <input type="text" placeholder="Buscar produto..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="category-scroll" id="cat-list"></div>
    <div class="market-grid" id="product-grid"></div>
</section>

<section id="screen-cart" class="app-screen">
    <div class="header-orange"><h2>Meu Pedido</h2></div>
    <div class="container" style="padding-bottom: 180px;">
        <div id="cart-items-list"></div>
    </div>
    <div id="cart-footer" class="container" style="position:fixed; bottom:60px; background:white; width:100%; border-top:2px solid #eee; display:none; left:50%; transform:translateX(-50%);">
        <p>Subtotal: <span id="sub-val" style="float:right">0,00</span></p>
        <p>Entrega: <span style="float:right">R$ 7,00</span></p>
        <h3 style="color:var(--primary-green)">TOTAL: <span id="total-val" style="float:right">0,00</span></h3>
        <button class="btn-green" onclick="enviarWhatsApp()">FINALIZAR WHATSAPP</button>
    </div>
</section>

<section id="screen-admin" class="app-screen">
    <div class="header-orange"><h2>Painel Administrativo</h2></div>
    <div class="container">
        <div style="background: #fff; padding: 15px; border-radius: 10px; margin-bottom: 20px; border: 1px solid #ddd;">
            <h4>Novo Produto</h4>
            <div class="input-group"><input type="text" id="adm-nome" placeholder="Nome do Produto"></div>
            <div class="input-group">
                <select id="adm-cat">
                    <option>Mercearia Seca</option><option>Hortifrúti</option>
                    <option>Açougue</option><option>Bebidas</option><option>Limpeza</option>
                </select>
            </div>
            <div class="input-group"><input type="number" id="adm-preco" placeholder="Preço (Ex: 10.50)"></div>
            <button class="btn-green" onclick="admAdicionar()">SALVAR PRODUTO</button>
        </div>
        <div id="adm-lista-geral"></div>
        <button class="btn-add" style="background:#333; margin-top:20px" onclick="irPara('screen-login')">SAIR DO PAINEL</button>
    </div>
</section>

<nav class="bottom-nav" id="main-nav" style="display:none;">
    <button class="nav-item" onclick="irPara('screen-home')">LOJA</button>
    <button class="nav-item" onclick="irPara('screen-cart')">CARRINHO (<span id="count">0</span>)</button>
    <button class="nav-item" onclick="logout()">SAIR</button>
</nav>

<script>
    const SENHA_MESTRA = "admin123"; // SENHA ÚNICA DO ADM
    const TAXA = 7.00;
    
    // Dados iniciais se o banco estiver vazio
    const estoquePadrao = [
        { id: 1, cat: "Mercearia Seca", n: "Arroz 5kg", p: 25.90 },
        { id: 2, cat: "Açougue", n: "Picanha KG", p: 69.90 },
        { id: 3, cat: "Bebidas", n: "Coca-Cola 2L", p: 10.50 }
    ];

    let estoque = JSON.parse(localStorage.getItem('medela_estoque')) || estoquePadrao;
    let carrinho = JSON.parse(localStorage.getItem('medela_cart')) || [];
    let user = JSON.parse(localStorage.getItem('medela_user_ativo'));
    let catAtual = "Mercearia Seca";

    // Iniciar App
    window.onload = () => {
        if(user) { logarInterface(); irPara('screen-home'); } 
        else { irPara('screen-login'); }
        if(!localStorage.getItem('medela_estoque')) salvarEstoque();
    };

    function salvarEstoque() { localStorage.setItem('medela_estoque', JSON.stringify(estoque)); }

    function irPara(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('main-nav').style.display = (id === 'screen-home' || id === 'screen-cart') ? 'flex' : 'none';
        if(id === 'screen-home') renderHome();
        if(id === 'screen-cart') renderCart();
        if(id === 'screen-admin') renderAdmin();
    }

    // --- FUNÇÕES DE USUÁRIO ---
    function salvarCadastro() {
        const nome = document.getElementById('reg-nome').value;
        const cpf = document.getElementById('reg-cpf').value;
        const end = document.getElementById('reg-end').value;
        const pass = document.getElementById('reg-pass').value;
        if(!nome || !cpf) return alert("Preencha os dados!");
        localStorage.setItem(cpf, JSON.stringify({nome, cpf, end, pass}));
        alert("Cadastrado com sucesso!");
        irPara('screen-login');
    }

    function fazerLogin() {
        const cpf = document.getElementById('login-cpf').value;
        const pass = document.getElementById('login-pass').value;
        const salvo = JSON.parse(localStorage.getItem(cpf));
        if(salvo && salvo.pass === pass) {
            user = salvo;
            localStorage.setItem('medela_user_ativo', JSON.stringify(user));
            logarInterface();
            irPara('screen-home');
        } else { alert("Dados incorretos!"); }
    }

    function logarInterface() {
        document.getElementById('user-display').innerText = "Olá, " + user.nome.split(' ')[0] + "!";
        updateBadge();
    }

    // --- LÓGICA DA LOJA ---
    function renderHome(busca = "") {
        const cats = [...new Set(estoque.map(i => i.cat))];
        document.getElementById('cat-list').innerHTML = cats.map(c => `
            <div class="cat-pill ${catAtual === c ? 'cat-active' : ''}" onclick="setCat('${c}')">${c}</div>
        `).join('');

        const grid = document.getElementById('product-grid');
        const filtrados = estoque.filter(p => 
            p.cat === catAtual && p.n.toLowerCase().includes(busca.toLowerCase())
        );

        grid.innerHTML = filtrados.map(p => `
            <div class="item-card">
                <div style="height:80px; background:#f9f9f9; border-radius:8px; margin-bottom:10px; display:flex; align-items:center; justify-content:center; font-size:24px;">📦</div>
                <b>${p.n}</b>
                <span style="display:block; color:var(--primary-green); font-weight:bold">R$ ${p.p.toFixed(2)}</span>
                <button class="btn-add" onclick="addAoCarrinho(${p.id})">ADICIONAR</button>
            </div>
        `).join('');
    }

    function setCat(c) { catAtual = c; renderHome(); }

    function addAoCarrinho(id) {
        const p = estoque.find(x => x.id === id);
        carrinho.push(p);
        localStorage.setItem('medela_cart', JSON.stringify(carrinho));
        updateBadge();
        alert("Adicionado!");
    }

    function updateBadge() { document.getElementById('count').innerText = carrinho.length; }

    function renderCart() {
        const container = document.getElementById('cart-items-list');
        if(carrinho.length === 0) {
            container.innerHTML = "<p style='text-align:center'>Carrinho vazio</p>";
            document.getElementById('cart-footer').style.display = "none";
            return;
        }
        let sub = 0;
        container.innerHTML = carrinho.map((it, i) => {
            sub += it.p;
            return `<div class="admin-item"><span>${it.n} - R$ ${it.p.toFixed(2)}</span> <button class="btn-del" onclick="remDoCarrinho(${i})">X</button></div>`;
        }).join('');
        
        document.getElementById('sub-val').innerText = "R$ " + sub.toFixed(2);
        document.getElementById('total-val').innerText = "R$ " + (sub + TAXA).toFixed(2);
        document.getElementById('cart-footer').style.display = "block";
    }

    function remDoCarrinho(i) {
        carrinho.splice(i, 1);
        localStorage.setItem('medela_cart', JSON.stringify(carrinho));
        renderCart();
        updateBadge();
    }

    // --- ÁREA ADMINISTRATIVA ---
    function acessoAdmin() {
        const p = prompt("Digite a senha administrativa:");
        if(p === SENHA_MESTRA) {
            irPara('screen-admin');
        } else {
            alert("Senha incorreta!");
        }
    }

    function renderAdmin() {
        const lista = document.getElementById('adm-lista-geral');
        lista.innerHTML = "<h4>Produtos Cadastrados</h4>";
        estoque.forEach((p, index) => {
            lista.innerHTML += `
                <div class="admin-item">
                    <div><b>${p.n}</b><br><small>${p.cat} - R$ ${p.p.toFixed(2)}</small></div>
                    <button class="btn-del" onclick="admRemover(${index})">Excluir</button>
                </div>
            `;
        });
    }

    function admAdicionar() {
        const n = document.getElementById('adm-nome').value;
        const c = document.getElementById('adm-cat').value;
        const p = parseFloat(document.getElementById('adm-preco').value);

        if(!n || !p) return alert("Preencha tudo!");

        estoque.push({ id: Date.now(), cat: c, n: n, p: p });
        salvarEstoque();
        renderAdmin();
        alert("Produto salvo!");
    }

    function admRemover(index) {
        if(confirm("Deseja remover este produto?")) {
            estoque.splice(index, 1);
            salvarEstoque();
            renderAdmin();
        }
    }

    function enviarWhatsApp() {
        let msg = `*MEDELA SUPERMERCADO*\n\n`;
        msg += `*CLIENTE:* ${user.nome}\n*ENDEREÇO:* ${user.end}\n\n`;
        carrinho.forEach(i => msg += `- ${i.n}: R$ ${i.p.toFixed(2)}\n`);
        msg += `\n*TOTAL (c/ taxa): ${document.getElementById('total-val').innerText}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    function logout() { localStorage.removeItem('medela_user_ativo'); location.reload(); }
    function mascaraCPF(i) {
        let v = i.value.replace(/\D/g, "");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }
</script>
</body>
</html>
