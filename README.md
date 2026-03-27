<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado - App</title>
    <style>
        :root { --primary-green: #00a859; --orange-main: #f37021; --bg-gray: #f4f4f4; --white: #ffffff; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; color: #333; }
        
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; background: white; }
        .active { display: flex; flex-direction: column; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* FORMULÁRIOS */
        .logo-area { text-align: center; margin: 30px 0; }
        .logo-area h1 { color: var(--primary-green); margin: 0; }
        .logo-area span { color: var(--orange-main); }
        .input-group { margin-bottom: 12px; }
        .input-group label { display: block; font-size: 11px; color: #666; margin-bottom: 4px; font-weight: bold; }
        .input-group input, .input-group select { width: 100%; padding: 14px; border: 1px solid #ddd; border-radius: 8px; background: #f9f9f9; box-sizing: border-box; font-size: 14px; }
        
        /* BOTÕES */
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 16px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 15px; }
        .btn-add { background: var(--orange-main); padding: 10px; font-size: 12px; margin-top: 10px; border: none; color: white; border-radius: 5px; cursor: pointer; width: 100%; }
        .btn-remove { background: #ff4444; padding: 6px 10px; border-radius: 5px; color: white; border: none; cursor: pointer; font-size: 10px; }

        /* HOME E GRID */
        .header-app { background: linear-gradient(135deg, var(--orange-main), #ff8c42); padding: 40px 20px; color: white; border-radius: 0 0 25px 25px; }
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 20px; padding-bottom: 100px; }
        .item-card { background: white; border: 1px solid #eee; border-radius: 12px; padding: 15px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .item-card img { width: 70px; height: 70px; margin-bottom: 10px; object-fit: contain; }
        .item-name { font-size: 13px; font-weight: bold; display: block; margin-bottom: 5px; }

        /* CARRINHO */
        .cart-container { padding: 20px; padding-bottom: 220px; }
        .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 15px; border-bottom: 1px solid #eee; background: #fff; margin-bottom: 8px; border-radius: 10px; border: 1px solid #f0f0f0; }
        .empty-cart-msg { text-align: center; padding: 50px 20px; color: #999; }
        
        /* PAGAMENTO E FIXO */
        .payment-box { background: #f9f9f9; padding: 15px; border-radius: 10px; margin-top: 20px; border: 1px dashed #ccc; }
        #troco-box { display: none; margin-top: 10px; }

        .footer-fixed { position: fixed; bottom: 70px; background: white; width: 100%; max-width: 450px; border-top: 2px solid var(--primary-green); padding: 15px 20px; box-sizing: border-box; z-index: 90; }

        /* NAV */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 100; }
        .nav-item { font-size: 10px; color: #bbb; text-align: center; cursor: pointer; border: none; background: none; position: relative; width: 33%; }
        .nav-active { color: var(--primary-green); font-weight: bold; }
        .badge { background: red; color: white; border-radius: 50%; padding: 2px 6px; font-size: 10px; position: absolute; top: -5px; right: 25%; }
    </style>
</head>
<body>

    <section id="screen-login" class="app-screen active">
        <div class="container">
            <div class="logo-area"><h1>Medela<span> Supermercado</span></h1><p>Entre para comprar</p></div>
            <div class="input-group"><label>CPF ou E-mail (Gmail)</label><input type="text" id="login-id" placeholder="Digite aqui"></div>
            <div class="input-group"><label>Senha</label><input type="password" id="login-pass" placeholder="••••••••"></div>
            <button class="btn-green" onclick="executarLogin()">Entrar</button>
            <p style="text-align:center; color: var(--primary-green); font-weight:bold; cursor:pointer; margin-top:25px;" onclick="irPara('screen-register')">Novo por aqui? Cadastre-se</p>
        </div>
    </section>

    <section id="screen-register" class="app-screen">
        <div class="container">
            <h2 style="color:var(--primary-green)">Cadastro Medela</h2>
            <div class="input-group"><label>* Nome Completo</label><input type="text" id="reg-nome"></div>
            <div class="input-group"><label>* CPF</label><input type="text" id="reg-cpf" oninput="mascaraCPF(this)"></div>
            <div class="input-group"><label>* Telefone/WhatsApp</label><input type="text" id="reg-tel" placeholder="(00) 00000-0000"></div>
            <div class="input-group"><label>* Gmail</label><input type="email" id="reg-email"></div>
            <div class="input-group"><label>* Endereço Completo</label><input type="text" id="reg-endereco" placeholder="Rua, Número, Bairro"></div>
            <div class="input-group"><label>* Senha</label><input type="password" id="reg-senha"></div>
            <button class="btn-green" onclick="finalizarCadastro()">Criar Minha Conta</button>
            <p style="text-align:center; cursor:pointer; margin-top:15px;" onclick="irPara('screen-login')">Voltar</p>
        </div>
    </section>

    <section id="screen-home" class="app-screen">
        <header class="header-app"><h2 id="user-display">Olá!</h2><p>Escolha seus produtos abaixo:</p></header>
        <div class="market-grid">
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/1134/1134444.png"><span class="item-name">Contra Filé KG</span><strong>R$ 39,90</strong><button class="btn-add" onclick="addAoCarrinho('Contra Filé', 39.90)">Adicionar</button></div>
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/415/415733.png"><span class="item-name">Banana Prata KG</span><strong>R$ 5,50</strong><button class="btn-add" onclick="addAoCarrinho('Banana Prata', 5.50)">Adicionar</button></div>
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/3014/3014545.png"><span class="item-name">Pão Francês KG</span><strong>R$ 14,90</strong><button class="btn-add" onclick="addAoCarrinho('Pão Francês', 14.90)">Adicionar</button></div>
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/2674/2674486.png"><span class="item-name">Leite Integral 1L</span><strong>R$ 6,20</strong><button class="btn-add" onclick="addAoCarrinho('Leite Integral', 6.20)">Adicionar</button></div>
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/2553/2553642.png"><span class="item-name">Detergente 500ml</span><strong>R$ 2,50</strong><button class="btn-add" onclick="addAoCarrinho('Detergente', 2.50)">Adicionar</button></div>
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/3122/3122042.png"><span class="item-name">Cerveja Lata</span><strong>R$ 4,50</strong><button class="btn-add" onclick="addAoCarrinho('Cerveja Lata', 4.50)">Adicionar</button></div>
        </div>
    </section>

    <section id="screen-cart" class="app-screen">
        <header class="header-app"><h2>Meu Carrinho</h2><p>Revise e finalize seu pedido</p></header>
        
        <div class="cart-container">
            <div id="cart-list">
                <div class="empty-cart-msg">Seu carrinho está vazio.<br>Volte para a aba de produtos!</div>
            </div>
            
            <div id="checkout-area" style="display:none;">
                <div class="payment-box">
                    <div class="input-group">
                        <label>FORMA DE PAGAMENTO</label>
                        <select id="pay-method" onchange="checkPayMethod()">
                            <option value="Cartão de Crédito">Cartão de Crédito</option>
                            <option value="Cartão de Débito">Cartão de Débito</option>
                            <option value="Pix">Pix</option>
                            <option value="Dinheiro">Dinheiro</option>
                        </select>
                    </div>
                    <div id="troco-box" class="input-group">
                        <label>TROCO PARA QUANTO?</label>
                        <input type="text" id="troco-valor" placeholder="Ex: 100,00">
                    </div>
                    <div style="margin-top:15px; border-top: 1px solid #eee; padding-top:10px;">
                        <p style="font-size: 13px; color: #444; margin: 5px 0;">Taxa de Entrega: <strong>R$ 5,00</strong></p>
                        <p style="font-size: 11px; color: #888;">Entregar em: <br><span id="display-address" style="color:var(--orange-main); font-weight:bold;"></span></p>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="footer-fixed" id="cart-footer" style="display:none;">
            <p style="font-size: 18px; margin: 10px 0;"><strong>Total Geral: R$ <span id="cart-total">0,00</span></strong></p>
            <button class="btn-green" onclick="enviarPedido()">✅ Finalizar via WhatsApp</button>
        </div>
    </section>

    <nav class="bottom-nav">
        <button class="nav-item nav-active" id="nav-home" onclick="irPara('screen-home')">PRODUTOS</button>
        <button class="nav-item" id="nav-cart" onclick="irPara('screen-cart')">CARRINHO <span id="cart-count" class="badge">0</span></button>
        <button class="nav-item" onclick="location.reload()">SAIR</button>
    </nav>

    <script>
        let carrinho = [];
        let usuarioLogado = null;
        const TAXA_ENTREGA = 5.00;

        function irPara(id) {
            document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('nav-active'));
            
            document.getElementById(id).classList.add('active');
            
            if(id === 'screen-home') document.getElementById('nav-home').classList.add('nav-active');
            if(id === 'screen-cart') {
                document.getElementById('nav-cart').classList.add('nav-active');
                if(usuarioLogado) document.getElementById('display-address').innerText = usuarioLogado.endereco;
                atualizarInterfaceCarrinho();
            }
            window.scrollTo(0,0);
        }

        function mascaraCPF(i) {
            let v = i.value.replace(/\D/g, "");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
            i.value = v;
        }

        function checkPayMethod() {
            const method = document.getElementById('pay-method').value;
            document.getElementById('troco-box').style.display = (method === 'Dinheiro') ? 'block' : 'none';
        }

        function finalizarCadastro() {
            const dados = {
                nome: document.getElementById('reg-nome').value,
                cpf: document.getElementById('reg-cpf').value,
                tel: document.getElementById('reg-tel').value,
                email: document.getElementById('reg-email').value.toLowerCase(),
                endereco: document.getElementById('reg-endereco').value,
                senha: document.getElementById('reg-senha').value
            };

            if(!dados.nome || !dados.cpf || !dados.email) { alert("Preencha os campos obrigatórios!"); return; }

            localStorage.setItem(dados.cpf, JSON.stringify(dados));
            localStorage.setItem(dados.email, JSON.stringify(dados));
            alert("Cadastro realizado com sucesso!");
            irPara('screen-login');
        }

        function executarLogin() {
            const id = document.getElementById('login-id').value.toLowerCase();
            const senha = document.getElementById('login-pass').value;
            const user = JSON.parse(localStorage.getItem(id));

            if (user && user.senha === senha) {
                usuarioLogado = user;
                document.getElementById('user-display').innerText = "Olá, " + user.nome.split(' ')[0] + "!";
                irPara('screen-home');
            } else { alert("Login ou Senha incorretos!"); }
        }

        function addAoCarrinho(nome, preco) {
            carrinho.push({ nome, preco });
            document.getElementById('cart-count').innerText = carrinho.length;
            // Efeito visual simples
            const btn = event.target;
            const original = btn.innerText;
            btn.innerText = "✅ Adicionado";
            btn.style.background = "#008c4a";
            setTimeout(() => { btn.innerText = original; btn.style.background = "var(--orange-main)"; }, 800);
        }

        function removerDoCarrinho(index) {
            carrinho.splice(index, 1);
            atualizarInterfaceCarrinho();
            document.getElementById('cart-count').innerText = carrinho.length;
        }

        function atualizarInterfaceCarrinho() {
            const list = document.getElementById('cart-list');
            const footer = document.getElementById('cart-footer');
            const checkout = document.getElementById('checkout-area');
            const totalText = document.getElementById('cart-total');
            
            if (carrinho.length === 0) {
                list.innerHTML = '<div class="empty-cart-msg">Seu carrinho está vazio.<br>Volte para a aba de produtos!</div>';
                footer.style.display = "none";
                checkout.style.display = "none";
                return;
            }

            list.innerHTML = "";
            let subtotal = 0;
            carrinho.forEach((item, index) => {
                subtotal += item.preco;
                list.innerHTML += `
                    <div class="cart-item">
                        <div><strong>${item.nome}</strong><br><small>R$ ${item.preco.toFixed(2)}</small></div>
                        <button class="btn-remove" onclick="removerDoCarrinho(${index})">Remover</button>
                    </div>`;
            });

            totalText.innerText = (subtotal + TAXA_ENTREGA).toFixed(2);
            footer.style.display = "block";
            checkout.style.display = "block";
        }

        function enviarPedido() {
            const formaPagamento = document.getElementById('pay-method').value;
            const troco = document.getElementById('troco-valor').value;

            let texto = `*NOVO PEDIDO - MEDELA SUPERMERCADO*\n\n`;
            texto += `*CLIENTE:* ${usuarioLogado.nome}\n`;
            texto += `*WHATSAPP:* ${usuarioLogado.tel}\n`;
            texto += `*CPF:* ${usuarioLogado.cpf}\n`;
            texto += `*ENDEREÇO:* ${usuarioLogado.endereco}\n`;
            texto += `----------------------------\n`;
            texto += `*ITENS:*\n`;
            carrinho.forEach(item => { texto += `• ${item.nome} (R$ ${item.preco.toFixed(2)})\n`; });
            texto += `----------------------------\n`;
            texto += `*TOTAL (C/ ENTREGA): R$ ${document.getElementById('cart-total').innerText}*\n`;
            texto += `*PAGAMENTO:* ${formaPagamento}\n`;
            if(formaPagamento === 'Dinheiro' && troco) texto += `*TROCO PARA:* R$ ${troco}\n`;

            const url = `https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(texto)}`;
            window.open(url, '_blank');
        }
    </script>
</body>
</html>
