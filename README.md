<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; color: var(--text-dark); }
        
        /* Previne tela branca: as telas começam escondidas, mas o JS decide qual mostrar */
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; flex-direction: column; }
        .active { display: flex !important; }
        
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* LOGIN & CADASTRO */
        .logo-main { text-align: center; margin: 30px 0; }
        .logo-main h1 { color: var(--primary-green); margin: 0; font-size: 32px; font-weight: 900; }
        .input-group { margin-bottom: 15px; }
        .input-group label { display: block; font-size: 12px; font-weight: bold; margin-bottom: 5px; }
        .input-group input { width: 100%; padding: 14px; border: 1px solid #ddd; border-radius: 10px; outline: none; box-sizing: border-box; }
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; font-size: 16px; }

        /* HOME */
        .header-orange { background: var(--orange-header); padding: 30px 20px; color: white; border-radius: 0 0 25px 25px; position: sticky; top: 0; z-index: 100; }
        .category-scroll { display: flex; overflow-x: auto; padding: 10px 20px; gap: 10px; background: #fff; border-bottom: 1px solid #eee; white-space: nowrap; }
        .cat-pill { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 13px; cursor: pointer; border: 1px solid #ddd; }
        .cat-active { background: var(--primary-green); color: white; border-color: var(--primary-green); font-weight: bold; }

        /* PRODUTOS */
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 15px; padding-bottom: 150px; }
        .item-card { background: white; border-radius: 12px; padding: 12px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .item-card .price { color: var(--primary-green); font-weight: bold; display: block; margin-top: 5px; }
        .btn-add { background: var(--orange-header); color: white; border: none; width: 100%; padding: 8px; border-radius: 6px; cursor: pointer; margin-top: 10px; font-weight: bold; }

        /* CARRINHO */
        .cart-footer { position: fixed; bottom: 65px; background: white; width: 100%; max-width: 450px; padding: 15px; box-sizing: border-box; border-top: 2px solid #eee; left: 50%; transform: translateX(-50%); z-index: 999; }
        .info-user-cart { background: #fff; padding: 15px; border-radius: 10px; margin-bottom: 15px; border: 1px solid #ddd; font-size: 14px; }
        
        /* NAV */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 1000; left: 50%; transform: translateX(-50%); }
        .nav-item { font-size: 11px; color: #aaa; text-align: center; border: none; background: none; font-weight: bold; cursor: pointer; }
        .nav-active { color: var(--primary-green); }
    </style>
</head>
<body>

    <section id="screen-login" class="app-screen">
        <div class="container">
            <div class="logo-main"><h1>MEDELA</h1><p style="color:var(--orange-header); font-weight:bold; margin:0;">SUPERMERCADO</p></div>
            <div class="input-group"><label>CPF</label><input type="text" id="login-cpf" placeholder="000.000.000-00"></div>
            <div class="input-group"><label>Senha</label><input type="password" id="login-pass" placeholder="••••••••"></div>
            <button class="btn-green" onclick="fazerLogin()">ENTRAR</button>
            <p style="text-align:center; margin-top:20px; cursor:pointer;" onclick="irPara('screen-register')">Não tem conta? <b>Cadastre-se</b></p>
        </div>
    </section>

    <section id="screen-register" class="app-screen">
        <div class="container">
            <h2>Criar Conta</h2>
            <div class="input-group"><label>Nome Completo</label><input type="text" id="reg-nome"></div>
            <div class="input-group"><label>CPF</label><input type="text" id="reg-cpf" oninput="mascaraCPF(this)"></div>
            <div class="input-group"><label>Endereço Completo</label><input type="text" id="reg-end" placeholder="Rua, número, bairro"></div>
            <div class="input-group"><label>Senha</label><input type="password" id="reg-pass"></div>
            <button class="btn-green" onclick="salvarCadastro()">CADASTRAR</button>
            <p style="text-align:center; margin-top:20px; cursor:pointer;" onclick="irPara('screen-login')">Já tenho conta</p>
        </div>
    </section>

    <section id="screen-home" class="app-screen">
        <div class="header-orange">
            <h2 id="user-display">Olá!</h2>
            <input type="text" class="search-box" style="width:100%" placeholder="O que busca no Medela?" oninput="filtrar(this.value)">
        </div>
        <div class="category-scroll" id="cat-list"></div>
        <div class="market-grid" id="product-grid"></div>
    </section>

    <section id="screen-cart" class="app-screen">
        <div class="header-orange"><h2>Meu Pedido</h2></div>
        <div class="container">
            <div class="info-user-cart">
                <strong>Entregar em:</strong><br>
                <span id="cart-user-nome"></span><br>
                <span id="cart-user-end"></span>
            </div>
            <div id="cart-list"></div>
        </div>
        <div class="cart-footer" id="cart-footer" style="display:none;">
            <div style="display:flex; justify-content:space-between;"><span>Subtotal:</span><span id="sub-val">0,00</span></div>
            <div style="display:flex; justify-content:space-between; color:red;"><span>Entrega:</span><span>R$ 7,00</span></div>
            <div style="display:flex; justify-content:space-between; font-weight:bold; font-size:18px; margin-top:5px;">
                <span>TOTAL:</span><span style="color:var(--primary-green)">R$ <span id="total-val">0,00</span></span>
            </div>
            <button class="btn-green" style="margin-top:10px" onclick="enviarWhatsApp()">PEDIR PELO WHATSAPP</button>
        </div>
    </section>

    <nav class="bottom-nav" id="main-nav" style="display:none;">
        <button class="nav-item nav-active" id="btn-h" onclick="irPara('screen-home')">PRODUTOS</button>
        <button class="nav-item" id="btn-c" onclick="irPara('screen-cart')">CARRINHO (<span id="count">0</span>)</button>
        <button class="nav-item" onclick="logout()">SAIR</button>
    </nav>

<script>
    const TAXA = 7.00;
    const produtosMaster = [
        { cat: "Mercearia Seca", itens: ["Arroz Agulhinha", "Arroz Integral", "Feijão Carioca", "Feijão Preto", "Farinha de Trigo", "Açúcar Cristal", "Café Pilão", "Óleo de Soja", "Macarrão Espaguete", "Leite Condensado"] },
        { cat: "Hortifrúti", itens: ["Banana Prata", "Laranja", "Tomate", "Cebola", "Batata", "Ovos 30un", "Alface", "Cenoura"] },
        { cat: "Açougue", itens: ["Picanha KG", "Alcatra", "Contrafilé", "Frango Inteiro", "Carne Moída", "Linguiça Toscana"] },
        { cat: "Bebidas", itens: ["Coca-Cola 2L", "Guaraná 2L", "Cerveja Lata", "Água 500ml", "Suco de Uva"] }
    ];

    let carrinho = JSON.parse(localStorage.getItem('medela_cart')) || [];
    let user = JSON.parse(localStorage.getItem('medela_user_ativo'));
    let catAtual = "Mercearia Seca";

    // INICIALIZAÇÃO SEGURA (EVITA TELA BRANCA)
    window.addEventListener('DOMContentLoaded', () => {
        if(user) {
            logarInterface();
            irPara('screen-home');
        } else {
            irPara('screen-login');
        }
    });

    function irPara(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('main-nav').style.display = (id === 'screen-home' || id === 'screen-cart') ? 'flex' : 'none';
        
        if(id === 'screen-home') renderHome();
        if(id === 'screen-cart') renderCart();
    }

    function salvarCadastro() {
        const nome = document.getElementById('reg-nome').value;
        const cpf = document.getElementById('reg-cpf').value;
        const end = document.getElementById('reg-end').value;
        const pass = document.getElementById('reg-pass').value;

        if(!nome || !cpf || !end) return alert("Preencha Nome e Endereço!");

        const dados = { nome, cpf, end, pass };
        localStorage.setItem(cpf, JSON.stringify(dados));
        alert("Cadastro feito!");
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
        } else {
            alert("CPF ou Senha incorretos!");
        }
    }

    function logarInterface() {
        document.getElementById('user-display').innerText = "Olá, " + user.nome.split(' ')[0] + "!";
        document.getElementById('cart-user-nome').innerText = user.nome;
        document.getElementById('cart-user-end').innerText = user.end;
        updateBadge();
    }

    function renderHome() {
        const list = document.getElementById('cat-list');
        list.innerHTML = produtosMaster.map(c => `<div class="cat-pill ${catAtual === c.cat ? 'cat-active' : ''}" onclick="setCat('${c.cat}')">${c.cat}</div>`).join('');
        
        const grid = document.getElementById('product-grid');
        grid.innerHTML = "";
        const c = produtosMaster.find(x => x.cat === catAtual);
        c.itens.forEach(item => {
            const p = (Math.random() * 15 + 5).toFixed(2);
            grid.innerHTML += `<div class="item-card"><b>${item}</b><span class="price">R$ ${p}</span><button class="btn-add" onclick="add('${item}', ${p})">ADICIONAR</button></div>`;
        });
    }

    function add(n, p) {
        carrinho.push({n, p});
        localStorage.setItem('medela_cart', JSON.stringify(carrinho));
        updateBadge();
    }

    function updateBadge() { document.getElementById('count').innerText = carrinho.length; }

    function renderCart() {
        const list = document.getElementById('cart-list');
        if(carrinho.length === 0) {
            list.innerHTML = "<p style='text-align:center; padding:30px;'>Carrinho vazio.</p>";
            document.getElementById('cart-footer').style.display = "none";
            return;
        }
        let sub = 0;
        list.innerHTML = carrinho.map((it, i) => {
            sub += parseFloat(it.p);
            return `<div style="background:#fff; padding:10px; margin-bottom:5px; border-radius:8px; display:flex; justify-content:space-between;">
                <span>${it.n}</span><b>R$ ${it.p.toFixed(2)} <span style="color:red; margin-left:10px" onclick="rem(${i})">X</span></b>
            </div>`;
        }).join('');
        document.getElementById('sub-val').innerText = "R$ " + sub.toFixed(2);
        document.getElementById('total-val').innerText = (sub + TAXA).toFixed(2);
        document.getElementById('cart-footer').style.display = "block";
    }

    function rem(i) {
        carrinho.splice(i, 1);
        localStorage.setItem('medela_cart', JSON.stringify(carrinho));
        renderCart();
        updateBadge();
    }

    function enviarWhatsApp() {
        let msg = `*MEDELA SUPERMERCADO*\n\n`;
        msg += `*CLIENTE:* ${user.nome}\n*ENDEREÇO:* ${user.end}\n\n`;
        carrinho.forEach(i => msg += `- ${i.n}: R$ ${i.p.toFixed(2)}\n`);
        msg += `\nTaxa Entrega: R$ 7,00\n*TOTAL: R$ ${document.getElementById('total-val').innerText}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    function setCat(c) { catAtual = c; renderHome(); }
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
