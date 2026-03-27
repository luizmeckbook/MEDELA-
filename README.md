<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO PRO - V6</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg-gray); margin: 0; color: var(--text-dark); overflow-x: hidden; }
        
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        .header-orange { background: var(--orange-header); padding: 15px 20px; color: white; border-radius: 0 0 20px 20px; text-align: center; position: sticky; top:0; z-index:100; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 1000; left: 50%; transform: translateX(-50%); }
        .nav-item { font-size: 11px; color: #aaa; text-align: center; border: none; background: none; font-weight: bold; cursor: pointer; }
        .nav-active { color: var(--primary-green); }

        .cat-container { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; scrollbar-width: none; }
        .cat-pill { padding: 8px 16px; background: #f0f0f0; border-radius: 20px; font-size: 12px; font-weight: bold; cursor: pointer; border: 1px solid #ddd; }
        .cat-pill.active-cat { background: var(--primary-green); color: white; border-color: var(--primary-green); }

        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 15px; padding-bottom: 160px; }
        .item-card { background: white; border-radius: 15px; padding: 12px; text-align: center; box-shadow: 0 4px 6px rgba(0,0,0,0.05); display: flex; flex-direction: column; }
        .product-img { width: 100%; height: 100px; object-fit: contain; border-radius: 10px; margin-bottom: 10px; background: #fff; }
        
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 12px; font-weight: bold; cursor: pointer; font-size: 15px; transition: 0.2s; }
        .btn-green:active { transform: scale(0.97); }
        
        .input-group { margin-bottom: 15px; text-align: left; }
        .input-group input, .input-group select { width: 100%; padding: 14px; border: 1px solid #ddd; border-radius: 10px; box-sizing: border-box; font-size: 14px; outline: none; }
        .input-group input:focus { border-color: var(--orange-header); }

        .list-item { background: white; padding: 15px; margin-bottom: 10px; border-radius: 12px; display: flex; justify-content: space-between; align-items: center; border-left: 6px solid var(--orange-header); box-shadow: 0 2px 4px rgba(0,0,0,0.05); }
        
        .payment-methods { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin: 15px 0; }
        .pay-btn { padding: 12px; border: 2px solid #eee; border-radius: 10px; background: #fff; cursor: pointer; font-weight: bold; font-size: 12px; }
        .pay-btn.selected { border-color: var(--primary-green); color: var(--primary-green); background: #e8f5e9; }
        .info-box { background: #f9f9f9; padding: 15px; border-radius: 10px; border: 1px solid #ddd; margin-top: 10px; display: none; }
    </style>
</head>
<body>

<section id="screen-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 50px 0;">
            <h1 style="color: var(--primary-green); margin:0; font-size: 45px;">MEDELA</h1>
            <p style="color:var(--orange-header); font-weight:bold; letter-spacing: 3px;">SUPERMERCADO</p>
        </div>
        <div class="input-group">
            <input type="text" id="login-cpf" placeholder="Seu CPF (000.000.000-00)" oninput="mascaraCPF(this)" maxlength="14">
        </div>
        <button class="btn-green" onclick="fazerLogin()">ACESSAR MINHA CONTA</button>
        <p style="text-align:center; margin-top:25px;">Primeira vez? <b style="color:var(--orange-header); cursor:pointer" onclick="irPara('screen-register')">Cadastre-se aqui</b></p>
        <p style="text-align:center; margin-top:60px; font-size:12px; color:#bbb; cursor:pointer;" onclick="acessoAdmin()">⚙️ PAINEL ADMINISTRATIVO</p>
    </div>
</section>

<section id="screen-register" class="app-screen">
    <div class="header-orange"><h2>Novo Cadastro</h2></div>
    <div class="container">
        <div class="input-group"><input type="text" id="reg-nome" placeholder="Nome Completo"></div>
        <div class="input-group"><input type="text" id="reg-cpf" placeholder="CPF" oninput="mascaraCPF(this)" maxlength="14"></div>
        <div class="input-group"><input type="text" id="reg-end" placeholder="Endereço Completo para Entrega"></div>
        <button class="btn-green" onclick="salvarCadastro()">CRIAR CONTA</button>
        <button class="btn-green" style="background:#aaa; margin-top:12px;" onclick="irPara('screen-login')">VOLTAR AO LOGIN</button>
    </div>
</section>

<section id="screen-home" class="app-screen">
    <div class="header-orange">
        <h3 id="user-display" style="margin:0">Olá!</h3>
        <input type="text" id="search-home" placeholder="O que você procura hoje?" style="width:100%; margin-top:12px; padding:12px; border-radius:10px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="cat-container" id="cat-list"></div>
    <div class="market-grid" id="product-grid"></div>
</section>

<section id="screen-cart" class="app-screen">
    <div class="header-orange"><h2>Minha Cesta</h2></div>
    <div class="container" id="cart-items-list" style="padding-bottom: 280px;"></div>
    
    <div id="cart-footer" class="container" style="position:fixed; bottom:65px; background:white; width:100%; border-top:2px solid #eee; display:none; left:50%; transform:translateX(-50%); z-index: 500; border-radius: 20px 20px 0 0; box-shadow: 0 -5px 15px rgba(0,0,0,0.05);">
        <h3 style="color:var(--primary-green); margin:15px 0;">Total Final: R$ <span id="total-val">0,00</span></h3>
        <div class="payment-methods">
            <button class="pay-btn" id="btn-pix" onclick="setMetodo('pix')">PIX</button>
            <button class="pay-btn" id="btn-credito" onclick="setMetodo('credito')">CRÉDITO</button>
            <button class="pay-btn" id="btn-debito" onclick="setMetodo('debito')">DÉBITO</button>
            <button class="pay-btn" id="btn-dinheiro" onclick="setMetodo('dinheiro')">DINHEIRO</button>
        </div>
        <div id="info-pix" class="info-box">Chave: <b id="chave-pix-txt"></b> <button onclick="copiarPix()" style="font-size:10px; padding:2px 5px;">Copiar</button></div>
        <div id="info-credito" class="info-box">Parcelamento: <select id="select-parcelas" style="width:100%; border-radius:5px; padding:5px;"></select></div>
        <div id="info-dinheiro" class="info-box"><input type="number" id="troco-input" placeholder="Troco para quanto?" style="width:100%; padding:8px; border-radius:5px; border:1px solid #ddd;"></div>
        <button class="btn-green" style="margin:15px 0; background:#25d366" onclick="enviarWhatsApp()">ENVIAR PEDIDO NO WHATSAPP</button>
    </div>
</section>

<section id="screen-admin" class="app-screen">
    <div class="header-orange"><h2>Gestão de Loja</h2></div>
    <div class="container" style="padding-bottom: 60px;">
        <div class="input-group">
            <label>Chave PIX Recebimento:</label>
            <input type="text" id="adm-chave-pix" placeholder="CPF, Email ou Celular">
            <button class="btn-green" style="padding:8px; margin-top:8px; font-size:12px;" onclick="salvarConfig()">ATUALIZAR CHAVE</button>
        </div>
        <hr>
        <div style="background:white; padding:15px; border-radius:15px; border:1px solid #ddd; margin-bottom:20px;">
            <h4 style="margin:0 0 10px 0">Novo Produto</h4>
            <div class="input-group"><input type="text" id="adm-nome" placeholder="Nome (Ex: Feijão Preto 1kg)"></div>
            <div class="input-group"><input type="text" id="adm-foto" placeholder="Link da Foto (URL)"></div>
            <div class="input-group"><select id="adm-cat-select"></select></div>
            <div class="input-group">
                <select id="adm-tipo"><option value="un">UNIDADE (UN)</option><option value="kg">QUILO (KG)</option></select>
            </div>
            <div class="input-group"><input type="number" id="adm-preco" step="0.01" placeholder="Preço (0.00)"></div>
            <button class="btn-green" onclick="admAdicionar()">CADASTRAR PRODUTO</button>
        </div>
        <div class="input-group">
            <input type="text" id="adm-search-input" placeholder="🔍 Buscar produto para remover..." oninput="renderAdmin(this.value)" style="border:2px solid var(--orange-header);">
        </div>
        <div id="adm-lista-geral"></div>
        <button class="btn-green" style="background:#333; margin-top:30px" onclick="sairAdmin()">DESLOGAR ADMIN</button>
    </div>
</section>

<nav class="bottom-nav" id="main-nav" style="display:none;">
    <button class="nav-item" id="nav-h" onclick="irPara('screen-home')">🛒 LOJA</button>
    <button class="nav-item" id="nav-c" onclick="irPara('screen-cart')">🛍️ CESTA (<span id="count">0</span>)</button>
    <button class="nav-item" onclick="logout()">🚪 SAIR</button>
</nav>

<script>
    const SENHA_MESTRA = "123";
    const CATEGORIAS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria", "Fiambreria"];
    
    // Database Core
    let estoque = JSON.parse(localStorage.getItem('medela_estoque_v6')) || [];
    let carrinho = [];
    let userAtivo = JSON.parse(localStorage.getItem('medela_user_v6'));
    let isAdmin = localStorage.getItem('admin_session_v6') === 'true';
    let chavePix = localStorage.getItem('medela_pix_v6') || "Não definida";
    let catAtual = CATEGORIAS[0];
    let metodoAtivo = "";

    // STARTUP SYSTEM (PREVENT TELA BRANCA)
    window.onload = () => {
        try {
            initSelects();
            if (isAdmin) {
                irPara('screen-admin');
            } else if (userAtivo && userAtivo.cpf) {
                logarInterface();
                irPara('screen-home');
            } else {
                irPara('screen-login');
            }
        } catch (e) {
            console.error("Erro no carregamento. Resetando...");
            localStorage.clear();
            location.reload();
        }
    };

    function initSelects() {
        const sel = document.getElementById('adm-cat-select');
        if(sel) sel.innerHTML = CATEGORIAS.map(c => `<option value="${c}">${c}</option>`).join('');
        const cp = document.getElementById('adm-chave-pix');
        if(cp) cp.value = chavePix;
    }

    function irPara(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        const target = document.getElementById(id);
        if(target) target.classList.add('active');
        
        const nav = document.getElementById('main-nav');
        nav.style.display = (id === 'screen-home' || id === 'screen-cart') ? 'flex' : 'none';
        
        document.getElementById('nav-h').classList.toggle('nav-active', id === 'screen-home');
        document.getElementById('nav-c').classList.toggle('nav-active', id === 'screen-cart');
        
        if(id === 'screen-home') renderHome();
        if(id === 'screen-cart') renderCart();
        if(id === 'screen-admin') renderAdmin();
        window.scrollTo(0,0);
    }

    // --- USER FLOW ---
    function fazerLogin() {
        const cpf = document.getElementById('login-cpf').value;
        const dados = localStorage.getItem(`client_${cpf}`);
        if(dados) {
            userAtivo = JSON.parse(dados);
            localStorage.setItem('medela_user_v6', JSON.stringify(userAtivo));
            logarInterface();
            irPara('screen-home');
        } else { alert("CPF não cadastrado! Por favor, faça o cadastro."); }
    }

    function salvarCadastro() {
        const n = document.getElementById('reg-nome').value.trim();
        const c = document.getElementById('reg-cpf').value.trim();
        const e = document.getElementById('reg-end').value.trim();
        if(!n || c.length < 14 || !e) return alert("Preencha todos os campos corretamente.");
        
        userAtivo = {nome: n, cpf: c, end: e};
        localStorage.setItem(`client_${c}`, JSON.stringify(userAtivo));
        localStorage.setItem('medela_user_v6', JSON.stringify(userAtivo));
        logarInterface();
        irPara('screen-home');
    }

    function logarInterface() { 
        if(userAtivo) document.getElementById('user-display').innerText = `Olá, ${userAtivo.nome.split(' ')[0]}!`;
    }

    // --- LOJA ENGINE ---
    function renderHome(busca = "") {
        const catBox = document.getElementById('cat-list');
        catBox.innerHTML = CATEGORIAS.map(c => `<div class="cat-pill ${catAtual===c?'active-cat':''}" onclick="setCat('${c}')">${c}</div>`).join('');
        
        const grid = document.getElementById('product-grid');
        const lista = estoque.filter(p => p.cat === catAtual && p.n.toLowerCase().includes(busca.toLowerCase()));
        
        grid.innerHTML = lista.map(p => `
            <div class="item-card">
                <img src="${p.foto || 'https://via.placeholder.com/150?text=📦'}" class="product-img" onerror="this.src='https://via.placeholder.com/150?text=📦'">
                <b style="font-size:13px; min-height:35px; overflow:hidden;">${p.n}</b>
                <span style="color:var(--primary-green); font-weight:bold; font-size:16px">R$ ${p.p.toFixed(2)}</span>
                <div style="margin:10px 0; font-size:12px;">
                    <input type="number" id="q-${p.id}" value="1" step="${p.tipo==='kg'?'0.1':'1'}" style="width:50px; text-align:center;"> ${p.tipo}
                </div>
                <button onclick="addCart(${p.id})" style="background:var(--orange-header); color:white; border:none; padding:10px; border-radius:8px; cursor:pointer; font-weight:bold;">COMPRAR</button>
            </div>
        `).join('');
    }

    function setCat(c) { catAtual = c; renderHome(); }

    function addCart(id) {
        const p = estoque.find(x => x.id === id);
        const q = parseFloat(document.getElementById(`q-${id}`).value);
        if(!q || q <= 0) return;
        carrinho.push({...p, qtd: q, sub: p.p * q});
        document.getElementById('count').innerText = carrinho.length;
        alert("Adicionado à cesta!");
    }

    // --- CHECKOUT FLOW ---
    function renderCart() {
        const list = document.getElementById('cart-items-list');
        if(carrinho.length === 0) {
            list.innerHTML = "<div style='text-align:center; margin-top:50px; color:#999;'>Sua cesta está vazia.</div>";
            document.getElementById('cart-footer').style.display = "none";
            return;
        }
        let total = carrinho.reduce((acc, i) => acc + i.sub, 0) + 7;
        list.innerHTML = carrinho.map((it, idx) => `
            <div class="list-item">
                <div style="font-size:14px;"><b>${it.n}</b><br><small>${it.qtd}${it.tipo} x R$ ${it.p.toFixed(2)}</small></div>
                <div style="text-align:right">
                    <b style="color:var(--primary-green)">R$ ${it.sub.toFixed(2)}</b><br>
                    <span onclick="remCart(${idx})" style="color:red; cursor:pointer; font-size:11px;">Excluir</span>
                </div>
            </div>
        `).join('');
        document.getElementById('total-val').innerText = total.toFixed(2);
        document.getElementById('cart-footer').style.display = "block";
        resetPagamento();
    }

    function remCart(i) { 
        carrinho.splice(i,1); 
        document.getElementById('count').innerText = carrinho.length;
        renderCart(); 
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
                let totalComJuros = total * juros;
                sel.innerHTML += `<option value="${i}">${i}x de R$ ${(totalComJuros/i).toFixed(2)} (Total R$ ${totalComJuros.toFixed(2)})</option>`;
            }
        } else if(m === 'dinheiro') {
            document.getElementById('info-dinheiro').style.display = 'block';
        }
    }

    function resetPagamento() {
        metodoAtivo = "";
        document.querySelectorAll('.pay-btn').forEach(b => b.classList.remove('selected'));
        document.querySelectorAll('.info-box').forEach(i => i.style.display = 'none');
    }

    function copiarPix() { navigator.clipboard.writeText(chavePix); alert("Chave Pix copiada!"); }

    function enviarWhatsApp() {
        if(!metodoAtivo) return alert("Selecione como deseja pagar!");
        let totalVal = document.getElementById('total-val').innerText;
        let pInfo = `Pagamento: ${metodoAtivo.toUpperCase()}`;
        if(metodoAtivo === 'credito') pInfo += ` em ${document.getElementById('select-parcelas').options[document.getElementById('select-parcelas').selectedIndex].text}`;
        if(metodoAtivo === 'dinheiro') pInfo += ` (Troco p/ R$ ${document.getElementById('troco-input').value || 'Não solicitado'})`;

        let msg = `*NOVO PEDIDO - MEDELA*\n\n*Cliente:* ${userAtivo.nome}\n*Endereço:* ${userAtivo.end}\n\n`;
        carrinho.forEach(i => msg += `• ${i.n} (${i.qtd}${i.tipo}) = R$ ${i.sub.toFixed(2)}\n`);
        msg += `\n*Taxa Entrega:* R$ 7,00\n*TOTAL:* R$ ${totalVal}\n\n*${pInfo}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }

    // --- ADMIN CORE ---
    function acessoAdmin() {
        if(prompt("Senha de acesso:") === SENHA_MESTRA) {
            localStorage.setItem('admin_session_v6', 'true');
            isAdmin = true;
            irPara('screen-admin');
        } else { alert("Senha incorreta!"); }
    }

    function sairAdmin() {
        localStorage.removeItem('admin_session_v6');
        isAdmin = false;
        location.reload();
    }

    function salvarConfig() {
        chavePix = document.getElementById('adm-chave-pix').value;
        localStorage.setItem('medela_pix_v6', chavePix);
        alert("Configuração atualizada!");
    }

    function admAdicionar() {
        const n = document.getElementById('adm-nome').value;
        const f = document.getElementById('adm-foto').value;
        const c = document.getElementById('adm-cat-select').value;
        const t = document.getElementById('adm-tipo').value;
        const p = parseFloat(document.getElementById('adm-preco').value);
        if(!n || !p) return alert("Preencha os dados do produto!");

        estoque.push({id: Date.now(), n, cat: c, tipo: t, p, foto: f});
        localStorage.set
