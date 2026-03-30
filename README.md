<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Açougue Medela</title>

<style>
body { font-family: Arial; margin:0; background:#f5f5f5; }

.box {
  max-width: 320px;
  margin: 80px auto;
  background: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
}

input, button {
  width:100%;
  padding:10px;
  margin-top:10px;
}

button {
  background:#b30000;
  color:white;
  border:none;
}

header {
  background:#b30000;
  color:white;
  padding:15px;
  text-align:center;
}

.produto {
  background:white;
  margin:10px;
  padding:10px;
  border-radius:10px;
}

.admin {
  background:#111;
  color:white;
  padding:10px;
}
</style>
</head>
<body>

<!-- LOGIN -->
<div id="login" class="box">
<h2>Login</h2>
<input id="cpfLogin" placeholder="CPF">
<input id="senhaLogin" type="password" placeholder="Senha">
<button onclick="login()">Entrar</button>
<span onclick="mostrarCadastro()">Cadastrar</span>
</div>

<!-- CADASTRO -->
<div id="cadastro" class="box" style="display:none;">
<h2>Cadastro</h2>
<input id="nome" placeholder="Nome completo">
<input id="cpfCadastro" placeholder="CPF">
<input id="rg" placeholder="Identidade">
<input id="senhaCadastro" type="password" placeholder="Senha">
<button onclick="cadastrar()">Criar</button>
<span onclick="mostrarLogin()">Voltar</span>
</div>

<!-- APP CLIENTE -->
<div id="app" style="display:none;">
<header>Bem-vindo <span id="nomeUser"></span></header>
<div id="produtos"></div>

<h3>Chat</h3>
<div id="chatBox"></div>
<input id="msg">
<button onclick="enviarMsg()">Enviar</button>
</div>

<!-- ADMIN -->
<div id="admin" style="display:none;">
<div class="admin">👑 PAINEL ADMIN MASTER</div>

<h3>Criar Produto</h3>
<input id="nomeProd" placeholder="Nome">
<input id="precoProd" placeholder="Preço">
<button onclick="criarProduto()">Adicionar</button>

<h3>Produtos</h3>
<div id="listaAdmin"></div>

<h3>Usuários</h3>
<div id="usuarios"></div>

<h3>Chat</h3>
<div id="chatAdmin"></div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import {
  getFirestore,
  collection,
  addDoc,
  getDocs,
  updateDoc,
  deleteDoc,
  doc
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "SUA_KEY",
  authDomain: "SEU_DOMINIO",
  projectId: "SEU_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

// 🔑 ADMIN MASTER FIXO
const MASTER_CPF = "00000000000";
const MASTER_SENHA = "123456";

// TELAS
window.mostrarCadastro = () => {
  login.style.display="none";
  cadastro.style.display="block";
};

window.mostrarLogin = () => {
  cadastro.style.display="none";
  login.style.display="block";
};

// CADASTRO
window.cadastrar = async () => {
  await addDoc(collection(db,"usuarios"), {
    nome:nome.value,
    cpf:cpfCadastro.value,
    rg:rg.value,
    senha:senhaCadastro.value,
    admin:false
  });
  alert("Criado!");
  mostrarLogin();
};

// LOGIN
window.login = async () => {

  // 👑 MASTER LOGIN
  if(cpfLogin.value === MASTER_CPF && senhaLogin.value === MASTER_SENHA){
    login.style.display="none";
    admin.style.display="block";
    carregarAdmin();
    return;
  }

  const snap = await getDocs(collection(db,"usuarios"));

  snap.forEach(docu=>{
    const u = docu.data();

    if(u.cpf==cpfLogin.value && u.senha==senhaLogin.value){

      nomeUser.innerText = u.nome;
      login.style.display="none";

      if(u.admin){
        admin.style.display="block";
        carregarAdmin();
      } else {
        app.style.display="block";
        carregarProdutos();
      }
    }
  });
};

// PRODUTOS
window.criarProduto = async () => {
  await addDoc(collection(db,"produtos"),{
    nome:nomeProd.value,
    preco:precoProd.value
  });
  carregarAdmin();
};

async function carregarProdutos(){
  const snap = await getDocs(collection(db,"produtos"));
  produtos.innerHTML="";
  snap.forEach(d=>{
    const p = d.data();
    produtos.innerHTML += `
      <div class="produto">
        <h3>${p.nome}</h3>
        <p>R$ ${p.preco}</p>
      </div>`;
  });
}

// ADMIN
async function carregarAdmin(){

  // PRODUTOS
  const snap = await getDocs(collection(db,"produtos"));
  listaAdmin.innerHTML="";
  snap.forEach(d=>{
    const p = d.data();
    listaAdmin.innerHTML += `
      <div>
        ${p.nome} - ${p.preco}
        <button onclick="del('${d.id}')">Excluir</button>
      </div>`;
  });

  // USUÁRIOS
  const users = await getDocs(collection(db,"usuarios"));
  usuarios.innerHTML="";
  users.forEach(u=>{
    const data = u.data();
    usuarios.innerHTML += `
      <div>
        👤 ${data.nome} | CPF: ${data.cpf} | SENHA: ${data.senha}
        <br>
        <button onclick="tornarAdmin('${u.id}')">Tornar Admin</button>
      </div><hr>`;
  });

  // CHAT
  const chat = await getDocs(collection(db,"chat"));
  chatAdmin.innerHTML="";
  chat.forEach(c=>{
    const m = c.data();
    chatAdmin.innerHTML += `<p>${m.msg}</p>`;
  });
}

// PROMOVER ADMIN
window.tornarAdmin = async (id) => {
  await updateDoc(doc(db,"usuarios",id),{
    admin:true
  });
  alert("Agora é admin!");
  carregarAdmin();
};

// DELETE PRODUTO
window.del = async (id) => {
  await deleteDoc(doc(db,"produtos",id));
  carregarAdmin();
};

// CHAT
window.enviarMsg = async () => {
  await addDoc(collection(db,"chat"),{
    msg:msg.value
  });
};
</script>

</body>
</html>
