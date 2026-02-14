<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>IPT 電話</title>
</head>
<body bgcolor="#FFFFFF" text="#000000">

<center>
  <br>
  <font size="6"><b>IPT 電話</b></font>
  <br><br>

  <table border="1" width="250" cellpadding="10">
    <tr>
      <td align="center" bgcolor="#F0F0F0">
        <font size="6" id="display">&nbsp;</font>
      </td>
    </tr>
  </table>

  <br>

  <table border="0" cellpadding="5">
    <tr>
      <td><input type="button" value="  1  " onclick="addNum('1')" style="width:70px; height:50px; font-size:20px;"></td>
      <td><input type="button" value="  2  " onclick="addNum('2')" style="width:70px; height:50px; font-size:20px;"></td>
      <td><input type="button" value="  3  " onclick="addNum('3')" style="width:70px; height:50px; font-size:20px;"></td>
    </tr>
    <tr>
      <td><input type="button" value="  4  " onclick="addNum('4')" style="width:70px; height:50px; font-size:20px;"></td>
      <td><input type="button" value="  5  " onclick="addNum('5')" style="width:70px; height:50px; font-size:20px;"></td>
      <td><input type="button" value="  6  " onclick="addNum('6')" style="width:70px; height:50px; font-size:20px;"></td>
    </tr>
    <tr>
      <td><input type="button" value="  7  " onclick="addNum('7')" style="width:70px; height:50px; font-size:20px;"></td>
      <td><input type="button" value="  8  " onclick="addNum('8')" style="width:70px; height:50px; font-size:20px;"></td>
      <td><input type="button" value="  9  " onclick="addNum('9')" style="width:70px; height:50px; font-size:20px;"></td>
    </tr>
    <tr>
      <td><input type="button" value="  * " onclick="addNum('*')" style="width:70px; height:50px; font-size:20px;"></td>
      <td><input type="button" value="  0  " onclick="addNum('0')" style="width:70px; height:50px; font-size:20px;"></td>
      <td><input type="button" value="  #  " onclick="addNum('#')" style="width:70px; height:50px; font-size:20px;"></td>
    </tr>
  </table>

  <br>

  <input type="button" value="電話をかける" onclick="startCall()" style="width:230px; height:60px; font-size:20px; background-color:#CCFFCC;">
  
  <br><br>
  <input type="button" value="番号を消去" onclick="clearNum()" style="width:100px; height:30px;">

  <br><br>
  <hr width="90%">
  <p id="status">【待機中】</p>

</center>

<script>
  var dialNum = "";

  function addNum(n) {
    dialNum += n;
    document.getElementById('display').innerHTML = dialNum;
  }

  function clearNum() {
    dialNum = "";
    document.getElementById('display').innerHTML = "&nbsp;";
  }

  function startCall() {
    // 標準の確認ダイアログ
    var result = window.confirm(dialNum + " に発信しますか？");
    
    if (result) {
      connectSystem();
    }
  }

  function connectSystem() {
    var status = document.getElementById('status');
    status.innerHTML = "接続しています。5秒ほどお待ちください...";

    // 5秒の静寂
    setTimeout(function() {
      status.innerHTML = "<font color='#0000FF'><b>通話中</b></font>";
      // ここでGitHubのvoice.binを読み込んで再生を開始
    }, 5000);
  }
</script>

</body>
</html>
