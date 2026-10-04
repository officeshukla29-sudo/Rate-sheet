<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>New Sales Rate Calculator</title>
<style>
:root{--ink:#0f172a;--mut:#475569;--line:#e2e8f0;--bg:#f8fafc;--teal:#0f766e;--tealL:#ccfbf1}
*{box-sizing:border-box}
body{margin:0;font:14px Arial,Helvetica,sans-serif;background:var(--bg);color:var(--ink)}
header{background:var(--ink);color:#fff;padding:14px 20px}
header h1{margin:0;font-size:20px}
header p{margin:4px 0 0;font-size:12px;color:#94a3b8}
nav{display:flex;gap:4px;padding:10px 20px 0;flex-wrap:wrap;border-bottom:2px solid var(--teal);background:#fff}
nav button{border:0;background:#e2e8f0;padding:9px 16px;font:inherit;font-weight:700;cursor:pointer;border-radius:6px 6px 0 0;color:var(--mut)}
nav button.on{background:var(--teal);color:#fff}
main{padding:16px 20px;max-width:1200px;margin:auto}
.tab{display:none}.tab.on{display:block}
.sel{display:grid;grid-template-columns:150px 200px;gap:8px;align-items:center;margin-bottom:12px}
select,input{font:inherit;padding:6px;border:1px solid #cbd5e1;border-radius:4px}
.sel select{background:#ffff00;font-weight:700;color:#0000ff}
.wrap{overflow-x:auto;background:#fff;border:1px solid var(--line)}
table{border-collapse:collapse;width:100%}
th,td{border:1px solid var(--line);padding:7px 10px;text-align:center;white-space:nowrap}
td:first-child,th:first-child{text-align:left}
th{color:#fff;background:var(--ink)}
td.n{font-variant-numeric:tabular-nums}
.tot td{background:var(--teal);color:#fff;font-weight:700;font-size:15px}
.otc td{background:var(--tealL);font-weight:700}
.dim td{color:#64748b;font-size:12px}
td input{width:76px;text-align:center;color:#0000ff;border:1px solid transparent;background:transparent}
td input:hover,td input:focus{border-color:#94a3b8;background:#fff}
.ok{padding:4px 10px;border-radius:4px;font-weight:700}
.ok.y{background:#bbf7d0;color:#166534}.ok.x{background:#fecaca;color:#991b1b}
.quote{white-space:pre-wrap;background:#f0fdfa;border:1px solid var(--line);padding:12px;margin:8px 0;line-height:1.5}
.btn{background:var(--teal);color:#fff;border:0;padding:8px 14px;border-radius:4px;font:inherit;font-weight:700;cursor:pointer}
.btn.g{background:#64748b}
h3{margin:18px 0 8px;font-size:15px}
.note{font-size:12px;color:var(--mut);line-height:1.6;margin:10px 0}
.bar{display:flex;gap:8px;margin-bottom:10px;flex-wrap:wrap;align-items:center}
.g1{background:#0f172a}.g2{background:#2563eb}.g3{background:#0f766e}
@media print{nav,.bar,.btn{display:none}.tab{display:block!important}}
</style>
</head>
<body>
<header><h1>New Sales Rate Calculator</h1>
<p>Final Amount = Package Charge + OTC (Device Rental + Drop-wire + Deposit). Amount in Rs.</p></header>
<nav id="nav"></nav>
<main>
<section class="tab" id="t0">
 <div class="sel"><b>Plan</b><select id="cp"></select><b>ONT Type</b><select id="co"></select><b>Bandwidth (Mbps)</b><select id="cb"></select><b>STB Qty (auto)</b><span id="cs"></span><b>Status</b><span id="cst"></span></div>
 <div class="wrap"><table id="ct"></table></div>
 <h3>Customer Quote (copy for WhatsApp/Viber)</h3>
 <div class="quote" id="cq"></div>
 <button class="btn" id="cc">Copy quote</button>
 <p class="note">Available: Internet Only / Combo 1TV = 5G (50-200) + 6G (200-600) | Combo 2TV = 6G 400/500/600 | Combo 3TV = 6G 500/600.<br>Rates edit karna parey: Package Rates / Device Rates tab ma matra edit garnu.</p>
</section>
<section class="tab" id="t1">
 <div class="bar"><b>Filter:</b><select id="fp"><option value="">All plans</option></select><select id="fo"><option value="">All ONT</option><option>5G</option><option>6G</option></select></div>
 <div class="wrap"><table id="ft"></table></div>
</section>
<section class="tab" id="t2">
 <p class="note">Package charge only (New &amp; Renew). Blue cells are editable; all tabs update.</p>
 <div class="wrap"><table id="pt"></table></div>
</section>
<section class="tab" id="t3">
 <p class="note">Blue cells are editable. Rental incl. VAT.</p>
 <div class="wrap"><table id="dt"></table></div>
 <h3>Plan setup (assumption)</h3>
 <div class="wrap" style="max-width:300px"><table id="st"></table></div>
 <p class="note">Assumptions: 5G ONT = Dual-Band rates, 6G ONT = Wi-Fi 6 rates. 1TV = 1 STB Primary; 2TV = Primary + 1 Secondary; 3TV = Primary + 2 Secondary. Drop-wire once (with ONT). Deposit per device (ONT + each STB). ATV and Beacon not included.</p>
 <button class="btn g" id="rs">Reset all rates</button>
</section>
</main>
<script>
const D=["1M","3M","6M","12M"],M=[1,3,6,12];
const PL={"Internet Only":["#2563eb","#dbeafe"],"Combo 1TV":["#9333ea","#f3e8fe"],"Combo 2TV":["#ea580c","#ffedd5"],"Combo 3TV":["#dc2626","#fee2e2"]};
const P=Object.keys(PL);
const A=[["5G",50,[1100,3200,6270,11200]],["5G",80,[1200,3500,6840,12240]],["5G",100,[1400,4100,7980,14280]],["5G",150,[1500,4400,8550,15300]],["5G",200,[1650,4800,9405,16830]],["6G",200,[1650,4800,9405,16830]],["6G",400,[1800,5250,10260,18360]],["6G",500,[1900,5500,10830,19400]],["6G",600,[2100,6100,11970,22600]]];
const B=[["5G",50,[1350,3900,7470,13600]],["5G",80,[1450,4200,8040,14640]],["5G",100,[1650,4800,9180,16680]],["5G",150,[1750,5100,9750,17700]],["5G",200,[1900,5500,10605,19230]],["6G",200,[1900,5500,10605,19230]],["6G",400,[2050,5950,11460,20760]],["6G",500,[2150,6200,12030,21800]],["6G",600,[2350,6800,13170,25000]]];
const DEF={
pk:[...A.map(r=>["Internet Only",...r]),...B.map(r=>["Combo 1TV",...r]),
["Combo 2TV","6G",400,[2300,6650,12660,23160]],["Combo 2TV","6G",500,[2400,6900,13230,24200]],["Combo 2TV","6G",600,[2600,7500,14370,27400]],
["Combo 3TV","6G",500,[2650,7600,14430,26600]],["Combo 3TV","6G",600,[2850,8200,15570,29800]]],
dev:[["Dual-Band Rental (incl. VAT)",[3390,2260,1695,1130]],["Dual-Band Deposit",[1000,1000,1000,1000]],["Dual-Band Drop-wire",[1130,1130,1130,1130]],
["Wi-Fi 6 Rental (incl. VAT)",[3390,2260,1695,1130]],["Wi-Fi 6 Deposit",[1000,1000,1000,1000]],["Wi-Fi 6 Drop-wire",[1130,1130,1130,1130]],
["STB Primary Rental (incl. VAT)",[3390,2260,1695,1130]],["STB Secondary Rental (incl. VAT)",[2260,2260,2260,2260]],["STB Deposit",[1000,1000,1000,1000]]],
stb:{"Internet Only":0,"Combo 1TV":1,"Combo 2TV":2,"Combo 3TV":3}};
let S=JSON.parse(JSON.stringify(DEF));
try{const x=localStorage.getItem("nsrc");if(x)S=JSON.parse(x)}catch(e){}
const save=()=>{try{localStorage.setItem("nsrc",JSON.stringify(S))}catch(e){}};
const f=n=>Math.round(n).toLocaleString("en-US");
const $=id=>document.getElementById(id);
const dv=(o,i)=>{const k=o==="5G"?0:3,d=S.dev;return{rent:d[k][1][i],dep:d[k+1][1][i],drop:d[k+2][1][i]}};
function calc(p,o,i){const v=dv(o,i),n=S.stb[p],d=S.dev;
 const r={ont:v.rent,sp:n>=1?d[6][1][i]:0,ss:Math.max(n-1,0)*d[7][1][i],drop:v.drop,dO:v.dep,dS:n*d[8][1][i]};
 r.otc=r.ont+r.sp+r.ss+r.drop+r.dO+r.dS;return r}
const pkg=(p,o,b,i)=>{const r=S.pk.find(x=>x[0]===p&&x[1]===o&&x[2]===+b);return r?r[3][i]:null};
const opt=(a,sel)=>a.map(x=>`<option${x==sel?" selected":""}>${x}</option>`).join("");
// nav
const T=["Calculator","Final Amount","Package Rates","Device Rates"];
$("nav").innerHTML=T.map((t,i)=>`<button data-i="${i}">${t}</button>`).join("");
function show(i){document.querySelectorAll(".tab").forEach((e,j)=>e.classList.toggle("on",i==j));document.querySelectorAll("nav button").forEach((e,j)=>e.classList.toggle("on",i==j))}
$("nav").onclick=e=>{if(e.target.dataset.i)show(+e.target.dataset.i)};
// calculator
$("cp").innerHTML=opt(P,"Internet Only");$("co").innerHTML=opt(["5G","6G"],"5G");$("cb").innerHTML=opt([50,80,100,150,200,400,500,600],100);
function rCalc(){
 const p=$("cp").value,o=$("co").value,b=$("cb").value,ok=pkg(p,o,b,0)!==null;
 $("cs").textContent=S.stb[p];$("cst").innerHTML=`<span class="ok ${ok?"y":"x"}">${ok?"OK":"Combination not available"}</span>`;
 const c=D.map((_,i)=>calc(p,o,i)),pk=D.map((_,i)=>ok?pkg(p,o,b,i):0);
 const row=(l,fn,cls="")=>`<tr class="${cls}"><td>${l}</td>${D.map((_,i)=>`<td class="n">${fn(i)}</td>`).join("")}</tr>`;
 const tot=i=>pk[i]+c[i].otc;
 $("ct").innerHTML=`<tr>${["Component",...D].map(h=>`<th>${h}</th>`).join("")}</tr>`+
 row("Package Charge",i=>f(pk[i]))+row("Device Rental - ONT",i=>f(c[i].ont))+row("STB Primary Rental",i=>f(c[i].sp))+row("STB Secondary Rental",i=>f(c[i].ss))+
 row("Drop-wire",i=>f(c[i].drop))+row("Deposit - ONT",i=>f(c[i].dO))+row("Deposit - STB",i=>f(c[i].dS))+
 row("OTC Total",i=>f(c[i].otc),"otc")+row("TOTAL PAYABLE (New Sales)",i=>ok?f(tot(i)):"N/A","tot")+row("Per Month (approx)",i=>ok?f(tot(i)/M[i]):"N/A")+row("Months",i=>M[i],"dim");
 const L=["1 Month","3 Months","6 Months","12 Months"];
 $("cq").textContent=ok?`${p} | ${o} ONT | ${b} Mbps (New Connection)\n`+L.map((l,i)=>`${l}: Rs ${f(tot(i))} (Package ${f(pk[i])} + OTC ${f(c[i].otc)})`).join("\n")+"\nOTC = Device Rental + Drop-wire + Deposit":"Combination not available";
}
["cp","co","cb"].forEach(id=>$(id).onchange=rCalc);
$("cc").onclick=()=>{const t=$("cq").textContent;(navigator.clipboard?navigator.clipboard.writeText(t):Promise.reject()).then(()=>{$("cc").textContent="Copied"},()=>{const r=document.createRange();r.selectNodeContents($("cq"));getSelection().removeAllRanges();getSelection().addRange(r);$("cc").textContent="Selected - press Copy"});setTimeout(()=>$("cc").textContent="Copy quote",1800)};
// final
$("fp").innerHTML+=P.map(p=>`<option>${p}</option>`).join("");
function rFinal(){
 const fp=$("fp").value,fo=$("fo").value;
 let h=`<tr><th rowspan=2>Plan</th><th rowspan=2>ONT</th><th rowspan=2>BW (Mbps)</th><th rowspan=2>STB</th><th colspan=4 class="g1">Final Amount (Package + OTC)</th><th colspan=4 class="g2">Package Charge</th><th colspan=4 class="g3">OTC</th></tr><tr>`+
 [1,2,3].map(g=>D.map(d=>`<th class="g${g}">${d}</th>`).join("")).join("")+"</tr>";
 S.pk.filter(r=>(!fp||r[0]===fp)&&(!fo||r[1]===fo)).forEach(r=>{
  const [p,o,b,v]=r,[dk,lt]=PL[p],oc=D.map((_,i)=>calc(p,o,i).otc);
  h+=`<tr><td style="background:${lt};color:${dk};font-weight:700">${p}</td><td style="background:${lt};color:${dk};font-weight:700">${o}</td><td style="background:${lt};color:${dk};font-weight:700">${b}</td><td style="background:${lt};color:${dk};font-weight:700">${S.stb[p]}</td>`+
  v.map((x,i)=>`<td class="n" style="background:#f1f5f9;font-weight:700">${f(x+oc[i])}</td>`).join("")+v.map(x=>`<td class="n" style="color:#008000">${f(x)}</td>`).join("")+oc.map(x=>`<td class="n">${f(x)}</td>`).join("")+"</tr>"});
 $("ft").innerHTML=h}
$("fp").onchange=$("fo").onchange=rFinal;
// rate tables
const inp=(k,v)=>`<td><input type="number" data-k="${k}" value="${v}"></td>`;
function rRates(){
 $("pt").innerHTML=`<tr>${["Plan","ONT Type","Bandwidth (Mbps)",...D].map(h=>`<th>${h}</th>`).join("")}</tr>`+
 S.pk.map((r,j)=>`<tr><td style="background:${PL[r[0]][1]};color:${PL[r[0]][0]};font-weight:700">${r[0]}</td><td>${r[1]}</td><td>${r[2]}</td>${r[3].map((x,i)=>inp(`p.${j}.${i}`,x)).join("")}</tr>`).join("");
 $("dt").innerHTML=`<tr>${["Item",...D].map(h=>`<th>${h}</th>`).join("")}</tr>`+S.dev.map((r,j)=>`<tr><td>${r[0]}</td>${r[1].map((x,i)=>inp(`d.${j}.${i}`,x)).join("")}</tr>`).join("");
 $("st").innerHTML=`<tr><th>Plan</th><th>STB Qty</th></tr>`+P.map(p=>`<tr><td style="background:${PL[p][1]};color:${PL[p][0]};font-weight:700">${p}</td>${inp("s."+p,S.stb[p])}</tr>`).join("")}
document.addEventListener("input",e=>{const k=e.target.dataset.k;if(!k)return;const [t,a,b]=k.split("."),v=+e.target.value||0;
 if(t==="p")S.pk[a][3][b]=v;else if(t==="d")S.dev[a][1][b]=v;else S.stb[k.slice(2)]=v;save();rCalc();rFinal()});
$("rs").onclick=()=>{S=JSON.parse(JSON.stringify(DEF));save();rRates();rCalc();rFinal()};
rRates();rCalc();rFinal();show(0);
</script>
</body>
</html>
