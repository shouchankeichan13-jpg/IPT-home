<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>IPT電話</title>
<style>
  /* 【平成パート】ダイヤル画面の無骨なスタイル */
  body { margin: 0; font-family: sans-serif; background: #ffffff; }
  .heisei-table { border: 2px solid #000; background: #ccc; }
  .dial-input { width: 70px; height: 50px; font-size: 20px; cursor: pointer; }

  /* 【iPhoneパート】通話中画面の漆黒スタイル */
  #callPage { 
    display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
    background: #000000; color: #ffffff; text-align: center; z-index: 100;
  }
  .iphone-name { font-size: 32px; margin-top: 80px; font-weight: normal; }
  .iphone-timer { font-size: 18px; margin-top: 10px; color: #d1d1d1; }
  
  /* アイコン風ボタン（CSSで丸く） */
  .circle-btn {
    width: 75px; height: 75px; border-radius: 50%; background: rgba(255,255,255,0.15);
    border: none; color: white; font-size: 12px; margin: 10px; cursor: pointer;
  }
  
  /* 終了ボタン（赤） */
  .end-btn-red {
    width: 75px; height: 75px; border-radius: 50%; background: #FF3B30;
    border: none; margin-top: 100px; cursor: pointer;
  }
  .end-btn-red:after { content: "終了"; color: white; font-weight: bold; }
</style>
</head>
<body>

<div id="dialPage">
  <center>
    <br><font size="5"><b>IPT 電話（通信待機中）</b></font><br><br>
    
    <table border="1" width="280" cellpadding="10" bgcolor="#F0F0F0">
      <tr><td align="right"><font size="6" id="display">&nbsp;</font></td></tr>
    </table>
    
    <br>
    <table class="heisei-table" border="1" cellpadding="5">
      <tr>
        <td><input type="button" value=" 1 " class="dial-input" onclick="add('1')"></td>
        <td><input type="button" value=" 2 " class="dial-input" onclick="add('2')"></td>
        <td><input type="button" value=" 3 " class="dial-input" onclick="add('3')"></td>
      </tr>
      <tr>
        <td><input type="button" value=" 4 " class="dial-input" onclick="add('4')"></td>
        <td><input type="button" value=" 5 " class="dial-input" onclick="add('5')"></td>
        <td><input type="button" value=" 6 " class="dial-input" onclick="add('6')"></td>
      </tr>
      <tr>
        <td><input type="button" value=" 7 " class="dial-input" onclick="add('7')"></td>
        <td><input type="button" value=" 8 " class="dial-input" onclick="add('8')"></td>
        <td><input type="button" value=" 9 " class="dial-input" onclick="add('9')"></td>
      </tr>
      <tr>
        <td><input type="button" value=" * " class="dial-input" onclick="add('*')"></td>
        <td><input type="button" value=" 0 " class="dial-input" onclick="add('0')"></td>
        <td><input type="button" value=" # " class="dial-input" onclick="add('#')"></td>
      </tr>
    </table>
    <br>
    <input type="button" value="電話をかける" onclick="startCall()" style="width:250px; height:60px; font-size:22px; cursor:pointer;">
    <br><br>
    <input type="button" value="消去" onclick="cls()" style="width:80px; height:30px;">
  </center>
</div>

<div id="callPage">
  <div class="iphone-name" id="target">発信中...</div>
  <div class="iphone-timer" id="timerText">接続中...</div>

  <center>
    <div style="width: 300px; margin-top: 50px;">
      <button class="circle-btn">消音</button>
      <button class="circle-btn">キーパッド</button>
      <button class="circle-btn">スピーカー</button>
      <button class="circle-btn">通話追加</button>
      <button class="circle-btn">FaceTime</button>
      <button class="circle-btn">連絡先</button>
    </div>

    <button class="end-btn-red" onclick="location.reload()"></button>
  </center>
</div>

<script>
  var num = "";
  function add(n) { num += n; document.getElementById('display').innerHTML = num; }
  function cls() { num = ""; document.getElementById('display').innerHTML = "&nbsp;"; }

  function startCall() {
    if(num == "") return;
    // 平成の無骨なダイアログ
    if (window.confirm(num + " へ発信しますか？")) {
      document.getElementById('dialPage').style.display = 'none';
      document.getElementById('callPage').style.display = 'block';
      document.getElementById('target').innerHTML = num;
      
      // 5秒の静寂プロトコル
      setTimeout(function() {
        document.getElementById('timerText').innerHTML = "00:00";
        startTimer();
        // 🛡️ ここにGitHubからの音声再生ロジックが入ります
      }, 5000);
    }
  }

  function startTimer() {
    var s = 0;
    setInterval(function() {
      s++;
      var min = Math.floor(s / 60);
      var sec = s % 60;
      document.getElementById('timerText').innerHTML = 
        (min < 10 ? "0" + min : min) + ":" + (sec < 10 ? "0" + sec : sec);
    }, 1000);
  }
</script>

</body>
</html>
