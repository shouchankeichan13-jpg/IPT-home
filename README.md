<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>IPT Terminal</title>
    <style>
        body { background: #050505; color: #00ff41; font-family: 'Consolas', monospace; display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; margin: 0; }
        #monitor { border: 1px solid #00ff41; width: 90%; max-width: 600px; padding: 20px; box-shadow: 0 0 20px rgba(0, 255, 65, 0.2); }
        .glitch { font-size: 1.2em; border-bottom: 1px solid #00ff41; margin-bottom: 20px; padding-bottom: 10px; }
        #log { font-size: 0.9em; height: 150px; overflow-y: auto; text-align: left; margin: 10px 0; }
        .btn { background: transparent; color: #00ff41; border: 1px solid #00ff41; padding: 10px 30px; cursor: pointer; transition: 0.3s; }
        .btn:hover { background: #00ff41; color: #050505; }
    </style>
</head>
<body>
    <div id="monitor">
        <div class="glitch">IPT COMMAND CENTER v13.7</div>
        <div id="status">STATUS: STANDBY...</div>
        <div id="log">> IPT system initialized...<br>> Waiting for stream...</div>
        <button class="btn" onclick="startIPT()">START MONITORING</button>
    </div>

    <script>
        const log = document.getElementById('log');
        const status = document.getElementById('status');
        
        function addLog(msg) {
            log.innerHTML += `<br>> ${msg}`;
            log.scrollTop = log.scrollHeight;
        }

        async function startIPT() {
            status.innerText = "STATUS: CONNECTING...";
            addLog("Initializing 5s buffer...");
            
            // 接続から5秒間は、総帥の望む「楽しい無音」を演出
            setTimeout(() => {
                status.innerText = "STATUS: LIVE (8bit/10kHz)";
                status.style.color = "#ff0000";
                addLog("Stream synchronized.");
                // ここでGitHubのvoice.binをフェッチし、再生を開始する処理を実行
            }, 5000);
        }
    </script>
</body>
</html>
