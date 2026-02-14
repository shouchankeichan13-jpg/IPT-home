<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>IPT電話</title>
<style>
  /* 全体の基本設定 */
  body { margin: 0; font-family: -apple-system, BlinkMacSystemFont, "Helvetica Neue", sans-serif; background: #fff; }
  
  /* ダイヤル画面（シンプル） */
  #dialPage { padding-top: 50px; text-align: center; }
  .dial-btn { width: 75px; height: 75px; font-size: 24px; margin: 5px; border-radius: 50%; border: 1px solid #ccc; background: #f0f0f0; cursor: pointer; }
  .call-btn { width: 250px; height: 60px; font-size: 20px; background: #4CD964; color: white; border: none; border-radius: 30px; margin-top: 20px; cursor: pointer; }

  /* 通話中画面（iPhone風） */
  #callPage { 
    display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
    background: radial-gradient(circle at center, #333 0%, #000 100%); color: white; text-align: center;
  }
  .calling-name { font-size: 32px; margin-top: 80px; font-weight: 300; }
  .timer { font-size: 18px; margin-top: 10px; color: #aaa; }
  
  /* アイコン風グリッド */
  .grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; width: 80%; margin: 60px auto; }
  .icon-btn { 
    width: 70px; height: 70px; border-radius: 50%; background: rgba(255,255,255,0.1); 
    border: none; color: white; margin: 0 auto; display: flex; align-items: center; justify-content: center; font-size: 12px;
  }
  
  /* 終了ボタン（赤） */
  .end-btn { 
    width: 75px; height: 75px; border-radius: 50%; background: #FF3B30; border: none; 
    position: absolute; bottom: 80px; left: 50%; transform: translateX(-50%); cursor: pointer;
  }
</style>
</head>
<body>

<div id="dialPage">
  <div style="font-size: 40px; height: 60px; margin-bottom: 20px;" id="display"></div>
  <div id="pad">
    <button class="dial-btn" onclick="add('1')">1</button>
    <button class="dial-btn" onclick="add('2')">2</button>
    <button class="dial-btn" onclick="add('3')">3</button><br>
    <button class="dial-btn" onclick="add('4')">4</button>
    <button class="dial-btn" onclick="add('5')">5</button>
    <button class="dial-btn" onclick="add('6')">6</button><br>
    <button class="dial-btn" onclick="add('7')">7</button>
    <button class="dial-btn" onclick="add('8')">8</button>
    <button class="dial-btn" onclick="add('9')">9</button><br>
    <button class="dial-btn" onclick="add('*')">*</button>
    <button class="dial-btn" onclick="add('0')">0</button>
    <button class="dial-btn" onclick="add('#')">#</button><br>
    <button class="call-btn" onclick="startCall()">電話をかける</button>
  </div>
</div>

<div id="callPage">
  <div class="calling-name" id="target">発信中...</div>
  <div class="timer" id="timer">接続中...</div>

  <div class="grid">
    <div class="icon-btn">消音</div>
    <div class="icon-btn">キーパッド</div>
    <div class="icon-btn">スピーカー</div>
    <div class="icon-btn">通話追加</div>
    <div class="icon-btn">FaceTime</div>
    <div class="icon-btn">連絡先</div>
  </div>

  <button class="end-btn" onclick="location.reload()"></button>
</div>

<script>
  let num = "";
  function add(n) { num += n; document.getElementById('display').innerText = num; }

  function startCall() {
    if(!num) return;
    if(window.confirm(num + " に発信しますか？")) {
      document.getElementById('dialPage').style.display = 'none';
      document.getElementById('callPage').style.display = 'block';
      document.getElementById('target').innerText = num;
      
      // 5秒の「楽しい無音」のあと、タイマー開始
      setTimeout(() => {
        let sec = 0;
        setInterval(() => {
          sec++;
          let m = Math.floor(sec/60);
          let s = sec%60;
          document.getElementById('timer').innerText = (m<10?'0'+m:m)+":"+(s<10?'0'+s:s);
        }, 1000);
        // ここにGitHub再生処理を挿入
      }, 5000);
    }
  }
</script>

</body>
</html>
