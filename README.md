<!DOCTYPE html>
<html lang="en"><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Prorate Calculator</title>
<style>
/* ---------- Design tokens: one blue brand colour + neutral slate; green = discount, amber = tax ---------- */
:root{
  --bg:#f1f4f9;--card:#fff;--ink:#16202e;--mut:#5d6b7e;--line:#dbe2ec;--field:#f8fafd;
  --brand:#1f4fd8;--brand-d:#173ca6;--brand-bg:#e8eefc;--on-brand:#fff;
  --disc:#0f7b4f;--disc-bg:#e4f5ec;--tax:#a35a00;--tax-bg:#fff1dc;--bad:#c42b2b;--bad-bg:#fdeaea;
  --hero1:#173ca6;--hero2:#1f4fd8;
  box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){
  --bg:#0f141c;--card:#18202b;--ink:#e7ecf3;--mut:#9aa7b8;--line:#2b3644;--field:#121922;
  --brand:#6b97ff;--brand-d:#8fb0ff;--brand-bg:#1a2a4d;--on-brand:#0b1220;
  --disc:#4cc58b;--disc-bg:#12301f;--tax:#f0b25e;--tax-bg:#392a10;--bad:#f58a8a;--bad-bg:#3a1b1b;
  --hero1:#14306f;--hero2:#1f4fd8}}
:root[data-theme="dark"]{
  --bg:#0f141c;--card:#18202b;--ink:#e7ecf3;--mut:#9aa7b8;--line:#2b3644;--field:#121922;
  --brand:#6b97ff;--brand-d:#8fb0ff;--brand-bg:#1a2a4d;--on-brand:#0b1220;
  --disc:#4cc58b;--disc-bg:#12301f;--tax:#f0b25e;--tax-bg:#392a10;--bad:#f58a8a;--bad-bg:#3a1b1b;
  --hero1:#14306f;--hero2:#1f4fd8}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--ink);font-family:system-ui,-apple-system,"Segoe UI",Roboto,Arial,sans-serif;padding:20px;line-height:1.4}

/* ---------- Layout ---------- */
.box{max-width:980px;margin:auto;background:var(--card);border:1px solid var(--line);border-radius:12px;overflow:hidden;box-shadow:0 2px 12px rgba(20,40,80,.08)}
.hero{background:linear-gradient(120deg,var(--hero1),var(--hero2));color:#fff;padding:22px}
.hero h1{font-size:22px;margin:0 0 4px}
.hero .sub{color:#dde6ff;margin:0;font-size:14px}
.in{padding:20px 22px 22px}
.step{display:flex;align-items:center;gap:10px;font-weight:700;margin:20px 0 10px;font-size:16px}
.step:first-child{margin-top:0}
.step i{font-style:normal;width:26px;height:26px;border-radius:50%;background:var(--brand);color:var(--on-brand);display:grid;place-items:center;font-size:14px}

/* ---------- Form controls ---------- */
.set{display:grid;grid-template-columns:repeat(auto-fit,minmax(190px,1fr));gap:14px;margin-bottom:6px}
.set>div{background:var(--brand-bg);padding:12px;border-radius:8px}
label{display:block;font-size:13px;font-weight:600;margin-bottom:5px}
input,select{width:100%;padding:9px 10px;border:1px solid var(--line);border-radius:6px;font:inherit;background:var(--field);color:var(--ink)}
input:focus,select:focus,button:focus-visible{outline:2px solid var(--brand);outline-offset:1px}
input.bad{border-color:var(--bad);background:var(--bad-bg)}
.hint{font-size:12px;color:var(--mut);margin:6px 0 0}
.ev{display:block;font-size:12px;font-weight:600;color:var(--brand);text-align:right;min-height:15px;margin-top:3px}
.ev.bad{color:var(--bad)}
.amtbox{display:flex;gap:4px;align-items:center}
.inf{background:transparent;color:var(--brand);padding:2px 6px;font-size:18px;line-height:1;border-radius:50%}
.inf:hover,.inf[aria-expanded="true"]{background:var(--brand-bg);color:var(--brand)}
button{font:inherit;border:0;border-radius:6px;padding:9px 14px;cursor:pointer;color:var(--on-brand);background:var(--brand);font-weight:600}
button:hover{background:var(--brand-d);color:#fff}
button.x{background:transparent;color:var(--bad);padding:6px 8px}
button.x:hover{background:var(--bad-bg);color:var(--bad)}
.bar{display:flex;gap:10px;margin-top:14px;flex-wrap:wrap;align-items:center}
kbd{font:inherit;font-size:12px;border:1px solid var(--line);border-bottom-width:2px;border-radius:4px;padding:1px 6px;background:var(--field);color:var(--mut)}
.err{color:var(--bad);font-size:13px;min-height:18px;margin-top:8px}

/* ---------- Table ---------- */
.wrap{overflow-x:auto;border:1px solid var(--line);border-radius:8px}
table{width:100%;border-collapse:collapse;min-width:760px}
th,td{padding:8px;border-bottom:1px solid var(--line);text-align:right;font-variant-numeric:tabular-nums;vertical-align:top}
th:first-child,td:first-child{text-align:left}
thead th{font-size:13px;font-weight:600;background:var(--brand-bg);color:var(--brand)}
tbody tr:nth-child(even){background:color-mix(in srgb,var(--field) 70%,transparent)}
td{padding-top:12px}
td input{padding:7px 8px}
td:first-child input{min-width:110px}
td:nth-child(2) input{min-width:130px;text-align:right}
td:nth-child(2){min-width:200px}
th:nth-child(4),td:nth-child(4){color:var(--disc);background:var(--disc-bg)}
th:nth-child(6),td:nth-child(6){color:var(--tax);background:var(--tax-bg)}
th:nth-child(7),td:nth-child(7){color:var(--brand);background:var(--brand-bg);font-weight:700}
tfoot td{font-weight:700;border-bottom:0;background:var(--brand-bg);color:var(--brand)}
tfoot td:nth-child(4){color:var(--disc);background:var(--disc-bg)}
tfoot td:nth-child(6){color:var(--tax);background:var(--tax-bg)}

/* ---------- Explanation & results ---------- */
.explain{margin-top:14px;padding:12px 14px;background:var(--brand-bg);border-radius:8px;font-size:14px;line-height:1.5;border-left:4px solid var(--brand)}
.cards{display:grid;grid-template-columns:repeat(auto-fit,minmax(140px,1fr));gap:10px}
.card{border-radius:10px;padding:12px 14px;border:1px solid var(--line)}
.card span{display:block;font-size:13px;margin-bottom:4px}
.card strong{font-size:20px;font-variant-numeric:tabular-nums}
.c1,.c3{background:var(--brand-bg);color:var(--brand)}
.c2{background:var(--disc-bg);color:var(--disc)}
.c4{background:var(--tax-bg);color:var(--tax)}
.final{margin-top:12px;background:linear-gradient(120deg,var(--hero1),var(--hero2));color:#fff;border-radius:10px;padding:16px 18px;display:flex;justify-content:space-between;align-items:center;font-size:18px;gap:10px;flex-wrap:wrap}
.final b{font-size:28px;font-variant-numeric:tabular-nums}
.note{font-size:13px;color:var(--mut);padding:0 22px 20px;margin:0}
.pop{position:fixed;z-index:50;width:min(380px,calc(100vw - 16px));max-height:70vh;overflow:auto;background:var(--card);color:var(--ink);border:1px solid var(--line);border-top:3px solid var(--brand);border-radius:8px;box-shadow:0 8px 28px rgba(10,25,60,.25);padding:12px 14px;font-size:13px;text-align:left;font-weight:400}
.pop[hidden]{display:none}
.pt{font-weight:700;font-size:14px;color:var(--brand);margin-bottom:6px}
.pl{padding:5px 0;border-top:1px solid var(--line);overflow-wrap:anywhere;font-variant-numeric:tabular-nums}
.pl span{color:var(--mut)}.pl span:after{content:": "}
.pl b{font-weight:600}
.pl.d b{color:var(--disc)}.pl.t b{color:var(--tax)}.pl.f b{color:var(--brand)}
.pn{font-size:12px;color:var(--mut);padding:4px 0}
.pe{color:var(--bad);margin:4px 0}
@media (max-width:520px){body{padding:10px}.in{padding:16px 14px}}
</style>
</head>
<body>
<div class="box">
<div class="hero"><h1>Prorate calculator</h1>
<p class="sub">Split one discount fairly across your items, then add tax.</p></div>
<div class="in">

<div class="step"><i>1</i>Enter the discount and tax</div>
<div class="set">
  <div><label for="mode">Discount type</label>
    <select id="mode"><option value="amount">Total discount amount (prorated)</option><option value="percent">Discount % on each item</option></select></div>
  <div><label for="disc" id="discLabel">Total discount amount (₹)</label><input id="disc" type="text" inputmode="decimal" autocomplete="off" value="500"><span class="ev" id="discEv"></span></div>
  <div><label for="taxType">Tax type</label>
    <select id="taxType"><option value="percent">Percentage (%)</option><option value="amount">Amount (₹)</option></select></div>
  <div><label for="tax" id="taxLabel">Tax (%)</label><input id="tax" type="text" inputmode="decimal" autocomplete="off" value="16"><span class="ev" id="taxEv"></span></div>
</div>
<p class="hint">Every amount, discount and tax box accepts formulas with <code>+ − * / %</code> and brackets: <code>2+2</code>, <code>100*5+20</code>, <code>(500-50)/3</code>. <code>100+10%</code> adds 10% of 100 (= 110); <code>100*10%</code> is 10; <code>a % b</code> is the remainder. In a percent field, <code>10%</code> simply means 10. Click ⓘ beside an amount to see the full calculation.</p>

<div class="step"><i>2</i>List your items</div>
<div class="wrap">
<table>
<thead><tr><th>Item</th><th>Price (₹)</th><th>% of total</th><th>Discount (−)</th><th>Price after discount</th><th id="taxHead">Tax (+) %</th><th>You pay</th><th></th></tr></thead>
<tbody id="rows"></tbody>
<tfoot><tr><td>Total</td><td id="tA">0.00</td><td id="tS">0%</td><td id="tD">0.00</td><td id="tAD">0.00</td><td id="tT">0.00</td><td id="tF">0.00</td><td></td></tr></tfoot>
</table>
</div>
<div class="err" id="err" role="status"></div>
<div class="bar"><button id="add">+ Add item</button><span class="hint" style="margin:0">or tap <kbd>Alt</kbd> on its own</span></div>

<div class="explain" id="explain"></div>

<div class="step"><i>3</i>See the result</div>
<div class="cards">
  <div class="card c1"><span>Original total</span><strong id="sO">0.00</strong></div>
  <div class="card c2"><span>Total discount</span><strong id="sD">0.00</strong></div>
  <div class="card c3"><span>After discount</span><strong id="sAD">0.00</strong></div>
  <div class="card c4"><span id="sTL">Total tax</span><strong id="sT">0.00</strong></div>
</div>
<div class="final"><span>Final total to pay</span><b id="sF">0.00</b></div>
</div>
<p class="note">Rounded to cents. Any leftover cent goes to the item with the biggest remainder, so the parts always add up exactly.</p>
</div>

<div id="pop" class="pop" role="dialog" aria-label="Calculation details" hidden></div>
<script>
var rowsEl=document.getElementById("rows"),n=0;
var $=function(id){return document.getElementById(id)};
var f=function(c){return (c/100).toFixed(2)};

/* ---------- Safe formula evaluator (no eval) ----------
   Grammar: expr = term {(+|-) term}; term = unary {(*|/|%) unary};
   unary = (+|-) unary | postfix; postfix = primary {%}; primary = number | ( expr )
   A "%" is a postfix percent only when NOT followed by a number or "(" - otherwise it is modulo.
   Calculator-style: "a + b%" adds b% OF a, "a - b%" subtracts b% of a; "a * b%" is a*b/100.
   pctField=true: the field is already a percent, so a postfix % leaves the value unchanged. */
var num=function(x){return String(Math.round(x*1e6)/1e6)};
function prettyExpr(t){
  var isOp=function(x){return x!==undefined&&(x==="("||/^[\d.]/.test(x))},out="";
  for(var i=0;i<t.length;i++){
    var k=t[i],prev=t[i-1],next=t[i+1];
    if(/^[\d.]/.test(k)||k==="("||k===")")out+=k;
    else if(k==="%")out+=isOp(next)?" % ":"%";
    else{
      var un=(k==="+"||k==="-")&&(prev===undefined||prev==="("||/^[+\-*\/]$/.test(prev));
      out+=un?k:" "+(k==="*"?"×":k==="/"?"÷":k)+" ";
    }
  }
  return out;
}
function evaluate(src,pctField){
  var s=String(src==null?"":src).replace(/[₹,\s]/g,"");
  if(s==="")return {v:0,plain:true,empty:true,pretty:"0",notes:[]};
  if(!/^[0-9+\-*\/%().x×÷]+$/i.test(s))return {v:NaN};
  s=s.replace(/[x×]/gi,"*").replace(/÷/g,"/");
  var t=s.match(/\d*\.\d+|\d+\.?|[+\-*\/%()]/g)||[];
  if(t.join("")!==s)return {v:NaN};
  var p=0,bad=false,notes=[];
  var isOperand=function(x){return x!==undefined&&(x==="("||/^[\d.]/.test(x))};
  function expr(){
    var v=term().v;
    while(t[p]==="+"||t[p]==="-"){
      var o=t[p++],q=term();
      if(q.pct){var amt=v*q.v;notes.push(num(q.v*100)+"% of "+num(v)+" = "+num(amt));q={v:amt}}
      v=o==="+"?v+q.v:v-q.v;
    }
    return {v:v}.v}
  function term(){
    var r=unary(),v=r.v,pct=r.pct;
    while(t[p]==="*"||t[p]==="/"||t[p]==="%"){
      pct=false;
      var o=t[p++],w=unary().v;
      if(o==="*")v*=w;else if(o==="/"){if(w===0)bad=true;v/=w}else{if(w===0)bad=true;v%=w}}
    return {v:v,pct:pct}}
  function unary(){
    if(t[p]==="-"){p++;var r=unary();return {v:-r.v,pct:r.pct}}
    if(t[p]==="+"){p++;return unary()}
    var v=primary(),pc=false;
    while(t[p]==="%"&&!isOperand(t[p+1])){p++;pc=true;if(!pctField)v/=100}
    return {v:v,pct:pc&&!pctField}}
  function primary(){
    var x=t[p++];
    if(x==="("){var v=expr();if(t[p++]!==")")bad=true;return v}
    if(x!==undefined&&/^[\d.]/.test(x)){var n=parseFloat(x);if(isNaN(n))bad=true;return n}
    bad=true;return 0}
  var v=expr();
  if(bad||p!==t.length||!isFinite(v))return {v:NaN};
  return {v:v,plain:/^\d*\.?\d+$|^\d+\.$/.test(s),pretty:prettyExpr(t),notes:notes};
}
/* Read a field: returns the evaluation (invalid -> v=0, bad=true); flags the input and shows "= result" for formulas.
   The text typed by the user is never changed, so the original expression is preserved. */
function readField(input,evEl,pctField){
  var src=input.dataset.expr||input.value;   /* committed (Enter) fields keep their formula in data-expr */
  var r=evaluate(src,pctField),ok=!isNaN(r.v);
  input.classList.toggle("bad",!ok);
  if(evEl){evEl.classList.toggle("bad",!ok);
    evEl.textContent=!ok?"Invalid formula":(r.plain||r.empty?"":"= "+(Math.round(r.v*100)/100).toFixed(2));}
  r.bad=!ok;r.expr=src;if(!ok)r.v=0;
  return r;
}

/* Split `total` cents across weights, largest-remainder so parts add up exactly */
function share(total,w){
  var S=w.reduce(function(x,y){return x+y},0);
  if(S<=0||total<=0)return w.map(function(){return 0});
  var base=w.map(function(x){return Math.floor(total*x/S)});
  var left=total-base.reduce(function(x,y){return x+y},0);
  var order=w.map(function(x,i){return {i:i,r:(total*x)%S}}).sort(function(p,q){return q.r-p.r});
  for(var k=0;k<left;k++)base[order[k].i]++;
  return base;
}

function addRow(name,amt,focus){
  n++;
  var tr=document.createElement("tr");
  tr.innerHTML='<td><input type="text" class="nm"></td>'+
    '<td><div class="amtbox"><input type="text" inputmode="decimal" autocomplete="off" class="amt" placeholder="e.g. 100*5+20"><button class="inf" type="button" aria-label="Show calculation" aria-expanded="false" title="Show calculation">&#9432;</button></div><span class="ev"></span></td>'+
    '<td class="sh">0%</td><td class="d">0.00</td><td class="ad">0.00</td><td class="tx">0.00</td><td class="fi">0.00</td>'+
    '<td><button class="x" aria-label="Remove item">&#10005;</button></td>';
  tr.querySelector(".nm").value=name||"Item "+n;
  tr.querySelector(".amt").value=amt==null?"":amt;
  rowsEl.appendChild(tr);
  if(focus){tr.querySelector(".amt").focus();tr.scrollIntoView({block:"nearest"});}
}

function calc(){
  var mode=$("mode").value, taxType=$("taxType").value, anyBad=false;
  var dvR=readField($("disc"),$("discEv"),mode==="percent"), dv=dvR.v;
  var trR=readField($("tax"),$("taxEv"),taxType==="percent"), tr=trR.v;
  anyBad=dvR.bad||trR.bad;
  var rows=[].slice.call(rowsEl.children);
  var infos=rows.map(function(r){
    var x=readField(r.querySelector(".amt"),r.querySelector(".ev"),false);
    if(x.bad)anyBad=true;
    r._i={x:x,name:r.querySelector(".nm").value||"Item"};
    return x});
  var a=infos.map(function(x){return Math.round(x.v*100)});
  var S=a.reduce(function(x,y){return x+y},0), err="";
  var d=a.map(function(){return 0});

  /* discount */
  if(mode==="percent"){
    d=a.map(function(x){return Math.round(x*dv/100)});
  }else{
    var D=Math.round(dv*100);
    if(D>S&&S>0){D=S;err="Discount is larger than the total, so it was capped at "+f(S)+".";}
    d=share(D,a);
  }
  var ad=a.map(function(x,i){return x-d[i]});
  var ADs=ad.reduce(function(x,y){return x+y},0);

  /* tax */
  var tx;
  if(taxType==="percent"){
    tx=ad.map(function(x){return Math.round(x*tr/100)});
  }else{
    tx=share(Math.max(0,Math.round(tr*100)),ad);   /* fixed amount, shared by discounted price */
  }

  var tot={a:0,d:0,ad:0,t:0,f:0};
  rows.forEach(function(r,i){
    var fin=ad[i]+tx[i];r._i.a=a[i];r._i.d=d[i];r._i.ad=ad[i];r._i.tx=tx[i];r._i.fin=fin;
    r.querySelector(".sh").textContent=S?(a[i]/S*100).toFixed(1)+"%":"0%";
    r.querySelector(".d").textContent=f(d[i]);
    r.querySelector(".ad").textContent=f(ad[i]);
    r.querySelector(".tx").textContent=f(tx[i]);
    r.querySelector(".fi").textContent=f(fin);
    tot.a+=a[i];tot.d+=d[i];tot.ad+=ad[i];tot.t+=tx[i];tot.f+=fin;
  });
  var set=function(id,v){$(id).textContent=f(v)};
  set("tA",tot.a);set("tD",tot.d);set("tAD",tot.ad);set("tT",tot.t);set("tF",tot.f);
  set("sO",tot.a);set("sD",tot.d);set("sAD",tot.ad);set("sT",tot.t);set("sF",tot.f);
  $("tS").textContent=S?"100%":"0%";

  ctx={dvR:dvR,trR:trR,mode:mode,taxType:taxType,S:S,ADs:ADs,Dtot:tot.d};
  if(openRow)renderPop();

  /* tax labels */
  var tp=taxType==="percent";
  $("taxLabel").textContent=tp?"Tax (%)":"Tax (₹, total amount)";
  $("taxHead").textContent="Tax (+) "+(tp?"%":"₹");
  $("sTL").textContent=tp?"Total tax ("+(Math.round(tr*1e4)/1e4)+"%)":"Total tax (fixed ₹)";
  if(anyBad)err="Some formulas are not valid and were counted as 0. Use numbers, + - * / % and brackets.";
  $("err").textContent=err;

  /* explanation */
  var taxTxt=tp?(Math.round(tr*1e4)/1e4)+"% tax is added to what is left."
              :"a fixed tax of "+f(Math.round(tr*100))+" is shared by the discounted prices of the items.";
  var ex="",i0=a.findIndex(function(x){return x>0});
  if(mode==="percent"){
    ex="<b>How it works:</b> every item gets "+(Math.round(dv*1e4)/1e4)+"% off its price. Then "+taxTxt;
  }else if(i0>=0){
    var nm=rows[i0].querySelector(".nm").value||"Item";
    var pc=(a[i0]/S*100).toFixed(1);
    ex="<b>How it works:</b> the "+f(Math.min(Math.round(dv*100),S))+" discount is shared by price. <b>"+nm.replace(/</g,"&lt;")+"</b> is "+pc+"% of the total ("+f(a[i0])+" of "+f(S)+"), so it takes "+pc+"% of the discount = <b>"+f(d[i0])+"</b>. Then "+taxTxt;
  }else ex="Enter a price for at least one item to see how the discount is shared.";
  $("explain").innerHTML=ex;
}

/* ---------- Events ---------- */
$("mode").addEventListener("change",function(){
  var p=this.value==="percent";
  $("discLabel").textContent=p?"Discount (%)":"Total discount amount (₹)";
  $("disc").value=p?10:500;
  calc();
});
$("taxType").addEventListener("change",function(){
  $("tax").value=this.value==="percent"?16:0;
  calc();
});
$("add").addEventListener("click",function(){addRow("",0,true);calc();});
rowsEl.addEventListener("click",function(e){
  var b=e.target.closest&&e.target.closest("button.x");
  if(b){b.closest("tr").remove();calc();return;}
  var inf=e.target.closest&&e.target.closest("button.inf");
  if(inf){var tr=inf.closest("tr");if(openRow===tr)closePop();else openPop(tr,inf);}
});

/* ---------- Enter in an amount box: show the final amount; the formula is kept and comes back when you edit ---------- */
rowsEl.addEventListener("keydown",function(e){
  var inp=e.target;
  if(e.key!=="Enter"||!inp.classList||!inp.classList.contains("amt"))return;
  e.preventDefault();
  var src=inp.dataset.expr||inp.value,r=evaluate(src,false);
  if(!isNaN(r.v)&&!r.plain&&!r.empty){
    inp.dataset.expr=src;
    inp.value=(Math.round(r.v*100)/100).toFixed(2);
  }
  calc();inp.select();
});
rowsEl.addEventListener("input",function(e){          /* typing replaces the stored formula */
  if(e.target.classList.contains("amt"))delete e.target.dataset.expr;
});
rowsEl.addEventListener("focusin",function(e){        /* clicking back in restores the formula for editing */
  var inp=e.target;
  if(inp.classList.contains("amt")&&inp.dataset.expr){inp.value=inp.dataset.expr;delete inp.dataset.expr;inp.select();calc();}
});

/* ---------- Calculation details popup (the ⓘ icon) ---------- */
var pop=$("pop"),openRow=null,openBtn=null,ctx=null;
var esc=function(s){return String(s).replace(/[&<>"]/g,function(c){return {"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;"}[c]})};
var L=function(label,html,cls){return '<div class="pl '+(cls||"")+'"><span>'+label+'</span><b>'+html+'</b></div>'};
function closePop(){
  pop.hidden=true;
  if(openBtn)openBtn.setAttribute("aria-expanded","false");
  openRow=openBtn=null;
}
function openPop(tr,btn){
  closePop();openRow=tr;openBtn=btn;
  btn.setAttribute("aria-expanded","true");
  pop.hidden=false;renderPop();
}
function placePop(){
  var r=openBtn.getBoundingClientRect(),w=pop.offsetWidth,h=pop.offsetHeight;
  var left=Math.max(8,Math.min(r.left,window.innerWidth-w-8));
  var top=r.bottom+6;
  if(top+h>window.innerHeight-8)top=Math.max(8,r.top-h-6);
  pop.style.left=left+"px";pop.style.top=top+"px";
}
function inputNote(label,R){
  if(R.bad)return '<div class="pe">'+label+' "'+esc(R.expr)+'" is not valid and was counted as 0.</div>';
  if(R.plain||R.empty)return "";
  return '<div class="pn">'+label+' entered as '+esc(R.expr)+' ('+esc(R.pretty)+' = '+num(R.v)+')'+(R.notes.length?' - '+esc(R.notes.join("; ")):"")+'</div>';
}
function renderPop(){
  if(!openRow||!document.body.contains(openRow)||!openRow._i||!ctx||openRow._i.a===undefined){closePop();return}
  var i=openRow._i,x=i.x,h='<div class="pt">'+esc(i.name)+'</div>';
  var sh=ctx.S?(i.a/ctx.S*100).toFixed(1):"0.0";
  if(x.bad){
    h+='<div class="pe">Invalid formula "'+esc(x.expr)+'" - counted as 0. Use numbers, + - * / % and brackets.</div>';
  }else{
    h+=L("You entered",esc(x.expr.trim()||"(empty)"));
    h+=L("Calculation",(x.plain||x.empty)?esc(num(x.v))+" (entered directly)":esc(x.pretty)+" = "+esc(num(x.v)));
    if(x.notes.length)h+='<div class="pn">'+esc(x.notes.join("; "))+'</div>';
    if(Math.abs(i.a-x.v*100)>1e-6)h+='<div class="pn">Used as '+f(i.a)+' (rounded to cents)</div>';
  }
  h+=L("Share of total",f(i.a)+" ÷ "+f(ctx.S)+" = "+sh+"%");
  if(ctx.mode==="percent"){
    h+=L("Discount",f(i.a)+" × "+num(ctx.dvR.v)+"% = "+f(i.d),"d")+inputNote("Discount %",ctx.dvR);
  }else{
    h+=L("Discount",f(ctx.Dtot)+" total × "+sh+"% = "+f(i.d),"d")+inputNote("Discount",ctx.dvR);
  }
  h+=L("After discount",f(i.a)+" − "+f(i.d)+" = "+f(i.ad));
  if(ctx.taxType==="percent"){
    h+=L("Tax",f(i.ad)+" × "+num(ctx.trR.v)+"% = "+f(i.tx),"t")+inputNote("Tax %",ctx.trR);
  }else{
    h+=L("Tax","fixed "+f(Math.round(ctx.trR.v*100))+" × ("+f(i.ad)+" ÷ "+f(ctx.ADs)+") = "+f(i.tx),"t")+inputNote("Tax",ctx.trR);
  }
  h+=L("You pay",f(i.ad)+" + "+f(i.tx)+" = "+f(i.fin),"f");
  pop.innerHTML=h;placePop();
}
document.addEventListener("click",function(e){
  if(openRow&&!pop.contains(e.target)&&!(e.target.closest&&e.target.closest(".inf")))closePop();
});
document.addEventListener("keydown",function(e){if(e.key==="Escape"&&openRow){var b=openBtn;closePop();b.focus();}});
window.addEventListener("resize",function(){if(openRow)closePop();});
window.addEventListener("scroll",function(e){if(openRow&&!pop.contains(e.target))closePop();},true);
document.addEventListener("input",calc);

/* ---------- Alt key: a lone press-and-release adds a row ----------
   Fires on keyup, and only if no other key or mouse button was used while Alt was held,
   so Alt+Tab, Alt+Arrow, Alt+letter shortcuts etc. keep working as normal. */
var altSolo=false;
document.addEventListener("keydown",function(e){
  if(e.key==="Alt"){ if(!e.repeat&&!e.ctrlKey&&!e.shiftKey&&!e.metaKey)altSolo=true; }
  else altSolo=false;
});
document.addEventListener("mousedown",function(){altSolo=false;});
window.addEventListener("blur",function(){altSolo=false;});
document.addEventListener("keyup",function(e){
  if(e.key==="Alt"&&altSolo){
    altSolo=false;
    e.preventDefault();            /* stops the browser menu bar from grabbing focus on Windows */
    addRow("",0,true);calc();
  }
});

/* ---------- Start ---------- */
addRow("Item 1",2619.61);addRow("Item 2",1200);addRow("Item 3",850.5);
calc();
</script>
</body></html>
