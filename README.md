<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>IPT ツウシン モニター</title>
</head>
<body bgcolor="#FFFFFF" text="#000000">

<center>
  <br>
  <h1>IPT モニタリング センター</h1>
  <hr width="80%">
  
  <p><b>ステータス：</b> <font color="#FF0000" id="status">テイシチュウ</font></p>

  <table border="1" cellpadding="10">
    <tr>
      <td>
        ココをクリックするとセツゾクします。<br>
        セツゾクゴ、5ビョウカンのムオン（セイジャク）ガ アリマス。
      </td>
    </tr>
  </table>

  <br>

  <form>
    <input type="button" value="セツゾク カイシ" onclick="initIPT()" style="width:200px; height:50px;">
  </form>

  <br>
  <hr width="80%">

  <div id="log" align="left" style="width:80%; font-size: 12px;">
    > システム ジュンビ カンリョウ<br>
    > シレイ ヲ マッテイマス...
  </div>

</center>

<script>
  function initIPT() {
    var status = document.getElementById('status');
    var log = document.getElementById('log');

    status.innerHTML = "セツゾクチュウ...";
    log.innerHTML += "<br>> 5ビョウカンのバッファ ヲ セイセイチュウ...";

    // 平成の静寂：5秒のタイムラグ演出
    setTimeout(function() {
      status.innerHTML = "ライブ ハイスンチュウ！！";
      status.setAttribute("color", "#0000FF"); // 青色に変更
      log.innerHTML += "<br>> GitHubト ドウキ シマシタ。オンセイ ヲ サイセイ シマス。";
      
      // ココにGitHubからvoice.binをフェッチして再生する生JSを記述
    }, 5000);
  }
</script>

</body>
</html>
