<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - V10</title>
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
        .card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); display: flex; flex-direction: column; justify-content: space-between;}
        .card img { width: 100%; height: 80px; object-fit: contain; margin-bottom: 5px; }

        .btn { background: var(--green); color: white; border: none; padding: 12px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; margin-top: 5px; }
        .input-group { margin-bottom: 10px; width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }

        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .nav-btn { font-size: 11px; color: #aaa; border: none; background: none; font-weight: bold; cursor: pointer; }
        
        .admin-item { background: white; padding: 10px; margin-bottom: 5px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange); }
        .chat-box { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px; background: #e5ddd5; margin-bottom: 80px; }
        .msg { padding: 10px; border-radius: 10px; max-width: 80%; font-size: 14px; }
        .msg-client { background: #dcf8c6; align-self: flex-end; }
        .msg-admin { background: #fff; align-self: flex-start; }
    </style>
</head>
<body>

<section id="scr-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <input type="text" id="l-cpf" placeholder="CPF (000.000.000-00)" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <button class="btn" onclick="login()">ENTRAR</button>
        <p style="text-align:center; font-size:12px;">Novo? <span style="color:var(--orange); cursor:pointer" onclick="go('scr-reg')">Cadastre-se</span></p>
        <p style="text-align:center; font-size:10px; color:#ccc; margin-top:60px;" onclick="askAdmin()">PAINEL DO ADMINISTRADOR</p>
    </div>
</section>

<section id="scr-reg" class="app-screen">
    <div class="header"><h2>Cadastro</h2></div>
    <div class="container">
        <input type="text" id="r-nome" placeholder="Nome Completo" class="input-group">
        <input type="text" id="r-cpf" placeholder="CPF" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="text" id="r-end" placeholder="Endereço de Entrega" class="input-group">
        <button class="btn" onclick="register()">FINALIZAR</button>
        <button class="btn" style="background:#888" onclick="go('scr-login')">VOLTAR</button>
    </div>
</section>

<section id="scr-home" class="app-screen">
    <div class="header">
        <h3 id="u-name" style="margin:0">Olá!</h3>
        <input type="text" placeholder="🔍 Buscar item..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="cat-bar" id="cat-loja"></div>
    <div id="p-grid" class="grid"></div>
</section>

<section id="scr-cart" class="app-screen">
    <div class="header"><h2>Minha Cesta</h2></div>
    <div class="container" id="c-list" style="padding-bottom: 220px;"></div>
    <div id="c-foot" class="container" style="position:fixed; bottom:60px; background:white; width:100%; max-width:450px; left:50%; transform:translateX(-50%); border-top:1px solid #eee; display:none;">
        <h3 style="color:var(--green)">Total: R$ <span id="t-val">0,00</span></h3>
        <button class="btn" style="background:#25d366" onclick="sendZap()">ENVIAR PARA WHATSAPP</button>
    </div>
</section>

<section id="scr-suporte" class="app-screen">
    <div class="header"><h2>Mensagens</h2></div>
    <div id="chat-cliente" class="chat-box"></div>
    <div class="container" style="position:fixed; bottom:60px; background:#f0f0f0; width:100%; max-width:450px; left:50%; transform:translateX(-50%); display:flex; padding:10px; gap:5px;">
        <input type="text" id="msg-input" placeholder="Mensagem..." style="flex:1; border-radius:20px; border:1px solid #ccc; padding:10px;">
        <button onclick="enviarMensagem()" style="background:var(--green); color:white; border:none; border-radius:50%; width:40px;">➔</button>
    </div>
</section>

<section id="scr-admin" class="app-screen">
    <div class="header"><h2>Administração</h2></div>
    <div class="cat-bar">
        <div class="cat-item active-cat" id="tab-p" onclick="abaAdmin('produtos')">Estoque</div>
        <div class="cat-item" id="tab-m" onclick="abaAdmin('mensagens')">Suporte</div>
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
        document.getElementById('bot-nav').style.display = (id !== 'scr-login' && id !== 'scr-reg' && !isAdmin) ? 'flex' : 'none';
        if(id === 'scr-home') renderHome();
        if(id === 'scr-cart') renderCart();
        if(id === 'scr-suporte') renderChatCliente();
    }

    // --- LOJA ---
    function setCatLoja(c) {
        curCat = c;
        document.getElementById('cat-loja').innerHTML = CATS.map(cat => `<div class="cat-item ${cat===curCat?'active-cat':''}" onclick="setCatLoja('${cat}')">${cat}</div>`).join('');
        renderHome();
    }

    function renderHome(s = "") {
        if(!document.getElementById('cat-loja').innerHTML) setCatLoja(curCat);
        const filtered = db.filter(p => p.cat === curCat && p.nome.toLowerCase().includes(s.toLowerCase()));
        document.getElementById('p-grid').innerHTML = filtered.map(p => `
            <div class="card">
                <img src="${p.foto || 'https://via.placeholder.com/80?text=📦'}" onerror="this.src='https://via.placeholder.com/80?text=📦'">
                <b style="font-size:11px;">${p.nome}</b>
                <span style="color:var(--green); font-weight:bold; font-size:14px;">R$ ${p.preco.toFixed(2)} / ${p.tipo}</span>
                <div style="display:flex; align-items:center; gap:2px;">
                    <input type="number" id="qty-${p.id}" value="1" step="${p.tipo==='KG'?'0.1':'1'}" style="width:45px; padding:4px;">
                    <button class="btn" style="margin:0; padding:6px; font-size:10px;" onclick="addToCart(${p.id})">ADD</button>
                </div>
            </div>
        `).join('');
    }

    function addToCart(id) {
        const p = db.find(x => x.id === id);
        const qty = parseFloat(document.getElementById(`qty-${id}`).value);
        if(qty <= 0) return;
        cart.push({...p, qty: qty, total: p.preco * qty});
        document.getElementById('count').innerText = cart.length;
        alert("Adicionado!");
    }

    function renderCart() {
        const cL = document.getElementById('c-list');
        if(cart.length===0) { cL.innerHTML="<p style='text-align:center; margin-top:50px; color:#999;'>Sua cesta está vazia.</p>"; document.getElementById('c-foot').style.display="none"; return; }
        cL.innerHTML = cart.map((p,i) => `
            <div class="admin-item">
                <span style="font-size:13px;"><b>${p.nome}</b><br><small>${p.qty}${p.tipo} x R$ ${p.preco.toFixed(2)}</small></span>
                <b>R$ ${p.total.toFixed(2)} <span onclick="remCart(${i})" style="color:red; margin-left:10px; cursor:pointer">X</span></b>
            </div>
        `).join('');
        let total = cart.reduce((a,b)=>a+b.total,0) + 7;
        document.getElementById('t-val').innerText = total.toFixed(2);
        document.getElementById('c-foot').style.display="block";
    }

    function remCart(i) { cart.splice(i,1); renderCart(); document.getElementById('count').innerText = cart.length; }

    // --- SUPORTE ---
    function renderChatCliente() {
        const box = document.getElementById('chat-cliente');
        const conversa = chats[user.cpf] || [];
        box.innerHTML = conversa.map(m => `<div class="msg ${m.from === 'user' ? 'msg-client' : 'msg-admin'}">${m.text}</div>`).join('');
        box.scrollTop = box.scrollHeight;
    }

    function enviarMensagem() {
        const inp = document.getElementById('msg-input');
        if(!inp.value) return;
        if(!chats[user.cpf]) chats[user.cpf] = [];
        chats[user.cpf].push({ from: 'user', text: inp.value });
        localStorage.setItem('m_chats', JSON.stringify(chats));
        inp.value = ""; renderChatCliente();
    }

    // --- ADMIN (CATEGORIA + KG/UN ADICIONADO) ---
    function abaAdmin(tipo) {
        const cont = document.getElementById('adm-content');
        document.getElementById('tab-p').classList.toggle('active-cat', tipo === 'produtos');
        document.getElementById('tab-m').classList.toggle('active-cat', tipo === 'mensagens');
        
        if(tipo === 'produtos') {
            cont.innerHTML = `
                <div style="background:white; padding:15px; border-radius:10px; box-shadow:0 2px 5px rgba(0,0,0,0.1);">
                    <h4 style="margin-top:0;">Cadastrar Item</h4>
                    <input type="text" id="a-nome" placeholder="Nome do Produto" class="input-group">
                    <input type="text" id="a-foto" placeholder="Link da Foto (URL)" class="input-group">
                    <input type="number" id="a-preco" placeholder="Preço (0.00)" step="0.01" class="input-group">
                    
                    <label style="font-size:12px; font-weight:bold;">Categoria:</label>
                    <select id="a-cat" class="input-group">${CATS.map(c => `<option>${c}</option>`).join('')}</select>
                    
                    <label style="font-size:12px; font-weight:bold;">Tipo de Venda:</label>
                    <select id="a-tipo" class="input-group">
                        <option value="UN">UNIDADE (UN)</option>
                        <option value="KG">QUILO (KG)</option>
                    </select>
                    
                    <button class="btn" onclick="addProd()">SALVAR NO ESTOQUE</button>
                </div>
                <hr style="margin:20px 0; opacity:0.2;">
                <input type="text" placeholder="🔍 Pesquisar para excluir..." oninput="renderAdmin(this.value)" style="width:100%; padding:10px; border-radius:8px; border:1px solid #ddd; margin-bottom:10px;">
                <div id="a-list"></div>
                <button class="btn" style="background:#333; margin-top:20px;" onclick="sairAdmin()">DESLOGAR ADMIN</button>
            `;
            renderAdmin();
        } else {
            cont.innerHTML = `<h4>Conversas por CPF</h4><div id="adm-chats-list"></div><button class="btn" style="background:#333; margin-top:20px;" onclick="sairAdmin()">VOLTAR</button>`;
            renderListaChatsAdmin();
        }
    }

    function addProd() {
        const n = document.getElementById('a-nome').value;
        const f = document.getElementById('a-foto').value;
        const p = parseFloat(document.getElementById('a-preco').value);
        const c = document.getElementById('a-cat').value;
        const t = document.getElementById('a-tipo').value;
        if(!n || !p) return alert("Nome e Preço são obrigatórios!");
        db.push({id: Date.now(), nome: n, foto: f, preco: p, cat: c, tipo: t});
        localStorage.setItem('m_db', JSON.stringify(db));
        alert("Cadastrado com sucesso!");
        abaAdmin('produtos');
    }

    function renderAdmin(s="") {
        const filtered = db.filter(p=>p.nome.toLowerCase().includes(s.toLowerCase()));
        document.getElementById('a-list').innerHTML = filtered.map(p=>`
            <div class="admin-item">
                <span style="font-size:12px;"><b>${p.nome}</b><br><small>${p.cat} | ${p.tipo}</small></span>
                <button onclick="remProd(${p.id})" style="background:red; color:white; border:none; border-radius:5px; padding:5px 10px; cursor:pointer;">X</button>
            </div>
        `).join('');
    }

    function renderListaChatsAdmin() {
        const list = document.getElementById('adm-chats-list');
        list.innerHTML = Object.keys(chats).map(cpf => `
            <div class="admin-item" onclick="abrirChatAdmin('${cpf}')" style="cursor:pointer">
                <span><b>CPF: ${cpf}</b><br><small>Clique para responder</small></span>
                <span>➔</span>
            </div>
        `).join('') || "Nenhuma mensagem.";
    }

    function abrirChatAdmin(cpf) {
        const resp = prompt(`Histórico de ${cpf}:\n${chats[cpf].map(m => (m.from==='user'?'CLIENTE: ':'EU: ')+m.text).join('\n')}\n\nSua resposta:`);
        if(resp) {
            chats[cpf].push({ from: 'admin', text: resp });
            localStorage.setItem('m_chats', JSON.stringify(chats));
            renderListaChatsAdmin();
        }
    }

    // --- FUNÇÕES GERAIS ---
    function remProd(id) { db = db.filter(p=>p.id!==id); localStorage.setItem('m_db', JSON.stringify(db)); renderAdmin(); }
    function askAdmin() { if(prompt("Senha:")==="123") { localStorage.setItem('m_is_admin','true'); isAdmin=true; location.reload(); } }
    function logout() { localStorage.removeItem('m_user'); location.reload(); }
    function sairAdmin() { localStorage.removeItem('m_is_admin'); location.reload(); }
    function login() { const cpf = document.getElementById('l-cpf').value, s = localStorage.getItem(`u_${cpf}`); if(s) { user = JSON.parse(s); localStorage.setItem('m_user', JSON.stringify(user)); location.reload(); } else alert("CPF não cadastrado"); }
    function register() { const n = document.getElementById('r-nome').value, c = document.getElementById('r-cpf').value, e = document.getElementById('r-end').value; if(!n||c.length<14) return alert("Preencha tudo corretamente"); localStorage.setItem(`u_${c}`, JSON.stringify({nome:n,cpf:c,end:e})); localStorage.setItem('m_user', JSON.stringify({nome:n,cpf:c,end:e})); location.reload(); }
    function maskCPF(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2"); i.value = v; }
    function sendZap() { 
        let t = document.getElementById('t-val').innerText;
        let itens = cart.map(p => `- ${p.nome} (${p.qty}${p.tipo}): R$ ${p.total.toFixed(2)}`).join('\n');
        let msg = `*PEDIDO MEDELA*\n\n*Cliente:* ${user.nome}\n*Endereço:* ${user.end}\n\n*Produtos:*\n${itens}\n\n*TOTAL COM TAXA: R$ ${t}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`); 
    }
</script>
</body>
</html>
