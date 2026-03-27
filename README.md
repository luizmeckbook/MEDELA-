<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA - V8 FINAL</title>
    <style>
        :root { --green: #00a859; --orange: #f37021; --gray: #f4f4f4; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); margin: 0; padding: 0; }
        
        /* Prevenção de tela branca: todas as telas ocultas por padrão */
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }

        .container { padding: 20px; max-width: 450px; margin: 0 auto; box-sizing: border-box; }
        .header { background: var(--orange); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        
        /* Categorias Estilizadas */
        .cat-bar { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; scrollbar-width: none; }
        .cat-item { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 12px; font-weight: bold; cursor: pointer; }
        .cat-item.active-cat { background: var(--green); color: white; }

        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 100px; }
        .card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .card img { width: 100%; height: 90px; object-fit: contain; margin-bottom: 5px; }

        .btn { background: var(--green); color: white; border: none; padding: 15px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; margin-top: 10px; }
        .input-group { margin-bottom: 10px; }
        .input-group input, .input-group select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }

        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .nav-btn { font-size: 11px; color: #aaa; border: none; background: none; font-weight: bold; cursor: pointer; }
        
        .admin-item { background: white; padding: 10px; margin-bottom: 5px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange); }
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
        <h3 style="color:var(--green)">Total + R$ 7,00 Taxa: R$ <span id="t-val">0,00</span></h3>
        <button class="btn" style="background:#25d366" onclick="sendZap()">PEDIR NO WHATSAPP</button>
    </div>
</section>

<section id="scr-admin" class="app-screen">
    <div class="header"><h2>Painel Gestão</h2></div>
    <div class="container">
        <div style="background:white; padding:15px; border-radius:10px; margin-bottom:20px;">
            <h4>Novo Produto</h4>
            <div class="input-group"><input type="text" id="a-nome" placeholder="Nome"></div>
            <div class="input-group"><input type="text" id="a-foto" placeholder="URL da Foto"></div>
            <div class="input-group"><input type="number" id="a-preco" placeholder="Preço"></div>
            <div class="input-group">
                <select id="a-cat"></select> </div>
            <button class="btn" onclick="addProd()">SALVAR PRODUTO</button>
        </div>
        <input type="text" placeholder="Pesquisar para excluir..." oninput="renderAdmin(this.value)" style="width:100%; margin-bottom:10px; padding:10px;">
        <div id="a-list"></div>
        <button class="btn" style="background:#333" onclick="logout()">SAIR DO PAINEL</button>
    </div>
</section>

<nav class="nav" id="bot-nav" style="display:none;">
    <button class="nav-btn" onclick="go('scr-home')">🛒 LOJA</button>
    <button class="nav-btn" onclick="go('scr-cart')">🛍️ CESTA (<span id="count">0</span>)</button>
    <button class="nav-btn" onclick="logout()">🚪 SAIR</button>
</nav>

