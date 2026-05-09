
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MUIMIA | Academia de Combate</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth-compat.js"></script>
    <style>
        :root { --primary-red: #cc0000; --dark-bg: #050505; }
        body { background-color: var(--dark-bg); color: white; font-family: 'Inter', sans-serif; }
        .red-gradient { background: linear-gradient(135deg, #ff0000 0%, #7a0000 100%); }
        .bg-admin { background-color: #111; border: 1px solid #333; }
        .input-dark { background: #000; border: 1px solid #222; color: #fff; padding: 1rem; border-radius: 0.5rem; outline: none; }
        .input-dark:focus { border-color: var(--primary-red); }
    </style>
</head>
<body>

    <nav class="p-4 flex justify-between items-center border-b border-zinc-800 sticky top-0 bg-black z-50">
        <div class="flex items-center gap-3">
            <div class="w-12 h-12 border-2 border-red-600 rounded-full flex items-center justify-center bg-zinc-900 overflow-hidden">
                <i class="fas fa-bear-combined text-red-600 text-xl"></i> </div>
            <h1 id="txt-logo" class="text-2xl font-black italic text-red-600 tracking-tighter">MUIMIA</h1>
        </div>
        <div class="flex items-center gap-4">
            <span id="user-display" class="hidden text-xs font-bold text-zinc-500"></span>
            <button id="btn-admin" onclick="toggleModal('admin-modal')" class="hidden bg-white text-black px-4 py-1 rounded text-[10px] font-black uppercase">Painel Admin</button>
            <button id="btn-login-ui" onclick="toggleModal('login-modal')" class="bg-red-600 text-white px-6 py-1 rounded font-bold text-sm">LOGIN</button>
            <button id="btn-logout" onclick="logout()" class="hidden text-zinc-600 text-xs hover:text-white"><i class="fas fa-sign-out-alt"></i></button>
        </div>
    </nav>

    <header class="py-20 text-center px-4 relative overflow-hidden">
        <div class="absolute top-0 left-1/2 -translate-x-1/2 w-full h-full opacity-10 pointer-events-none">
            <i class="fas fa-skull text-[20rem]"></i>
        </div>
        <h2 id="hero-title" class="text-6xl md:text-8xl font-black uppercase italic mb-4">ESMAGUE A <span class="text-red-600">PREGUIÇA</span></h2>
        <p id="hero-desc" class="text-zinc-400 mb-8 tracking-[0.3em] text-xs uppercase">Boxing • Muay Thai • Jiu-Jitsu • MMA</p>
        <div class="bg-zinc-900/50 backdrop-blur-md inline-block p-8 rounded-2xl border border-zinc-800">
            <h3 id="plan-price" class="text-5xl font-black mb-4">R$ 149,90<span class="text-lg text-zinc-500">/mês</span></h3>
            <button onclick="toggleModal('payment-modal')" class="red-gradient w-full py-4 rounded-xl font-black shadow-xl hover:scale-105 transition">PAGAMENTO AUTOMÁTICO</button>
        </div>
    </header>

    <section class="max-w-5xl mx-auto grid grid-cols-1 md:grid-cols-2 gap-4 px-6 pb-20">
        <div class="bg-zinc-900 p-6 rounded-xl border-l-4 border-red-600">
            <h4 id="m1-n" class="font-bold text-xl uppercase italic">Boxing & Muay Thai</h4>
            <p id="m1-d" class="text-zinc-500 text-sm">Técnica em pé e condicionamento extremo.</p>
        </div>
        <div class="bg-zinc-900 p-6 rounded-xl border-l-4 border-red-600">
            <h4 id="m2-n" class="font-bold text-xl uppercase italic">Jiu-Jitsu & MMA</h4>
            <p id="m2-d" class="text-zinc-500 text-sm">Domínio no chão e artes integradas.</p>
        </div>
    </section>

    <div id="login-modal" class="hidden fixed inset-0 bg-black/95 flex items-center justify-center p-4 z-[60]">
        <div class="bg-zinc-900 p-8 rounded-3xl w-full max-w-md border border-zinc-800 shadow-2xl">
            <div id="login-view">
                <h3 class="text-2xl font-black text-red-600 mb-6 italic uppercase tracking-tighter">Área do Atleta</h3>
                <input type="email" id="user-email" placeholder="Seu Gmail" class="w-full input-dark mb-4">
                <input type="password" id="user-pass" placeholder="Sua Senha" class="w-full input-dark mb-2">
                <button onclick="handleResetPassword()" class="text-xs text-zinc-500 hover:text-red-600 mb-6 block w-full text-right uppercase font-bold">Esqueci a senha (receber no Gmail)</button>
                <button onclick="login()" class="w-full red-gradient p-4 rounded-xl font-black uppercase">Entrar</button>
            </div>
            <button onclick="toggleModal('login-modal')" class="mt-6 w-full text-zinc-600 text-[10px] uppercase tracking-widest font-bold">Voltar ao site</button>
        </div>
    </div>

    <div id="admin-modal" class="hidden fixed inset-0 bg-black/98 flex items-center justify-center p-4 z-[70]">
        <div class="bg-admin p-8 rounded-3xl w-full max-w-2xl max-h-[90vh] overflow-y-auto">
            <h3 class="text-2xl font-black text-white mb-8 border-b border-zinc-800 pb-4 italic">CONFIGURAÇÕES DA ACADEMIA</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                    <label class="text-[10px] text-red-600 font-bold uppercase">Título Hero</label>
                    <input type="text" id="adm-title" class="w-full input-dark mt-1 text-sm">
                </div>
                <div>
                    <label class="text-[10px] text-red-600 font-bold uppercase">Preço Mensal</label>
                    <input type="text" id="adm-price" class="w-full input-dark mt-1 text-sm">
                </div>
                <div class="md:col-span-2">
                    <label class="text-[10px] text-red-600 font-bold uppercase">Modalidade 1</label>
                    <input type="text" id="adm-m1-n" class="w-full input-dark mt-1 text-sm mb-2">
                    <input type="text" id="adm-m1-d" class="w-full input-dark text-xs text-zinc-400">
                </div>
            </div>
            <button onclick="saveAdmin()" class="w-full bg-white text-black p-4 rounded-xl font-black uppercase mt-8 hover:bg-zinc-200">Salvar Alterações</button>
        </div>
    </div>

    <script>
        // CONFIGURAÇÃO: Coloque seu Gmail aqui
        const ADMINS = ["ruanassistec@gmail.com", "seuemail@gmail.com"];

        // Inicializar Firebase (Simulado para o exemplo, basta colocar suas chaves)
        function toggleModal(id) { document.getElementById(id).classList.toggle('hidden'); }

        function login() {
            const email = document.getElementById('user-email').value.toLowerCase();
            if(!email.includes('@')) return alert("Use um Gmail válido.");
            
            localStorage.setItem('muimia_session', email);
            alert("Login realizado com sucesso!");
            location.reload();
        }

        function handleResetPassword() {
            const email = document.getElementById('user-email').value;
            if(!email) return alert("Digite seu e-mail no campo acima primeiro.");
            
            // Aqui o Firebase enviaria o e-mail real: firebase.auth().sendPasswordResetEmail(email)
            alert("LINK ENVIADO!\n\nVerifique sua caixa de entrada no Gmail para cadastrar uma nova senha.");
            toggleModal('login-modal');
        }

        function checkAuth() {
            const user = localStorage.getItem('muimia_session');
            if(user) {
                document.getElementById('btn-login-ui').classList.add('hidden');
                document.getElementById('btn-logout').classList.remove('hidden');
                document.getElementById('user-display').innerText = user;
                document.getElementById('user-display').classList.remove('hidden');
                
                if(ADMINS.includes(user)) {
                    document.getElementById('btn-admin').classList.remove('hidden');
                }
            }
        }

        function saveAdmin() {
            const data = {
                title: document.getElementById('adm-title').value,
                price: document.getElementById('adm-price').value,
                m1n: document.getElementById('adm-m1-n').value,
                m1d: document.getElementById('adm-m1-d').value
            };
            localStorage.setItem('muimia_config', JSON.stringify(data));
            location.reload();
        }

        function loadConfig() {
            const cfg = JSON.parse(localStorage.getItem('muimia_config'));
            if(cfg) {
                if(cfg.title) document.getElementById('hero-title').innerText = cfg.title;
                if(cfg.price) document.getElementById('plan-price').innerHTML = `R$ ${cfg.price}<span class="text-lg text-zinc-500">/mês</span>`;
                if(cfg.m1n) document.getElementById('m1-n').innerText = cfg.m1n;
                if(cfg.m1d) document.getElementById('m1-d').innerText = cfg.m1d;
                
                // Preencher inputs do admin
                document.getElementById('adm-title').value = cfg.title;
                document.getElementById('adm-price').value = cfg.price;
            }
        }

        function logout() {
            localStorage.removeItem('muimia_session');
            location.reload();
        }

        window.onload = () => { checkAuth(); loadConfig(); };
    </script>
</body>
</html>
