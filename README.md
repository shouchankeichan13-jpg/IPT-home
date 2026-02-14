<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>IPT V16.3 COMPLETE</title>
<style>
  * { box-sizing: border-box; }
  body { margin: 0; padding: 0; font-family: -apple-system, sans-serif; background: #ffffff; overflow: hidden; height: 100vh; width: 100vw; }

  /* [1] ダイヤル画面：平成レトロ */
  #dialPage { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100%; width: 100%; }
  .heisei-table { border: 2px solid #000; background: #ccc; border-collapse: collapse; }
  .dial-input { width: 80px; height: 60px; font-size: 24px; cursor: pointer; border: 2px solid #888; background: #eee; font-weight: bold; }
  .active-num { background: #ffff00 !important; border: 3px solid #ff0000 !important; transform: scale(1.05); }

  /* [2] 通話中画面：iPhoneスタイル */
  #callPage { 
    display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
    background: #000000; color: #ffffff; z-index: 100; transition: opacity 0.2s;
  }
  .call-container { display: flex; flex-direction: column; align-items: center; padding-top: 100px; height: 100%; }
  .iphone-name { font-size: 34px; font-weight: 300; margin-bottom: 8px; }
  .iphone-timer { font-size: 18px; color: #d1d1d1; margin-bottom: 60px; }
  
  .icon-grid { display: flex; justify-content: space-around; width: 300px; margin-bottom: 60px; }
  .icon-box { display: flex; flex-direction: column; align-items: center; width: 80px; }
  .circle-btn { width: 72px; height: 72px; border-radius: 50%; background: rgba(255,255,255,0.15); border: none; color: white; margin-bottom: 10px; font-size: 12px; cursor: pointer; transition: 0.2s; }
  .icon-label { font-size: 12px; color: white; }

  .end-btn-red { 
    width: 78px; height: 78px; border-radius: 50%; background: #FF3B30; border: none; 
    margin-top: auto; margin-bottom: 80px; cursor: pointer; color: white; font-weight: bold; font-size: 16px;
  }
</style>
</head>
<body onwheel="handleWheel(event)" onmousedown="handleMiddleClick(event)">

<div id="dialPage">
  <font size="5"><b>IPT V16.3</b></font><br>
  <table border="1" width="260" cellpadding="10" bgcolor="#F0F0F0" style="margin-bottom:20px;">
    <tr><td align="right" height="60"><font size="6" id="display">&nbsp;</font></td></tr>
  </table>
  <table class="heisei-table" border="1">
    <tr><td><input type="button" value="1" id="btn1" class="dial-input" onclick="add('1')"></td><td><input type="button" value="2" id="btn2" class="dial-input" onclick="add('2')"></td><td><input type="button" value="3" id="btn3" class="dial-input" onclick="add('3')"></td></tr>
    <tr><td><input type="button" value="4" id="btn4" class="dial-input" onclick="add('4')"></td><td><input type="button" value="5" id="btn5" class="dial-input" onclick="add('5')"></td><td><input type="button" value="6" id="btn6" class="dial-input" onclick="add('6')"></td></tr>
    <tr><td><input type="button" value="7" id="btn7" class="dial-input" onclick="add('7')"></td><td><input type="button" value="8" id="btn8" class="dial-input" onclick="add('8')"></td><td><input type="button" value="9" id="btn9" class="dial-input" onclick="add('9')"></td></tr>
    <tr><td><input type="button" value="*" id="btn*" class="dial-input" onclick="add('*')"></td><td><input type="button" value="0" id="btn0" class="dial-input" onclick="add('0')"></td><td><input type="button" value="#" id="btn#" class="dial-input" onclick="add('#')"></td></tr>
  </table>
  <br>
  <input type="button" value="電話をかける" onclick="startCall()" style="width:250px; height:60px; font-size:20px; cursor:pointer; background:#ccffcc; font-weight:bold; border:2px solid #000;">
</div>

<div id="callPage">
  <div class="call-container">
    <div class="iphone-name" id="target">発信中...</div>
    <div class="iphone-timer" id="timerText">接続中...</div>
    
    <div class="icon-grid">
      <div class="icon-box"><button id="muteBtn" class="circle-btn" onclick="toggleMute()">消音</button><span class="icon-label">消音</span></div>
      <div class="icon-box"><button id="keyBtn" class="circle-btn" onclick="alert('通話中ダイヤル有効')">キーパッド</button><span class="icon-label">キーパッド</span></div>
      <div class="icon-box"><button id="spBtn" class="circle-btn" onclick="toggleSpeaker()">スピーカー</button><span class="icon-label">スピーカー</span></div>
    </div>

    <button class="end-btn-red" onclick="location.reload()">終了</button>
  </div>
</div>

<script>
  // --- 音響設定 ---
  const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  const mainGain = audioCtx.createGain(); mainGain.connect(audioCtx.destination);
  const dtmf={'1':[697,1209],'2':[697,1336],'3':[697,1477],'4':[770,1209],'5':[770,1336],'6':[770,1477],'7':[852,1209],'8':[852,1336],'9':[852,1477],'*':[941,1209],'0':[941,1336],'#':[941,1477]};

  function playDTMF(f1,f2){
    const o1=audioCtx.createOscillator(); const o2=audioCtx.createOscillator(); const g=audioCtx.createGain();
    o1.frequency.value=f1; o2.frequency.value=f2; o1.connect(g); o2.connect(g); g.connect(mainGain);
    const n=audioCtx.currentTime; g.gain.setValueAtTime(0,n); g.gain.linearRampToValueAtTime(0.2,n+0.01);
    g.gain.setValueAtTime(0.2,n+0.15); g.gain.exponentialRampToValueAtTime(0.001,n+0.2);
    o1.start(n); o2.start(n); o1.stop(n+0.2); o2.stop(n+0.2);
  }

  function playRBT(){
    const c=audioCtx.createOscillator(); const m=audioCtx.createOscillator(); const mg=audioCtx.createGain(); const g=audioCtx.createGain();
    c.frequency.value=400; m.frequency.value=16; mg.gain.value=15;
    m.connect(mg); mg.connect(c.frequency); c.connect(g); g.connect(mainGain);
    let n=audioCtx.currentTime;
    for(let i=0; i<2; i++){
      let s=n+(i*3); g.gain.setValueAtTime(0,s); g.gain.linearRampToValueAtTime(0.12,s+0.05);
      g.gain.setValueAtTime(0.12,s+1.0); g.gain.linearRampToValueAtTime(0,s+1.05);
    }
    c.start(n); m.start(n); c.stop(n+5); m.stop(n+5);
  }

  // --- 操作ロジック ---
  var num=""; var cur=0; var keys=["1","2","3","4","5","6","7","8","9","*","0","#"];
  window.onload=()=>updateFocus();
  function add(n){ audioCtx.resume(); playDTMF(dtmf[n][0],dtmf[n][1]); num+=n; document.getElementById('display').innerHTML=num; }
  function handleWheel(e){
    if(document.getElementById('callPage').style.display==='block') return;
    document.getElementById('btn'+keys[cur]).classList.remove('active-num');
    cur=e.deltaY>0?(cur+1)%keys.length:(cur-1+keys.length)%keys.length;
    updateFocus(); e.preventDefault();
  }
  function handleMiddleClick(e){ if(e.button===1 && document.getElementById('dialPage').style.display!=='none'){ add(keys[cur]); e.preventDefault(); } }
  function updateFocus(){ document.getElementById('btn'+keys[cur]).classList.add('active-num'); }

  // --- 通話画面ボタン機能 ---
  let isMuted=false; let isSp=false;
  function toggleMute(){
    isMuted=!isMuted; const b=document.getElementById('muteBtn');
    b.style.background=isMuted?"white":"rgba(255,255,255,0.15)"; b.style.color=isMuted?"black":"white";
  }
  function toggleSpeaker(){
    isSp=!isSp; const b=document.getElementById('spBtn');
    b.style.background=isSp?"white":"rgba(255,255,255,0.15)"; b.style.color=isSp?"black":"white";
    mainGain.gain.setTargetAtTime(isSp?2.0:1.0, audioCtx.currentTime, 0.1);
  }

  // --- 発信・センサー ---
  if('ProximitySensor' in window){
    try {
      const s=new ProximitySensor({frequency:10});
      s.addEventListener('reading',()=>{
        const cp=document.getElementById('callPage');
        if(cp.style.display==='block'){
          if(s.near){ cp.style.opacity="0"; mainGain.gain.setTargetAtTime(0.1,audioCtx.currentTime,0.05); }
          else{ cp.style.opacity="1"; mainGain.gain.setTargetAtTime(isSp?2.0:1.0,audioCtx.currentTime,0.05); }
        }
      });
      s.start();
    }catch(e){}
  }

  function startCall(){
    if(num=="")return;
    if(window.confirm(num+" へ発信しますか？")){
      audioCtx.resume();
      document.getElementById('dialPage').style.display='none';
      document.getElementById('callPage').style.display='block';
      document.getElementById('target').innerHTML=num;
      playRBT();
      setTimeout(()=>{ document.getElementById('timerText').innerHTML="00:00"; startTimer(); }, 5000);
    }
  }

  function startTimer(){
    let s=0; setInterval(()=>{ s++; let m=Math.floor(s/60); let sc=s%60;
      document.getElementById('timerText').innerHTML=(m<10?'0'+m:m)+":"+(sc<10?'0'+sc:sc);
    },1000);
  }
</script>
</body>
</html>
