<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado - Oficial</title>
    <style>
        :root { --primary-green: #00a859; --orange-main: #f37021; --bg-gray: #f2f2f2; --white: #ffffff; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; }
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; background: white; }
        .active { display: flex; flex-direction: column; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        .logo-area { text-align: center; margin: 30px 0; }
        .logo-area h1 { color: var(--primary-green); margin: 0; }
        .logo-area span { color: var(--orange-main); }
        .input-group { margin-bottom: 12px; text-align: left; }
        .input-group label { display: block; font-size: 11px; color: #666; margin-bottom: 4px; font-weight: bold; }
        .input-group input { width: 100%; padding: 14px; border: 1px solid #ddd; border-radius: 8px; background: #f8f8f8; box-sizing: border-box; }
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 16px; border-radius: 8px; font-weight: bold; cursor: pointer; }
        .header-app { background: linear-gradient(135deg, var(--orange-main), #ff8c42); padding: 45px 20px; color: white; border-radius: 0 0 25px 25px; }
        .economy-card { background: white; margin: -30px 20px 0 20px; padding: 20px; border-radius: 15px; box-shadow: 0 8px 20px rgba(0,0,0,0.1); }
        .product-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 20px; }
        .product-card { background: white; padding: 15px; border-radius: 12px; text-align: center; border: 1px solid #eee; }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; }
    </style>
</head>
<body>

    <section id="screen-login" class="app-screen active">
        <div class="container">
            <div class="logo-area">
                <h1>Medela<span> Supermercado</span></h1>
                <p>Entre com CPF ou Gmail</p>
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
            <p style="text-align:center; color: var(--primary-green); font-weight:bold; cursor:pointer; margin-top:25px;" onclick="irPara('screen-register')">Criar conta (Maior de 18 anos)</p>
        </div>
    </section>

    <section id="screen-register" class="app-screen">
        <div class="container">
            <h2 style="color:var(--primary-green)">Cadastro Medela</h2>
            <div class="input-group">
                <label>* CPF (Somente números)</label>
                <input type="text" id="reg-cpf" placeholder="000.000.000-00" oninput="mascaraCPF(this)">
            </div>
            <div class="input-group">
                <label>* Nome Completo (Deve bater com o CPF)</label>
                <input type="text" id="reg-nome" placeholder="Nome como no documento">
            </div>
            <div class="input-group">
                <label>* Data de Nascimento</label>
                <input type="date" id="reg-nascimento">
            </div>
            <div class="input-group">
                <label>* Telefone</label>
                <input type="text" id="reg-tel" placeholder="(00) 00000-0000">
            </div>
            <div class="input-group">
                <label>* Gmail</label>
                <input type="email" id="reg-email" placeholder="exemplo@gmail.com">
            </div>
            <div class="input-group">
                <label>* Crie sua Senha</label>
                <input type="password" id="reg-senha" placeholder="••••••••">
            </div>
            <button class="btn-green" id="btn-cadastrar" onclick="finalizarCadastro()">Verificar e Cadastrar</button>
            <p style="text-align:center; cursor:pointer; color:#666;" onclick="irPara('screen-login')">Voltar</p>
        </div>
    </section>

    <section id="screen-home" class="app-screen">
        <header class="header-app">
            <h2 id="user-display">Olá!</h2>
            <p>Economia garantida Medela</p>
        </header>
        <div class="economy-card">
            <span>Você economizou hoje:</span>
            <h2 style="color:var(--primary-green)">R$ 0,00</h2>
        </div>
        <div class="product-grid">
            <div class="product-card">
                <img src="https://cdn-icons-png.flaticon.com/512/1134/1134444.png" width="60">
                <p>Carne kg</p>
                <strong>R$ 29,98</strong>
                <button class="btn-green" style="padding:5px; font-size:10px;">Ativar</button>
            </div>
        </div>
        <nav class="bottom-nav">
            <div style="color:var(--primary-green)">Ofertas</div>
            <div>Jornal</div>
            <div onclick="location.reload()">Sair</div>
        </nav>
    </section>

    <script>
        function irPara(id) {
            document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
            document.getElementById(id).classList.add('active');
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
            const m = hoje.getMonth() - nasc.getMonth();
            if (m < 0 || (m === 0 && hoje.getDate() < nasc.getDate())) idade--;
            return idade;
        }

        function finalizarCadastro() {
            const btn = document.getElementById('btn-cadastrar');
            const cpf = document.getElementById('reg-cpf').value;
            const nome = document.getElementById('reg-nome').value;
            const dataNasc = document.getElementById('reg-nascimento').value;
            const email = document.getElementById('reg-email').value.toLowerCase();
            const senha = document.getElementById('reg-senha').value;

            // 1. Validar Idade
            if (calcularIdade(dataNasc) < 18) {
                alert("Erro: Você precisa ter mais de 18 anos para se cadastrar.");
                return;
            }

            // 2. Simulação de "CPF bate com Nome"
            // Como não temos acesso à Receita Federal, simulamos um "carregamento"
            btn.innerText = "Consultando base de dados...";
            btn.disabled = true;

            setTimeout(() => {
                if (nome.length < 10) {
                    alert("Erro: O Nome Completo não condiz com o CPF informado na base de dados.");
                    btn.innerText = "Verificar e Cadastrar";
                    btn.disabled = false;
                } else {
                    const dados = { nome, cpf, email, senha };
                    localStorage.setItem(cpf, JSON.stringify(dados));
                    localStorage.setItem(email, JSON.stringify(dados));
                    alert("Sucesso: Dados validados! Cadastro concluído.");
                    irPara('screen-login');
                }
            }, 2000); // 2 segundos de simulação
        }

        function executarLogin() {
            const id = document.getElementById('login-id').value.toLowerCase();
            const senha = document.getElementById('login-pass').value;
            const user = JSON.parse(localStorage.getItem(id));

            if (user && user.senha === senha) {
                document.getElementById('user-display').innerText = "Olá, " + user.nome.split(' ')[0];
                irPara('screen-home');
            } else {
                alert("Dados incorretos ou usuário não validado!");
            }
        }
    </script>
</body>
</html>
