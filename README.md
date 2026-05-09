
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MÜMIA | Painel do Guerreiro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        body { background-color: #0a0a0a; color: white; font-family: 'Inter', sans-serif; scroll-behavior: smooth; }
        .accent-gradient { background: linear-gradient(90deg, #f97316 0%, #ea580c 100%); }
        .bg-card { background-color: #121212; border: 1px solid #1f1f1f; }
        .text-accent { color: #f97316; }
        input { background: #000 !important; border: 1px solid #333 !important; color: white !important; padding: 12px; border-radius: 8px; width: 100%; outline: none; }
        input:focus { border-color: #f97316 !important; }
        .hidden { display: none !important; }
    </style>
</head>
<body>

    <nav class="p-5 flex justify-between items-center border-b border-zinc-800 sticky top-0 bg-black/90 backdrop-blur-md z-50">
        <div class="flex items-center gap-2 cursor-pointer" onclick="location.reload()">
            <div class="w-10 h-10 accent-gradient rounded flex items-center justify-center font-black text-black">M</div>
            <h1 class="text-xl font-black italic">MÜMIA</h1>
        </div>
        
        <div class="flex items-center gap-4">
            <div id="nav-guest" class="flex gap-4">
                <button onclick="openAuth('login')" class="text-xs font-bold uppercase tracking-widest">Entrar</button>
                <button onclick="openAuth('signup')" class="accent-gradient px-5 py-2 rounded-lg font-bold text-xs uppercase shadow-lg">Cadastrar</button>
            </div>
            
            <div id="nav-user" class="hidden flex items-center gap-4">
                <button id="admin-btn" onclick="toggleModal('admin-panel')" class="hidden bg-white text-black text-[10px] px-3 py-1 rounded font-black uppercase">Admin</button>
                <span id="user-name-display" class="text-xs font-bold text-zinc-500 uppercase italic"></span>
                <button onclick="logout()" class="text-zinc-600 hover:text-white text-sm"><i class="fas fa-sign-out-alt"></i></button>
            </div>
        </div>
    </nav>

    <main id="home-content">
        <section class="py-32 text-center px-6">
            <h2 class="text-7xl md:text-9xl font-black uppercase mb-6 leading-none italic">RENASÇA <br> <span class="text-accent">GUERREIRO.</span></h2>
            <p class="text-zinc-500 max-w-xl mx-auto mb-10 text-lg uppercase tracking-widest font-bold">A elite do Boxing, Muay Thai e Jiu-Jitsu.</p>
            <button onclick="openAuth('signup')" class="accent-gradient px-12 py-5 rounded-2xl font-black text-xl shadow-[0_0_30px_rgba(249,115,22,0.3)] hover:scale-105 transition">QUERO MINHA VAGA</button>
        </section>
    </main>

    <main id="user-dashboard" class="hidden min-h-screen p-6 max-w-6xl mx-auto py-12">
        <div class="flex flex-col md:flex-row justify-between items-end mb-12 border-b border-zinc-800 pb-8">
            <div>
                <h2 class="text-4xl font-black italic uppercase">Bem-vindo ao <span class="text-accent">Tatame</span></h2>
                <p class="text-zinc-500 font-bold uppercase tracking-tighter mt-2">Sua jornada de evolução continua.</p>
            </div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div class="bg-card p-8 rounded-3xl border-t-4 border-accent">
                <h4 class="text-xs font-black text-zinc-500 uppercase mb-4 tracking-widest">Seu Plano</h4>
                <p class="text-2xl font-black uppercase italic mb-2">Plano Black Mümia</p>
                <p class="text-accent font-bold mb-6">Status: Ativo (Recorrência)</p>
                <button class="w-full py-3 bg-zinc-800 rounded-lg font-bold text-xs uppercase hover:bg-zinc-700 transition">Gerenciar Pagamento</button>
            </div>

            <div class="bg-card p-8 rounded-3xl">
                <h4 class="text-xs font-black text-zinc-500 uppercase mb-4 tracking-widest">Segurança</h4>
                <p class="text-xl font-black uppercase italic mb-6">Alterar Senha</p>
                <button onclick="handleResetPassword()" class="w-full py-3 border border-zinc-700 rounded-lg font-bold text-xs uppercase hover:border-accent hover:text-accent transition">Receber Link no Gmail</button>
            </div>

            <div class="bg-card p-8 rounded-3xl">
                <h4 class="text-xs font-black text-zinc-500 uppercase mb-4 tracking-widest">Acesso Rápido</h4>
                <div class="space-y-3">
                    <div class="flex justify-between text-sm border-b border-zinc-800 pb-2"><span>Muay Thai</span> <span class="text-zinc-500">19:00h</span></div>
                    <div class="flex justify-between text-sm border-b border-zinc-800 pb-2"><span>Jiu-Jitsu</span> <span class="text-zinc-500">20:30h</span></div>
                </div>
            </div>
        </div>
    </main>

    <div id="auth-modal" class="hidden fixed inset-0 bg-black/98 flex items-center justify-center p-6 z-[100]">
        <div class="bg-card p-10 rounded-3xl w-full max-w-md shadow-2xl relative border border-zinc-800">
            <div id="view-login">
                <h3 class="text-3xl font-black italic uppercase text-accent mb-8">Login</h3>
                <input type="email" id="login-email" placeholder="Seu Gmail" class="mb-4">
                <input type="password" id="login-pass" placeholder="Senha" class="mb-4">
                <button onclick="handleLogin()" class="accent-gradient w-full py-4 rounded-xl font-black uppercase shadow-lg">Entrar</button>
                <p class="text-center text-xs text-zinc-600 mt-6 uppercase font-bold tracking-widest">Novo aqui? <button onclick="switchView('signup')" class="text-white underline">Criar Conta</button></p>
            </div>
            <div id="view-signup" class="hidden text-center">
                <h3 class="text-3xl font-black italic uppercase text-accent mb-8">Cadastro</h3>
                <input type="text" id="reg-name" placeholder="Nome Completo" class="mb-4">
                <input type="email" id="reg-email" placeholder="Gmail" class="mb-4">
                <button onclick="handleRegister()" class="accent-gradient w-full py-4 rounded-xl font-black uppercase shadow-lg">Confirmar</button>
                <button onclick="switchView('login')" class="mt-4 text-xs text-zinc-500 font-bold uppercase">Voltar</button>
            </div>
            <button onclick="toggleModal('auth-modal')" class="mt-8 w-full text-zinc-700 text-[10px] font-black uppercase tracking-widest">Fechar</button>
        </div>
    </div>

    <div id="admin-panel" class="hidden fixed inset-0 bg-black flex items-center justify-center p-6 z-[110]">
        <div class="bg-card p-8 rounded-3xl w-full max-w-2xl border-2 border-accent">
            <h3 class="text-2xl font-black italic mb-6">GERENCIAMENTO MÜMIA</h3>
            <label class="text-[10px] font-bold text-accent uppercase">Alterar Nome da Academia</label>
            <input type="text" id="adm-name-change" class="mt-1 mb-6">
            <button onclick="saveAdmin()" class="w-full bg-white text-black p-4 rounded-xl font-black uppercase hover:bg-zinc-200">Salvar Alterações</button>
        </div>
    </div>

    <script>
        const ADMIN_EMAILS = ["ruanassistec@gmail.com"];

        function toggleModal(id) { document.getElementById(id).classList.toggle('hidden'); }

        function openAuth(view) {
            toggleModal('auth-modal');
            switchView(view);
        }

        function switchView(view) {
            document.getElementById('view-login').classList.toggle('hidden', view !== 'login');
            document.getElementById('view-signup').classList.toggle('hidden', view !== 'signup');
        }

        function handleLogin() {
            const email = document.getElementById('login-email').value.toLowerCase();
            if(!email) return alert("Digite o e-mail.");
            
            localStorage.setItem('mumia_session', email);
            location.reload(); // Recarrega para aplicar o dashboard
        }

        function handleRegister() {
            const email = document.getElementById('reg-email').value.toLowerCase();
            if(!email) return alert("Digite o e-mail.");
            
            localStorage.setItem('mumia_session', email);
            location.reload();
        }

        function handleResetPassword() {
            const user = localStorage.getItem('mumia_session');
            alert(`Link enviado para ${user}! Verifique seu Gmail.`);
        }

        function logout() {
            localStorage.removeItem('mumia_session');
            location.reload();
        }

        // FUNÇÃO QUE CONTROLA O QUE APARECE
        window.onload = () => {
            const user = localStorage.getItem('mumia_session');
            const guestNav = document.getElementById('nav-guest');
            const userNav = document.getElementById('nav-user');
            const homeContent = document.getElementById('home-content');
            const userDashboard = document.getElementById('user-dashboard');

            if (user) {
                // Usuário Logado
                guestNav.classList.add('hidden');
                homeContent.classList.add('hidden');
                userNav.classList.remove('hidden');
                userDashboard.classList.remove('hidden');
                document.getElementById('user-name-display').innerText = user;

                if (ADMIN_EMAILS.includes(user)) {
                    document.getElementById('admin-btn').classList.remove('hidden');
                }
            }
        };
    </script>
</body>
</html>
