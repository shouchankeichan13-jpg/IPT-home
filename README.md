<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>IPT SYSTEM - RECEIVER</title>
    <style>
        :root { --ios-red: #FF3B30; --ios-green: #4CD964; }
        body { margin: 0; background: #000; color: #fff; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; overflow: hidden; height: 100vh; display: flex; align-items: center; justify-content: center; }

        /* 待機画面 */
        #standby { text-align: center; transition: opacity 0.5s; }
        .logo { font-size: 24px; letter-spacing: 5px; color: #555; margin-bottom: 10px; }
        .spinner { width: 30px; height: 30px; border: 3px solid rgba(255,255,255,0.1); border-top-color: #fff; border-radius: 50%; animation: spin 1s linear infinite; margin: 0 auto; }
        @keyframes spin { to { transform: rotate(360deg); } }

        /* 着信画面 (iPhone風) */
        #callUI { display: none; position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: linear-gradient(180deg, #2c3e50 0%, #000000 100%); flex-direction: column; align-items: center; justify-content: space-between; padding: 80px 0; box-sizing: border-box; }
        .caller-name { font-size: 32px; font-weight: 300; }
        .call-status { font-size: 18px; color: #aaa; margin-top: 10px; }
        .button-container { display: flex; gap: 60px; margin-bottom: 40px; }
        .btn { width: 75px; height: 75px; border-radius: 50%; border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; color: white; font-weight: bold; }
        .decline { background: var(--ios-red); }
        .accept { background: var(--ios-green); animation: pulse 2s infinite; }
        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(76, 217, 100, 0.4); } 70% { box-shadow: 0 0 0 20px rgba(76, 217, 100, 0); } 100% { box-shadow: 0 0 0 0 rgba(76, 217, 100, 0); } }
    </style>
</head>
<body>

    <div id="standby">
        <div class="logo">IPT SYSTEM</div>
        <div id="waitMsg" style="margin-bottom: 20px; color: #888;">小番号を入力してください...</div>
        <input type="text" id="myNumber" placeholder="自分の番号(4桁)" maxlength="4" style="background: #222; border: 1px solid #444; color: #fff; text-align: center; padding: 10px; border-radius: 5px; font-size: 20px; width: 150px;">
        <button onclick="startMonitoring()" style="margin-top: 20px; display: block; width: 100%; background: none; border: 1px solid #555; color: #555; padding: 5px; cursor: pointer;">待機開始</button>
        <div class="spinner" id="monIcon" style="display:none; margin-top: 20px;"></div>
    </div>

    <div id="callUI">
        <div style="text-align: center;">
            <div class="caller-name">総帥 (帝国直通)</div>
            <div class="call-status" id="statText">IPT通信 着信中...</div>
        </div>
        
        <div class="button-container">
            <div style="text-align:center;"><button class="btn decline" onclick="location.reload()">拒否</button><div style="margin-top:10px; font-size:12px;">拒否</div></div>
            <div style="text-align:center;"><button class="btn accept" id="acceptBtn" onclick="connected()">応答</button><div style="margin-top:10px; font-size:12px;">応答</div></div>
        </div>
    </div>

    <script>
        let myNum = "";
        let lastETag = "";

        function startMonitoring() {
            myNum = document.getElementById('myNumber').value;
            if (myNum.length !== 4) { alert("4桁の小番号を入力してください"); return; }
            
            document.getElementById('waitMsg').innerText = myNum + "番で待機中...";
            document.getElementById('myNumber').style.display = "none";
            document.querySelector('button').style.display = "none";
            document.getElementById('monIcon').style.display = "block";

            // 監視ループ開始
            setInterval(checkUpdate, 3000);
        }

        async function checkUpdate() {
            // 総帥のリポジトリの、自分の小番号.binを監視
            const url = `https://raw.githubusercontent.com/shouchankeichan13-jpg/IPT/main/${myNum}.bin`;
            
            try {
                const res = await fetch(url, { method: 'HEAD', cache: 'no-store' });
                if (res.ok) {
                    const etag = res.headers.get('ETag');
                    if (lastETag && lastETag !== etag) {
                        triggerCall();
                    }
                    lastETag = etag;
                }
            } catch (e) { console.log("待機中..."); }
        }

        function triggerCall() {
            document.getElementById('standby').style.display = 'none';
            document.getElementById('callUI').style.display = 'flex';
            // 着信音の代わりにバイブレーション（モバイルのみ）
            if (navigator.vibrate) navigator.vibrate([500, 200, 500, 200]);
        }

        function connected() {
            document.getElementById('statText').innerText = "00:01";
            document.getElementById('statText').style.color = "#4CD964";
            document.getElementById('acceptBtn').style.display = "none";
        }
    </script>
</body>
</html>
