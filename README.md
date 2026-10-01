# 5-min-<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>5M Signal Bot</title>
<style>
body{background:#0d1117;color:white;font-family:Arial;text-align:center;padding:15px}
.box{max-width:500px;margin:auto;background:#161b22;padding:20px;border-radius:18px}
select,button{width:100%;padding:13px;margin:6px 0;border-radius:10px;font-size:16px}
.signal{font-size:38px;font-weight:bold;padding:25px;margin:15px 0;background:#21262d;border-radius:15px}
.info{background:#21262d;padding:12px;margin:7px;border-radius:10px}
</style>
</head>

<body>
<div class="box">
<h1>📈 5M Signal Bot</h1>

<select id="pair">
<option value="BTCUSDT">BTC/USDT</option>
<option value="ETHUSDT">ETH/USDT</option>
<option value="BNBUSDT">BNB/USDT</option>
<option value="SOLUSDT">SOL/USDT</option>
</select>

<button onclick="getSignal()">🔄 GET SIGNAL</button>

<div id="signal" class="signal">⚪ WAIT</div>

<div class="info">Price: <span id="price">-</span></div>
<div class="info">RSI: <span id="rsi">-</span></div>
<div class="info">EMA20: <span id="e20">-</span></div>
<div class="info">EMA50: <span id="e50">-</span></div>

<p id="status">Connecting...</p>

<small>
5-minute technical signal only. No automatic trading.
Signals are not guaranteed.
</small>
</div>

<script>
function EMA(a,n){
 let k=2/(n+1),e=a[0];
  for(let i=1;i<a.length;i++) e=a[i]*k+e*(1-k);
   return e;
   }

   function RSI(a,n=14){
    let gain=0,loss=0;
     for(let i=1;i<=n;i++){
       let d=a[i]-a[i-1];
         gain+=Math.max(d,0);
           loss+=Math.max(-d,0);
            }
             gain/=n; loss/=n;

              for(let i=n+1;i<a.length;i++){
                let d=a[i]-a[i-1];
                  gain=(gain*(n-1)+Math.max(d,0))/n;
                    loss=(loss*(n-1)+Math.max(-d,0))/n;
                     }

                      if(loss===0)return 100;
                       return 100-(100/(1+gain/loss));
                       }

                       async function getSignal(){

                        const pair=document.getElementById("pair").value;
                         const signal=document.getElementById("signal");

                          signal.innerHTML="⏳ ANALYZING...";
                           document.getElementById("status").innerHTML="Getting live 5M candles...";

                            try{

                              const url=
                                "https://api.binance.com/api/v3/klines?symbol="
                                  +pair+"&interval=5m&limit=100";

                                    const response=await fetch(url);

                                      if(!response.ok) throw new Error();

                                        const data=await response.json();

                                          const closes=data.map(x=>Number(x[4]));

                                            const price=closes[closes.length-1];

                                              const ema20=EMA(closes.slice(-80),20);
                                                const ema50=EMA(closes,50);
                                                  const rsi=RSI(closes);

                                                    document.getElementById("price").innerHTML=
                                                      price.toFixed(4);

                                                        document.getElementById("rsi").innerHTML=
                                                          rsi.toFixed(1);

                                                            document.getElementById("e20").innerHTML=
                                                              ema20.toFixed(4);

                                                                document.getElementById("e50").innerHTML=
                                                                  ema50.toFixed(4);

                                                                    if(price>ema20 && ema20>ema50 && rsi>50 && rsi<70){

                                                                        signal.innerHTML="🟢 UP";

                                                                          }else if(price<ema20 && ema20<ema50 && rsi<50 && rsi>30){

                                                                              signal.innerHTML="🔴 DOWN";

                                                                                }else{

                                                                                    signal.innerHTML="⚪ NO TRADE";

                                                                                      }

                                                                                        document.getElementById("status").innerHTML=
                                                                                          "✅ Live data updated: "+new Date().toLocaleTimeString();

                                                                                           }catch(e){

                                                                                             signal.innerHTML="⚠️ ERROR";

                                                                                               document.getElementById("status").innerHTML=
                                                                                                 "Live data load nahi hua. Internet check karein.";

                                                                                                  }
                                                                                                  }

                                                                                                  getSignal();
                                                                                                  setInterval(getSignal,30000);
                                                                                                  </script>

                                                                                                  </body>
                                                                                                  </html>