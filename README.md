<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IPT Monitoring</title>
    <style>
        :root {
            --samsung-bg: #f4f7fa;
            --card-bg: #ffffff;
            --one-ui-blue: #007aff;
            --text-title: #000000;
            --text-body: #3c4043;
            --text-sub: #86868b;
        }

        body {
            background-color: var(--samsung-bg);
            color: var(--text-title);
            /* Samsung Oneフォントを最優先 */
            font-family: "Samsung One", "Samsung Sans", "Segoe UI", "Roboto", "Apple SD Gothic Neo", sans-serif;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            -webkit-font-smoothing: antialiased;
        }

        .container {
            width: 90%;
            max-width: 400px;
            background: var(--card-bg);
            border-radius: 40px; /* One UI特有の深い角丸 */
            padding: 48px 24px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.04);
            text-align: center;
            transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .header {
            margin-bottom: 40px;
        }

        .header h1 {
            font-size: 28px;
            font-weight: 700;
            margin: 0;
            letter-spacing: -0.5px;
        }

        .status-container {
            margin-bottom: 40px;
        }

        .badge {
            display: inline-flex;
            align-items: center;
            padding: 8px 20px;
            border-radius: 20px;
            background: #f1f3f4;
            color: var(--text-sub);
            font-size: 15px;
            font-weight: 500;
            transition: all 0.3s;
        }

        .badge.active {
            background: rgba(0, 122, 255, 0.1);
            color: var(--one-ui-blue);
        }

        /* 波形ビジュアライザー */
        .visualizer {
            height: 100px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 4px;
            margin-bottom: 40px;
        }

        .bar {
            width: 4px;
            height: 8px;
            background: #e0e0e0;
            border-radius: 2px;
            transition: height 0.2s ease;
        }

        .btn-action {
            background: var(--one-ui-blue);
            color: white;
            border: none;
            width: 100%;
            padding: 20px;
            border-radius: 30px;
            font-size: 18px;
            font-weight: 600;
            cursor: pointer;
            box-shadow: 0 8px 20px rgba(0, 122, 255, 0.2);
            transition: transform 0.2s, background 0.2s;
        }

        .btn-action:active {
            transform: scale(0.96);
            background: #0062cc;
        }

        .footer-log {
            margin-top: 30px;
            font-size: 14px;
            color: var(--text-sub);
            min-height: 20px;
        }
    </style>
</head>
<body>

<div class="container">
    <div class="header">
        <h1>IPT Monitoring</h1>
    </div>

    <div class="status-container">
        <div id="status-badge" class="badge">Standby</div>
    </div>

    <div class="visualizer" id="viz">
        <div class="bar"></div><div class="bar"></div><div class="bar"></div><div class="bar"></div><div class="bar"></div>
    </div>

    <button id="main-btn" class="btn-action" onclick="initIPT()">Activate</button>

    <div id="log" class="footer-log">Ready to synchronize</div>
</div>

<script>
    let isLive = false;

    function initIPT() {
        const btn = document.getElementById('main-btn');
        const badge = document.getElementById('status-badge');
        const log = document.getElementById('log');

        btn.style.display = 'none';
        badge.innerText = 'Connecting...';
        badge.classList.add('active');
        log.innerText = 'Initializing 5s buffer...';

        // 5秒の「楽しい無音」演出
        setTimeout(() => {
            isLive = true;
            badge.innerText = 'LIVE';
            badge.style.color = '#ff3b30'; // ライブ時は赤に
            log.innerText = 'Synchronized with IPT System';
            startVisualizer();
            // ※ここにGitHubからvoice.binをフェッチするコードを繋げます
        }, 5000);
    }

    function startVisualizer() {
        const bars = document.querySelectorAll('.bar');
        setInterval(() => {
            if(!isLive) return;
            bars.forEach(bar => {
                const h = Math.random() * 60 + 10;
                bar.style.height = h + 'px';
                bar.style.background = '#007aff';
            });
        }, 150);
    }
</script>

</body>
</html>
