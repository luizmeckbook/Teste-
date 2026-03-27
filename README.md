<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - V2</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg-gray); margin: 0; color: var(--text-dark); }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* HEADER */
        .header-orange { background: var(--orange-header); padding: 20px; color: white; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 1000; left: 50%; transform: translateX(-50%); }
        .nav-item { font-size: 11px; color: #aaa; text-align: center; border: none; background: none; font-weight: bold; cursor: pointer; }
        
        /* ESTILOS DE COMPONENTES */
        .input-group { margin-bottom: 12px; }
        .input-group label { display: block; font-size: 11px; font-weight: bold; margin-bottom: 4px; }
        .input-group input, .input-group select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; }
        
        /* GRID & CARDS */
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 150px; }
        .item-card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .qty-selector { display: flex; align-items: center; justify-content: center; gap: 5px; margin-top: 8px; }
        .qty-input { width: 60px !important; padding: 5px !important; text-align: center; }

        /* ADMIN */
        .admin-item { background: white; padding: 10px; margin-bottom: 8px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange-header); }
        .badge-type { font-size: 10px; padding: 2px 5px; background: #eee; border-radius: 4px; }
    </style>
</head>
<body>

<section id="screen-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--primary-green); margin:0; font-size: 40px;">MEDELA</h1>
            <p style="color:var(--orange-header); font-weight:bold; letter-spacing: 2px;">SUPERMERCADO</p>
        </div>
        <div class="input-group"><label>CPF</label><input type="text" id="login-cpf" placeholder="000.000.000-00" oninput="mascaraCPF(this)"></div>
        <div class="input-group"><label>Senha</label><input type="password" id="login-pass"></div>
        <button class="btn-green" onclick="fazerLogin()">ENTRAR</button>
        <p style="text-align:center; margin-top:30px; font-size:12px; color:#999; cursor:pointer;" onclick="acessoAdmin()">⚙️ ACESSO RESTRITO ADM</p>
    </div>
</section>

<section id="screen-home" class="app-screen">
    <div class="header-orange">
        <h3 id="user-display" style="margin:0">Olá!</h3>
        <input type="text" placeholder="Pesquisar na loja..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div style="display:flex; overflow-x:auto; padding:10px; gap:8px; background:white;" id="cat-list"></div>
    <div class="market-grid" id="product-grid"></div>
</section>

<section id="screen-cart" class="app-screen">
    <div class="header-orange"><h2>Meu Carrinho</h2></div>
    <div class="container" style="padding-bottom: 200px;" id="cart-items-list"></div>
    <div id="cart-footer" class="container" style="position:fixed; bottom:60px; background:white; width:100%; border-top:2px solid #eee; display:none; left:50%; transform:translateX(-50%);">
        <h3 style="color:var(--primary-green); margin-bottom:10px;">TOTAL: <span id="total-val" style="float:right">0,00</span></h3>
        <button class="btn-green" onclick="enviarWhatsApp()">PEDIR NO WHATSAPP</button>
    </div>
</section>

<section id="screen-admin" class="app-screen">
    <div class="header-orange"><h2>Painel ADM</h2></div>
    <div class="container">
        <div style="background: #fff; padding: 15px; border-radius: 10px; margin-bottom: 20px; border: 1px solid #ddd;">
            <h4 style="margin-top:0">Cadastrar Produto</h4>
            <div class="input-group"><input type="text" id="adm-nome" placeholder="Ex: Alcatra ou Arroz"></div>
            <div class="input-group">
                <select id="adm-tipo">
                    <option value="un">Venda por Unidade (UN)</option>
                    <option value="kg">Venda por Quilo (KG)</option>
                </select>
            </div>
            <div class="input-group"><input type="number" id="adm-preco" placeholder="Preço (Ex: 35.90)"></div>
            <button class="btn-green" onclick="admAdicionar()">ADICIONAR AO ESTOQUE</button>
        </div>

        <div class="input-group">
            <label>Pesquisar e Apagar:</label>
            <input type="text" placeholder="🔍 Buscar produto no estoque..." oninput="renderAdmin(this.value)">
        </div>
        <div id="adm-lista-geral"></div>
        <button class="btn-green" style="background:#333; margin-top:20px" onclick="location.reload()">SAIR DO ADM</button>
    </div>
</section>

<nav class="bottom-nav" id="main-nav" style="display:none;">
    <button class="nav-item" onclick="irPara('screen-home')">PRODUTOS</button>
    <button class="nav-item" onclick="irPara('screen-cart')">CARRINHO (<span id="count">0</span>)</button>
    <button class="nav-item" onclick="logout()">SAIR</button>
</nav>

<script>
    const SENHA_MESTRA = "123"; 
    let estoque = JSON.parse(localStorage.getItem('medela_estoque_v2')) || [
        { id: 1, n: "Arroz 5kg", p: 26.50, tipo: "un", cat: "Mercearia" },
        { id: 2, n: "Alcatra", p: 42.90, tipo: "kg", cat: "Açougue" }
    ];
    let carrinho = [];
    let user = JSON.parse(localStorage.getItem('medela_user_ativo'));

    window.onload = () => {
        if(user) { logarInterface(); irPara('screen-home'); } 
        else { irPara('screen-login'); }
    };

    function irPara(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('main-nav').style.display = (id === 'screen-home' || id === 'screen-cart') ? 'flex' : 'none';
        if(id === 'screen-home') renderHome();
        if(id === 'screen-cart') renderCart();
        if(id === 'screen-admin') renderAdmin();
    }

    // --- LOJA ---
    function renderHome(busca = "") {
        const grid = document.getElementById('product-grid');
        const filtrados = estoque.filter(p => p.n.toLowerCase().includes(busca.toLowerCase()));

        grid.innerHTML = filtrados.map(p => `
            <div class="item-card">
                <b>${p.n}</b><br>
                <small>R$ ${p.p.toFixed(2)} / ${p.tipo.toUpperCase()}</small>
                <div class="qty-selector">
                    <input type="number" id="qty-${p.id}" class="qty-input" value="1" step="${p.tipo === 'kg' ? '0.1' : '1'}" min="0.1">
                    <span>${p.tipo}</span>
                </div>
                <button class="btn-add" style="background:var(--orange-header); color:white; border:none; width:100%; padding:8px; border-radius:5px; margin-top:8px; cursor:pointer;" onclick="addAoCarrinho(${p.id})">ADICIONAR</button>
            </div>
        `).join('');
    }

    function addAoCarrinho(id) {
        const p = estoque.find(x => x.id === id);
        const qtd = parseFloat(document.getElementById(`qty-${id}`).value);
        if(qtd <= 0) return alert("Quantidade inválida");

        carrinho.push({ ...p, qtd Selecionada: qtd, precoFinal: p.p * qtd });
        document.getElementById('count').innerText = carrinho.length;
        alert(`${p.n} adicionado!`);
    }

    function renderCart() {
        const container = document.getElementById('cart-items-list');
        if(carrinho.length === 0) {
            container.innerHTML = "<center>Carrinho vazio</center>";
            document.getElementById('cart-footer').style.display = "none";
            return;
        }
        let total = 0;
        container.innerHTML = carrinho.map((it, i) => {
            total += it.precoFinal;
            return `
                <div class="admin-item">
                    <div>
                        <b>${it.n}</b><br>
                        <small>${it.qtdSelecionada} ${it.tipo} x R$ ${it.p.toFixed(2)}</small>
                    </div>
                    <div style="text-align:right">
                        <b>R$ ${it.precoFinal.toFixed(2)}</b><br>
                        <button onclick="remCart(${i})" style="color:red; border:none; background:none; font-size:11px">Remover</button>
                    </div>
                </div>
            `;
        }).join('');
        document.getElementById('total-val').innerText = "R$ " + (total + 7).toFixed(2);
        document.getElementById('cart-footer').style.display = "block";
    }

    function remCart(i) {
        carrinho.splice(i, 1);
        document.getElementById('count').innerText = carrinho.length;
        renderCart();
    }

    // --- ADMIN ---
    function acessoAdmin() {
        if(prompt("Senha ADM:") === SENHA_MESTRA) irPara('screen-admin');
        else alert("Senha incorreta");
    }

    function renderAdmin(busca = "") {
        const lista = document.getElementById('adm-lista-geral');
        const filtrados = estoque.filter(p => p.n.toLowerCase().includes(busca.toLowerCase()));
        
        lista.innerHTML = filtrados.map((p, i) => `
            <div class="admin-item">
                <span><b>${p.n}</b> <span class="badge-type">${p.tipo}</span><br>R$ ${p.p.toFixed(2)}</span>
                <button onclick="admRemover(${p.id})" style="background:red; color:white; border:none; padding:5px 10px; border-radius:5px">Apagar</button>
            </div>
        `).join('');
    }

    function admAdicionar() {
        const n = document.getElementById('adm-nome').value;
        const t = document.getElementById('adm-tipo').value;
        const p = parseFloat(document.getElementById('adm-preco').value);
        if(!n || !p) return alert("Preencha os campos!");

        estoque.push({ id: Date.now(), n, tipo: t, p });
        localStorage.setItem('medela_estoque_v2', JSON.stringify(estoque));
        renderAdmin();
        alert("Produto cadastrado!");
    }

    function admRemover(id) {
        if(confirm("Apagar este produto permanentemente?")) {
            estoque = estoque.filter(p => p.id !== id);
            localStorage.setItem('medela_estoque_v2', JSON.stringify(estoque));
            renderAdmin();
        }
    }

    // --- SISTEMA ---
    function fazerLogin() {
        const cpf = document.getElementById('login-cpf').value;
        if(cpf.length < 11) return alert("Digite um CPF válido");
        user = { nome: "Cliente Medela", cpf: cpf, end: "Endereço não informado" };
        localStorage.setItem('medela_user_ativo', JSON.stringify(user));
        irPara('screen-home');
    }

    function enviarWhatsApp() {
        let msg = `*MEDELA SUPERMERCADO*\n\n`;
        carrinho.forEach(i => msg += `- ${i.n}: ${i.qtdSelecionada}${i.tipo} = R$ ${i.precoFinal.toFixed(2)}\n`);
        msg += `\n*TOTAL: ${document.getElementById('total-val').innerText}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    function mascaraCPF(i) {
        let v = i.value.replace(/\D/g, "");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }
    function logout() { localStorage.removeItem('medela_user_ativo'); location.reload(); }
</script>
</body>
</html>
