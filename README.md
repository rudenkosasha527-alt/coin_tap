# coin_tap<!DOCTYPE html>

<html lang="ru">

<head>

<meta charset="UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Coin Tap</title>

<style>

*{box-sizing:border-box}

body{

 margin:0;background:#090d19;color:white;

 font-family:Arial,sans-serif;text-align:center;

 min-height:100vh;padding:25px 15px;

}

.app{max-width:420px;margin:auto}

h1{color:#ffd65a;font-size:32px;margin:12px}

.subtitle{color:#8994ad}

.balance{

 background:#151d32;border:1px solid #303c5c;

 border-radius:20px;padding:22px;margin:25px 0;

}

.balance small{color:#aab5ce}

.amount{font-size:38px;font-weight:bold;color:#ffd65a;margin:10px}

.coin{

 width:190px;height:190px;border-radius:50%;

 margin:25px auto;background:linear-gradient(145deg,#ffe68a,#e99a17);

 border:8px solid #ffdf62;

 box-shadow:0 0 35px #eaa51c66;

 display:flex;align-items:center;justify-content:center;

 font-size:95px;cursor:pointer;

 user-select:none;transition:transform .08s;

}

.coin:active{transform:scale(.92)}

.energy{color:#aab5ce;margin:15px}

.bar{height:12px;background:#28334d;border-radius:20px;overflow:hidden}

.fill{height:100%;background:#a36aff;width:100%;transition:width .2s}

.panel{

 background:#151d32;border-radius:16px;

 padding:18px;margin:15px 0;text-align:left;

}

.panel h3{margin-top:0;color:#ffd65a}

button{

 border:0;border-radius:12px;padding:13px 18px;

 background:#7655e9;color:white;font-weight:bold;

 cursor:pointer;font-size:15px;

}

button:disabled{opacity:.4}

.info{color:#aab5ce;font-size:13px}

</style>

</head>

<body>

<div class="app">

<h1>🪙 COIN TAP</h1>

<div class="subtitle">Tap • Collect • Upgrade</div>

<div class="balance">

 <small>ТВОЙ БАЛАНС</small>

 <div class="amount" id="balance">0 CT</div>

 <small>Виртуальные игровые очки</small>

</div>

<div class="energy">

 ⚡ Энергия: <span id="energy">1000</span>/1000

</div>

<div class="bar">

 <div class="fill" id="energyBar"></div>

</div>

<div class="coin" id="coin">🪙</div>

<div class="info">Нажимай на монету!</div>

<div class="panel">

 <h3>⚡ Улучшение</h3>

 <p>За одно нажатие: <b id="power">1 CT</b></p>

 <p>Цена улучшения: <b id="price">100 CT</b></p>

 <button id="upgrade">Улучшить</button>

</div>

<div class="panel">

 <h3>🎁 Ежедневный бонус</h3>

 <p>Получи 100 виртуальных CT раз в день.</p>

 <button id="daily">Получить бонус</button>

 <p class="info" id="dailyStatus"></p>

</div>

<div class="panel">

 <h3>📊 Статистика</h3>

 <p>Всего нажатий: <b id="clicks">0</b></p>

 <p>Уровень: <b id="level">1</b></p>

</div>

<div class="info">

 Coin Tap — развлекательная игра.

 CT не являются криптовалютой и не имеют денежной стоимости.

</div>

</div>

<script>

const $ = id => document.getElementById(id);

let data = JSON.parse(localStorage.getItem("coinTapData")) || {

 balance:0,

 energy:1000,

 power:1,

 clicks:0,

 level:1,

 lastDaily:"",

 lastTime:Date.now()

};

function save(){

 localStorage.setItem("coinTapData",JSON.stringify(data));

}

function render(){

 $("balance").textContent=Math.floor(data.balance)+" CT";

 $("energy").textContent=Math.floor(data.energy);

 $("energyBar").style.width=(data.energy/10)+"%";

 $("power").textContent=data.power+" CT";

 $("price").textContent=upgradePrice()+" CT";

 $("clicks").textContent=data.clicks;

 $("level").textContent=data.level;

 $("daily").disabled=data.lastDaily===new Date().toDateString();

 $("dailyStatus").textContent=$("daily").disabled?

 "Бонус уже получен сегодня!":"Бонус доступен!";

 save();

}

function upgradePrice(){

 return 100*data.power;

}

$("coin").addEventListener("click",()=>{

 if(data.energy<1){

  alert("Недостаточно энергии!");

  return;

 }

 data.energy-=1;

 data.balance+=data.power;

 data.clicks++;

 data.level=Math.floor(data.clicks/100)+1;

 render();

});

$("upgrade").addEventListener("click",()=>{

 let price=upgradePrice();

 if(data.balance<price){

  alert("Недостаточно CT!");

  return;

 }

 data.balance-=price;

 data.power++;

 render();

});

$("daily").addEventListener("click",()=>{

 let today=new Date().toDateString();

 if(data.lastDaily===today)return;

 data.balance+=100;

 data.lastDaily=today;

 render();

});

function regenerate(){

 let now=Date.now();

 let elapsed=(now-data.lastTime)/1000;

 data.energy=Math.min(1000,data.energy+elapsed*1);

 data.lastTime=now;

 render();

}

setInterval(regenerate,1000);