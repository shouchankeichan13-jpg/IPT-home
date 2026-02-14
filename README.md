<div id="callPage">
  <div class="call-container">
    <div class="iphone-name" id="target">発信中...</div>
    <div class="iphone-timer" id="timerText">接続中...</div>
    
    <div class="icon-grid">
      <div class="icon-box">
        <button id="muteBtn" class="circle-btn" onclick="toggleMute()">消音</button>
        <span class="icon-label">消音</span>
      </div>
      <div class="icon-box">
        <button id="keyBtn" class="circle-btn" onclick="showKeypad()">キーパッド</button>
        <span class="icon-label">キーパッド</span>
      </div>
      <div class="icon-box">
        <button id="spBtn" class="circle-btn" onclick="toggleSpeaker()">スピーカー</button>
        <span class="icon-label">スピーカー</span>
      </div>
    </div>

    <button class="end-btn-red" onclick="endCall()">終了</button>
  </div>
</div>

<script>
  // --- 実機能ロジック ---
  let isMuted = false;
  let isSpeaker = false;
  let callInterval = null;

  // 消音機能
  function toggleMute() {
    isMuted = !isMuted;
    const btn = document.getElementById('muteBtn');
    btn.style.background = isMuted ? "rgba(255,255,255,0.8)" : "rgba(255,255,255,0.15)";
    btn.style.color = isMuted ? "#000" : "#fff";
    // 実際のマイク制御が必要な場合はここに実装
  }

  // キーパッド表示（ダイヤル画面へ戻る擬似動作）
  function showKeypad() {
    // 通話中に番号入力が必要な場合、DTMF音を鳴らす準備
    alert("キーパッド：番号入力モード待機中");
  }

  // スピーカー機能
  function toggleSpeaker() {
    isSpeaker = !isSpeaker;
    const btn = document.getElementById('spBtn');
    btn.style.background = isSpeaker ? "rgba(255,255,255,0.8)" : "rgba(255,255,255,0.15)";
    btn.style.color = isSpeaker ? "#000" : "#fff";
    // スピーカーON時は音量をブースト
    mainGain.gain.setTargetAtTime(isSpeaker ? 2.0 : 1.0, audioCtx.currentTime, 0.1);
  }

  // 通話終了
  function endCall() {
    // 全ての音を強制停止
    audioCtx.suspend();
    clearInterval(callInterval);
    // 最初の画面にリセット
    location.reload(); 
  }

  // startTimerを修正
  function startTimer() {
    let s = 0;
    callInterval = setInterval(() => {
      s++;
      let m = Math.floor(s/60);
      let sc = s%60;
      document.getElementById('timerText').innerHTML = (m<10?'0'+m:m)+":"+(sc<10?'0'+sc:sc);
    }, 1000);
  }
</script>