<script>
    const CATS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria", "Fiambreria"];
    let db = JSON.parse(localStorage.getItem('medela_db')) || [];
    let user = JSON.parse(localStorage.getItem('medela_user'));
    let isAdmin = localStorage.getItem('medela_is_admin') === 'true';
    let cart = [];
    let curCat = "Mercearia";

    // --- SISTEMA ANTI-TELA BRANCA E MANUTENÇÃO DE SESSÃO ---
    window.onload = () => {
        // Popular selects de categoria logo no início
        document.getElementById('a-cat').innerHTML = CATS.map(c => `<option value="${c}">${c}</option>`).join('');
        document.getElementById('cat-loja').innerHTML = CATS.map(c => `<div class="cat-item ${c===curCat?'active-cat':''}" onclick="setCatLoja('${c}')">${c}</div>`).join('');

        if (isAdmin) {
            go('scr-admin');
        } else if (user) {
            document.getElementById('u-name').innerText = `Olá, ${user.nome.split(' ')[0]}`;
            go('scr-home');
        } else {
            go('scr-login');
        }
    };

    function go(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        const target = document.getElementById(id);
        if(target) target.classList.add('active');
        
        document.getElementById('bot-nav').style.display = (id === 'scr-home' || id === 'scr-cart') ? 'flex' : 'none';
        
        if(id === 'scr-home') renderHome();
        if(id === 'scr-cart') renderCart();
        if(id === 'scr-admin') renderAdmin();
    }

    // --- LOJA ---
    function setCatLoja(c) {
        curCat = c;
        document.getElementById('cat-loja').innerHTML = CATS.map(cat => `<div class="cat-item ${cat===curCat?'active-cat':''}" onclick="setCatLoja('${cat}')">${cat}</div>`).join('');
        renderHome();
    }

    function renderHome(search = "") {
        const grid = document.getElementById('p-grid');
        const filtered = db.filter(p => p.cat === curCat && p.nome.toLowerCase().includes(search.toLowerCase()));
        
        grid.innerHTML = filtered.map(p => `
            <div class="card">
                <img src="${p.foto || 'https://via.placeholder.com/100?text=Sem+Foto'}" onerror="this.src='https://via.placeholder.com/100?text=Erro+Foto'">
                <b style="font-size:12px; display:block; height:30px">${p.nome}</b>
                <span style="color:var(--green); font-weight:bold">R$ ${p.preco.toFixed(2)}</span>
                <button onclick="addToCart(${p.id})" style="background:var(--orange); color:white; border:none; padding:5px; border-radius:5px; width:100%; margin-top:5px; cursor:pointer">ADD</button>
            </div>
        `).join('');
    }

    function addToCart(id) {
        const p = db.find(x => x.id === id);
        cart.push(p);
        document.getElementById('count').innerText = cart.length;
    }

    function renderCart() {
        const list = document.getElementById('c-list');
        if(cart.length === 0) {
            list.innerHTML = "<p style='text-align:center; margin-top:50px'>Carrinho vazio</p>";
            document.getElementById('c-foot').style.display = "none";
            return;
        }
        let total = cart.reduce((a, b) => a + b.preco, 0) + 7;
        list.innerHTML = cart.map((p, i) => `
            <div class="admin-item">
                <span>${p.nome}</span>
                <b>R$ ${p.preco.toFixed(2)} <span onclick="remCart(${i})" style="color:red; margin-left:10px">X</span></b>
            </div>
        `).join('');
        document.getElementById('t-val').innerText = total.toFixed(2);
        document.getElementById('c-foot').style.display = "block";
    }

    function remCart(i) { cart.splice(i,1); renderCart(); document.getElementById('count').innerText = cart.length; }

    function sendZap() {
        let t = document.getElementById('t-val').innerText;
        let msg = `*PEDIDO MEDELA*\nCliente: ${user.nome}\nEndereço: ${user.end}\n\n*Itens:*\n`;
        cart.forEach(i => msg += `- ${i.nome}: R$ ${i.preco.toFixed(2)}\n`);
        msg += `\n*TOTAL COM TAXA: R$ ${t}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    // --- ADMIN ---
    function askAdmin() {
        if(prompt("Senha:") === "123") {
            isAdmin = true;
            localStorage.setItem('medela_is_admin', 'true');
            go('scr-admin');
        }
    }

    function addProd() {
        const n = document.getElementById('a-nome').value, f = document.getElementById('a-foto').value, p = parseFloat(document.getElementById('a-preco').value), c = document.getElementById('a-cat').value;
        if(!n || !p) return alert("Preencha Nome e Preço");
        db.push({id: Date.now(), nome: n, foto: f, preco: p, cat: c});
        localStorage.setItem('medela_db', JSON.stringify(db));
        renderAdmin();
        alert("Produto Salvo!");
    }

    function renderAdmin(search = "") {
        const list = document.getElementById('a-list');
        const filtered = db.filter(p => p.nome.toLowerCase().includes(search.toLowerCase()));
        list.innerHTML = filtered.map(p => `
            <div class="admin-item">
                <span><b>${p.nome}</b><br><small>${p.cat}</small></span>
                <button onclick="remProd(${p.id})" style="background:red; color:white; border:none; border-radius:5px">X</button>
            </div>
        `).join('');
    }

    function remProd(id) {
        db = db.filter(p => p.id !== id);
        localStorage.setItem('medela_db', JSON.stringify(db));
        renderAdmin();
    }

    // --- AUTH ---
    function login() {
        const cpf = document.getElementById('l-cpf').value;
        const saved = localStorage.getItem(`u_${cpf}`);
        if(saved) {
            user = JSON.parse(saved);
            localStorage.setItem('medela_user', JSON.stringify(user));
            location.reload(); // Recarrega para aplicar estado limpo
        } else alert("CPF não cadastrado");
    }

    function register() {
        const n = document.getElementById('r-nome').value, c = document.getElementById('r-cpf').value, e = document.getElementById('r-end').value;
        if(!n || c.length < 14) return alert("Dados incompletos");
        const d = {nome: n, cpf: c, end: e};
        localStorage.setItem(`u_${c}`, JSON.stringify(d));
        localStorage.setItem('medela_user', JSON.stringify(d));
        user = d;
        location.reload();
    }

    function logout() {
        localStorage.removeItem('medela_user');
        localStorage.removeItem('medela_is_admin');
        location.reload();
    }

    function maskCPF(i) {
        let v = i.value.replace(/\D/g, "");
        v = v.replace(/(\d{3})(\d)/, "$1.$2").replace(/(\d{3})(\d)/, "$1.$2").replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }
</script>
</body>
</html>
