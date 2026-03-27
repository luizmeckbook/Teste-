<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado - App</title>
    <style>
        :root { --primary-green: #00a859; --orange-main: #f37021; --bg-gray: #f4f4f4; --white: #ffffff; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; color: #333; }
        
        /* TELAS */
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; background: white; }
        .active { display: flex; flex-direction: column; }
        
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* FORMULÁRIOS */
        .logo-area { text-align: center; margin: 30px 0; }
        .logo-area h1 { color: var(--primary-green); margin: 0; }
        .logo-area span { color: var(--orange-main); }
        .input-group { margin-bottom: 12px; }
        .input-group label { display: block; font-size: 11px; color: #666; margin-bottom: 4px; font-weight: bold; }
        .input-group input { width: 100%; padding: 14px; border: 1px solid #ddd; border-radius: 8px; background: #f9f9f9; box-sizing: border-box; }
        
        /* BOTÕES */
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 16px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 15px; }
        .btn-off { background: #ccc !important; cursor: not-allowed !important; }

        /* HOME APP */
        .header-app { background: linear-gradient(135deg, var(--orange-main), #ff8c42); padding: 40px 20px; color: white; border-radius: 0 0 25px 25px; }
        .economy-card { background: white; margin: -30px 20px 10px 20px; padding: 20px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        
        /* GRID DE MERCADO */
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 20px; padding-bottom: 100px; }
        .item-card { background: white; border: 1px solid #eee; border-radius: 12px; padding: 15px; text-align: center; position: relative; opacity: 0.8; }
        .stock-tag { background: #ffeded; color: #d93025; font-size: 10px; font-weight: bold; padding: 4px; border-radius: 4px; position: absolute; top: 8px; right: 8px; }
        .item-card img { width: 60px; height: 60px; filter: grayscale(1); margin-bottom: 10px; }
        .item-name { font-size: 12px; font-weight: bold; margin: 5px 0; display: block; height: 30px; }

        /* NAV */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 100; }
        .nav-item { font-size: 10px; color: #bbb; text-align: center; cursor: pointer; }
        .nav-active { color: var(--primary-green); font-weight: bold; }
    </style>
</head>
<body>

    <section id="screen-login" class="app-screen active">
        <div class="container">
            <div class="logo-area">
                <h1>Medela<span> Supermercado</span></h1>
                <p style="font-size: 14px; color: #888;">Acesse com CPF ou Gmail</p>
            </div>
            <div class="input-group">
                <label>CPF ou E-mail</label>
                <input type="text" id="login-id" placeholder="Digite aqui">
            </div>
            <div class="input-group">
                <label>Senha</label>
                <input type="password" id="login-pass" placeholder="••••••••">
            </div>
            <button class="btn-green" onclick="executarLogin()">Entrar</button>
            <p style="text-align:center; color: var(--primary-green); font-weight:bold; cursor:pointer; margin-top:25px;" onclick="irPara('screen-register')">Novo por aqui? Cadastre-se</p>
        </div>
    </section>

    <section id="screen-register" class="app-screen">
        <div class="container">
            <h2 style="color:var(--primary-green)">Cadastro Medela</h2>
            <p style="font-size: 12px; color: #777; margin-bottom: 20px;">Verificação obrigatória: Maior de 18 anos.</p>
            
            <div class="input-group">
                <label>* CPF (Será validado)</label>
                <input type="text" id="reg-cpf" placeholder="000.000.000-00" oninput="mascaraCPF(this)">
            </div>
            <div class="input-group">
                <label>* Nome Completo (Conforme documento)</label>
                <input type="text" id="reg-nome" placeholder="Nome Sobrenome">
            </div>
            <div class="input-group">
                <label>* Data de Nascimento</label>
                <input type="date" id="reg-nascimento">
            </div>
            <div class="input-group">
                <label>* Gmail</label>
                <input type="email" id="reg-email" placeholder="seuemail@gmail.com">
            </div>
            <div class="input-group">
                <label>* Senha</label>
                <input type="password" id="reg-senha" placeholder="••••••••">
            </div>
            
            <button class="btn-green" id="btn-cadastrar" onclick="finalizarCadastro()">Validar e Criar Conta</button>
            <p style="text-align:center; cursor:pointer; color:#999; font-size: 14px;" onclick="irPara('screen-login')">Já tenho conta</p>
        </div>
    </section>

    <section id="screen-home" class="app-screen">
        <header class="header-app">
            <h2 id="user-display" style="margin:0">Olá!</h2>
            <p style="font-size:13px; opacity:0.9">Confira nossos setores abaixo:</p>
        </header>

        <div class="economy-card">
            <span style="font-size:11px; color:#999;">Economia Sincronizada:</span>
            <h2 style="color:var(--primary-green); margin:5px 0;">R$ 0,00</h2>
        </div>

        <div class="market-grid">
            <div class="item-card">
                <span class="stock-tag">SEM ESTOQUE</span>
                <img src="https://cdn-icons-png.flaticon.com/512/1134/1134444.png">
                <span class="item-name">Contra Filé KG</span>
                <button class="btn-green btn-off">Indisponível</button>
            </div>
            <div class="item-card">
                <span class="stock-tag">SEM ESTOQUE</span>
                <img src="https://cdn-icons-png.flaticon.com/512/415/415733.png">
                <span class="item-name">Banana Prata Unid.</span>
                <button class="btn-green btn-off">Indisponível</button>
            </div>
            <div class="item-card">
                <span class="stock-tag">SEM ESTOQUE</span>
                <img src="https://cdn-icons-png.flaticon.com/512/3122/3122042.png">
                <span class="item-name">Cerveja Lata 350ml</span>
                <button class="btn-green btn-off">Indisponível</button>
            </div>
            <div class="item-card">
                <span class="stock-tag">SEM ESTOQUE</span>
                <img src="https://cdn-icons-png.flaticon.com/512/2553/2553642.png">
                <span class="item-name">Detergente Líquido</span>
                <button class="btn-green btn-off">Indisponível</button>
            </div>
            <div class="item-card">
                <span class="stock-tag">SEM ESTOQUE</span>
                <img src="https://cdn-icons-png.flaticon.com/512/3014/3014545.png">
                <span class="item-name">Pão Francês KG</span>
                <button class="btn-green btn-off">Indisponível</button>
            </div>
            <div class="item-card">
                <span class="stock-tag">SEM ESTOQUE</span>
                <img src="https://cdn-icons-png.flaticon.com/512/2674/2674486.png">
                <span class="item-name">Leite Integral 1L</span>
                <button class="btn-green btn-off">Indisponível</button>
            </div>
        </div>

        <nav class="bottom-nav">
            <div class="nav-item nav-active">MERCADO</div>
            <div class="nav-item">CUPONS</div>
            <div class="nav-item" onclick="location.reload()">SAIR</div>
        </nav>
    </section>

    <script>
        function irPara(id) {
            document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            window.scrollTo(0,0);
        }

        function mascaraCPF(i) {
            let v = i.value.replace(/\D/g, "");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
            i.value = v;
        }

        function calcularIdade(data) {
            const hoje = new Date();
            const nasc = new Date(data);
            let idade = hoje.getFullYear() - nasc.getFullYear();
            if (hoje.getMonth() < nasc.getMonth() || (hoje.getMonth() === nasc.getMonth() && hoje.getDate() < nasc.getDate())) idade--;
            return idade;
        }

        function finalizarCadastro() {
            const btn = document.getElementById('btn-cadastrar');
            const cpf = document.getElementById('reg-cpf').value;
            const nome = document.getElementById('reg-nome').value;
            const dataNasc = document.getElementById('reg-nascimento').value;
            const email = document.getElementById('reg-email').value.toLowerCase();
            const senha = document.getElementById('reg-senha').value;

            if (calcularIdade(dataNasc) < 18) {
                alert("Cadastro negado: Você precisa ter 18 anos ou mais.");
                return;
            }

            btn.innerText = "Validando com a base de dados...";
            btn.disabled = true;

            setTimeout(() => {
                if (nome.trim().split(" ").length < 2) {
                    alert("Erro: O Nome Completo não condiz com o CPF na base de dados.");
                    btn.innerText = "Validar e Criar Conta";
                    btn.disabled = false;
                } else {
                    const user = { nome, cpf, email, senha };
                    localStorage.setItem(cpf, JSON.stringify(user));
                    localStorage.setItem(email, JSON.stringify(user));
                    alert("Sucesso! Cadastro validado.");
                    irPara('screen-login');
                }
            }, 1500);
        }

        function executarLogin() {
            const id = document.getElementById('login-id').value.toLowerCase();
            const senha = document.getElementById('login-pass').value;
            const user = JSON.parse(localStorage.getItem(id));

            if (user && user.senha === senha) {
                document.getElementById('user-display').innerText = "Olá, " + user.nome.split(' ')[0];
                irPara('screen-home');
            } else {
                alert("Erro: Dados incorretos ou CPF/Email não validado.");
            }
        }
    </script>
</body>
</html>
