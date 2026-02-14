<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>IPT電話 V15.1</title>
<style>
  body { margin: 0; font-family: sans-serif; background: #ffffff; overflow: hidden; touch-action: manipulation; }
  .heisei-table { border: 2px solid #000; background: #ccc; }
  .dial-input { width: 75px; height: 55px; font-size: 22px; cursor: pointer; border: 2px solid #888; background: #eee; font-weight: bold; }
  .active-num { background: #ffff00 !important; border: 3px solid #ff0000 !important; transform: scale(1.05); outline: none; }

  #callPage { 
    display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
    background: #000000; color: #ffffff; text-align: center; z-index: 100;
    transition: opacity 0.2s;
  }
  .iphone-name { font-size: 32px; margin-top: 80px; }
  .iphone-timer { font-size: 18px; margin-top: 10px; color: #d1d1d1; }
  .circle-btn { width: 75px; height: 75px; border-radius: 50%; background: rgba(255,255,255,0.15); border: none; color: white; font-size: 12px; margin: 10px; }
  .end-btn-red { width: 75px; height: 75px; border-radius: 50%; background: #FF3B30; border: none; margin-top: 100px; cursor: pointer; }
  .end-btn-red:after { content: "終了"; color: white; font-weight: bold; }
</style>
</head>
<body onwheel="handleWheel(event)" onmousedown="handleMiddleClick(event)">

<div id="dialPage">
  <center>
    <br><font size="5"><b>IPT 電話 V15.1</b></font><br>
    <font size="2" color="gray">改良：キレのあるプッシュ音 / 耳当て感知対応</font><br><br>
    <table border="1" width="280" cellpadding="10" bgcolor="#F0F0F0">
      <tr><td align="right" height="60"><font size="6" id="display">&nbsp;</font></td></tr>
    </table>
    <br>
    <table class="heisei-table" border="1" cellpadding="5">
      <tr>
        <td><input type="button" value="1" id="btn1" class="dial-input" onclick="add('1')"></td>
        <td><input type="button" value="2" id="btn2" class="dial-input" onclick="add('2')"></td>
        <td><input type="button" value="3" id="btn3" class="dial-input" onclick="add('3')"></td>
      </tr>
      <tr>
        <td><input type="button" value="4" id="btn4" class="dial-input" onclick="add('4')"></td>
        <td><input type="button" value="5" id="btn5" class="dial-input" onclick="add('5')"></td>
        <td><input type="button" value="6" id="btn6" class="dial-input" onclick="add('6')"></td>
      </tr>
      <tr>
        <td><input type="button" value="7" id="btn7" class="dial-input" onclick="add('7')"></td>
        <td><input type="button" value="8" id="btn8" class="dial-input" onclick="add('8')"></td>
        <td><input type="button" value="9" id="btn9" class="dial-input" onclick="add('9')"></td>
      </tr>
      <tr>
        <td><input type="button" value="*" id="btn*" class="dial-input" onclick="add('*')"></td>
        <td><input type="button" value="0" id="btn0" class="dial-input" onclick="add('0')"></td>
        <td><input type="button" value="#" id="btn#" class="dial-input" onclick="add('#')"></td>
      </tr>
    </table>
    <br>
    <input type="button" value="電話をかける" onclick="startCall()" style="width:250px; height:60px; font-size:22px; cursor:pointer; background:#ccffcc; font-weight:bold; border-radius:10px;">
  </center>
</div>

<div id="callPage">
  <div class="iphone-name" id="target">発信中...</div>
  <div class="iphone-timer" id="timerText">接続中...</div>
  <center>
    <div style="width: 300px; margin-top: 50px;">
      <button class="circle-btn">消音</button><button class="circle-btn">キーパッド</button><button class="circle-btn">スピーカー</button>
      <button class="circle-btn">通話追加</button><button class="circle-btn">FaceTime</button><button class="circle-btn">連絡先</button>
    </div>
    <button class="end-btn-red" onclick="location.reload()"></button>
  </center>
</div>

<script>
  // --- 鋭いプッシュ音エンジン ---
  const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  const mainGain = audioCtx.createGain(); 
  mainGain.connect(audioCtx.destination);

  const dtmfFreqs = {
    '1':[697,1209], '2':[697,1336], '3':[697,1477], '4':[770,1209], '5':[770,1336], '6':[770,1477],
    '7':[852,1209], '8':[852,1336], '9':[852,1477], '*':[941,1209], '0':[941,1336], '#':[941,1477]
  };

  function playDTMF(f1, f2) {
    const osc1 = audioCtx.createOscillator();
    const osc2 = audioCtx.createOscillator();
    const g = audioCtx.createGain();
    
    osc1.type = 'sine'; // 澄んだ高音
    osc2.type = 'sine';
    
    osc1.frequency.value = f1; 
    osc2.frequency.value = f2;
    
    osc1.connect(g); osc2.connect(g); g.connect(mainGain);
    
    const now = audioCtx.currentTime;
    
    // 針の刺さるような鋭いエンベロープ（アタック0.01秒）
    g.gain.setValueAtTime(0, now);
    g.gain.linearRampToValueAtTime(0.2, now + 0.01); // 瞬時に立ち上がる
    g.gain.setValueAtTime(0.2, now + 0.15);          // 短く保持
    g.gain.exponentialRampToValueAtTime(0.001, now + 0.2); // スパッと切る
    
    osc1.start(now); osc2.start(now);
    osc1.stop(now + 0.2); osc2.stop(now + 0.2);
  }

  function playRBT() {
    const osc = audioCtx.createOscillator(); const g = audioCtx.createGain();
    osc.frequency.value = 400; osc.connect(g); g.connect(mainGain);
    let now = audioCtx.currentTime;
    for(let i=0; i<2; i++) {
      g.gain.setValueAtTime(0, now + i*3); g.gain.linearRampToValueAtTime(0.1, now + i*3 + 0.1);
      g.gain.setValueAtTime(0.1, now + i*3 + 1.0); g.gain.linearRampToValueAtTime(0, now + i*3 + 1.1);
    }
    osc.start(now); osc.stop(now + 5);
  }

  // --- UI操作 ---
  var num = ""; var currentFocus = 0;
  var keys = ["1","2","3","4","5","6","7","8","9","*","0","#"];
  window.onload = function() { updateFocus(); };

  function add(n) { audioCtx.resume(); playDTMF(dtmfFreqs[n][0], dtmfFreqs[n][1]); num += n; document.getElementById('display').innerHTML = num; }
  
  function handleWheel(e) {
    if(document.getElementById('callPage').style.display === 'block') return;
    document.getElementById('btn' + keys[currentFocus]).classList.remove('active-num');
    currentFocus = e.deltaY > 0 ? (currentFocus + 1) % keys.length : (currentFocus - 1 + keys.length) % keys.length;
    updateFocus(); e.preventDefault();
  }
  
  function handleMiddleClick(e) { 
    if(e.button === 1 && document.getElementById('callPage').style.display !== 'block') { 
        add(keys[currentFocus]); e.preventDefault(); 
    } 
  }
  
  function updateFocus() { document.getElementById('btn' + keys[currentFocus]).classList.add('active-num'); }

  // --- 近接センサー ---
  if ('ProximitySensor' in window) {
    try {
      const sensor = new ProximitySensor({ frequency: 10 });
      sensor.addEventListener('reading', () => {
        const cp = document.getElementById('callPage');
        if (cp.style.display === 'block') {
          if (sensor.near) { cp.style.opacity = "0"; mainGain.gain.setTargetAtTime(0.1, audioCtx.currentTime, 0.05); }
          else { cp.style.opacity = "1"; mainGain.gain.setTargetAtTime(1.0, audioCtx.currentTime, 0.05); }
        }
      });
      sensor.start();
    } catch (err) {}
  }

  function startCall() {
    if(num == "") return;
    if (window.confirm(num + " へ発信しますか？")) {
      audioCtx.resume();
      document.getElementById('dialPage').style.display = 'none';
      document.getElementById('callPage').style.display = 'block';
      document.getElementById('target').innerHTML = num;
      playRBT();
      setTimeout(function() {
        document.getElementById('timerText').innerHTML = "00:00";
        startTimer();
      }, 5000);
    }
  }

  function startTimer() {
    var s = 0;
    setInterval(function() {
      s++; var min = Math.floor(s/60); var sec = s%60;
      document.getElementById('timerText').innerHTML = (min<10?'0'+min:min)+":"+(sec<10?'0'+sec:sec);
    }, 1000);
  }
</script>
</body>
</html>
