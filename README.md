<!DOCTYPE html>
<html>
<head>
<title>Teste Cassino</title>
</head>
<body style="background:black;color:white;text-align:center">

<h1>🎰 Cassino Teste</h1>

<p>Saldo: R$ <span id="saldo">100</span></p>

<input id="valor" placeholder="valor">

<br><br>

<button onclick="apostar()">Apostar</button>

<p id="res"></p>

<script>
let saldo = 100;

function apostar(){
  let valor = Number(document.getElementById("valor").value);

  if(valor > 0 && saldo >= valor){
    if(Math.random() > 0.5){
      saldo += valor;
      document.getElementById("res").innerText = "GANHOU";
    } else {
      saldo -= valor;
      document.getElementById("res").innerText = "PERDEU";
    }

    document.getElementById("saldo").innerText = saldo;
  }
}
</script>

</body>
</html>
