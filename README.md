<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>IPT電話</title>
<style>
  body { margin: 0; font-family: sans-serif; background: #ffffff; overflow-x: hidden; }
  .heisei-table { border: 2px solid #000; background: #ccc; }
  .dial-input { width: 75px; height: 55px; font-size: 22px; cursor: pointer; border: 2px solid #888; }
  .active-num { background: #ffff00 !important; border: 3px solid #ff0000 !important; transform: scale(1.05); }

  #callPage { 
    display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
    background: #000000; color: #ffffff; text-align: center; z-index: 100;
  }
  .iphone-name { font-size: 32px; margin-top: 80px; }
  .iphone-timer { font-size: 18px; margin-top: 10px; color: #d1d1d1; }
  .circle-btn { width: 75px; height: 75px; border-radius: 50%; background: rgba(255,255,255,0.15); border: none; color: white; margin: 10px; }
  .end-btn-red { width: 75px; height: 75px; border-radius: 50%; background: #FF3B30; border: none; margin-top: 100px; cursor: pointer; }
  .end-btn-red:after { content: "終了"; color: white; font-weight: bold; }
</style>
</head>
<body onwheel="handleWheel(event)" onmousedown="handleMiddleClick(event)">

<div id="dialPage">
  <center>
    <br><font size="5"><b>IPT 電話（音響プロトコル実装）</b></font><br><br>
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
    <input type="button" value="電話をかける" onclick="startCall()" style="width:250px; height:60px; font-size:22px; cursor:pointer; background:#ccffcc;">
  </center>
</div>

<div id="callPage">
  <div class="iphone-name" id="target">発信中...</div>
  <div class="iphone-timer" id="timerText">接続中...</div>
  <center>
    <div style="width: 300px; margin-top: 50px;">
      <button class="circle-btn">消音</button><button class="circle-btn">キーパッド</button><button class="circle-btn">スピーカー</button>
    </div>
    <button class="end-btn-red" onclick="location.reload()"></button>
  </center>
</div>

<script>
  // --- 音響エンジン (Web Audio API) ---
  const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  
  function playDTMF(f1, f2) {
    const osc1 = audioCtx.createOscillator();
    const osc2 = audioCtx.createOscillator();
    const gain = audioCtx.createGain();
    osc1.frequency.value = f1; osc2.frequency.value = f2;
    osc1.connect(gain); osc2.connect(gain); gain.connect(audioCtx.destination);
    gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
    osc1.start(); osc2.start();
    gain.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.2);
    osc1.stop(audioCtx.currentTime + 0.2); osc2.stop(audioCtx.currentTime + 0.2);
  }

  const dtmfFreqs = {
    '1':[697,1209], '2':[697,1336], '3':[697,1477],
    '4':[770,1209], '5':[770,1336], '6':[770,1477],
    '7':[852,1209], '8':[852,1336], '9':[852,1477],
    '*':[941,1209], '0':[941,1336], '#':[941,1477]
  };

  // --- UI操作 ---
  var num = "";
  var currentFocus = 0;
  var keys = ["1","2","3","4","5","6","7","8","9","*","0","#"];

  function add(n) {
    audioCtx.resume();
    playDTMF(dtmfFreqs[n][0], dtmfFreqs[n][1]);
    num += n;
    document.getElementById('display').innerHTML = num;
  }

  function handleWheel(e) {
    if(document.getElementById('callPage').style.display === 'block') return;
    document.getElementById('btn' + keys[currentFocus]).classList.remove('active-num');
    currentFocus = e.deltaY > 0 ? (currentFocus + 1) % keys.length : (currentFocus - 1 + keys.length) % keys.length;
    document.getElementById('btn' + keys[currentFocus]).classList.add('active-num');
    e.preventDefault();
  }

  function handleMiddleClick(e) {
    if(e.button === 1) { add(keys[currentFocus]); e.preventDefault(); }
  }

  // --- 呼出音 (RBT) ---
  function playRBT() {
    const osc = audioCtx.createOscillator();
    const gain = audioCtx.createGain();
    osc.frequency.value = 400; // 日本の呼出音に近い周波数
    osc.connect(gain); gain.connect(audioCtx.destination);
    
    // 1秒鳴って2秒休むリズム
    let now = audioCtx.currentTime;
    for(let i=0; i<2; i++) {
      gain.gain.setValueAtTime(0, now + i*3);
      gain.gain.linearRampToValueAtTime(0.1, now + i*3 + 0.1);
      gain.gain.setValueAtTime(0.1, now + i*3 + 1);
      gain.gain.linearRampToValueAtTime(0, now + i*3 + 1.1);
    }
    osc.start();
    osc.stop(now + 5);
  }

  function startCall() {
    if(num == "") return;
    if (window.confirm(num + " へ発信しますか？")) {
      audioCtx.resume();
      document.getElementById('dialPage').style.display = 'none';
      document.getElementById('callPage').style.display = 'block';
      document.getElementById('target').innerHTML = num;
      
      playRBT(); // プルルル音開始

      setTimeout(function() {
        document.getElementById('timerText').innerHTML = "00:00";
        // ここでタイマー開始 & GitHub音声再生
      }, 5000);
    }
  }
</script>
</body>
</html>
