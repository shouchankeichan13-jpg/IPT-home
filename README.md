// --- IPT V14.0 通信中継局スクリプト ---
const GITHUB_TOKEN = "あなたの個人アクセストークン"; // GitHubで発行
const REPO = "ユーザー名/リポジトリ名";
const PATH = "voice.bin"; // 8bit音声ファイル名

function doPost(e) {
  // ESP32から届いた8bitバイナリ
  const bytes = e.postData.contents;
  const base64Data = Utilities.base64Encode(bytes);
  
  const url = `https://api.github.com/repos/${REPO}/contents/${PATH}`;
  
  // 1. 現在のファイルのSHAを取得（上書きに必須）
  const getRes = UrlFetchApp.fetch(url, {
    headers: { "Authorization": "token " + GITHUB_TOKEN },
    muteHttpExceptions: true
  });
  
  let sha = "";
  if (getRes.getResponseCode() == 200) {
    sha = JSON.parse(getRes.getContentText()).sha;
  }

  // 2. GitHubへ射出
  const payload = {
    message: "IPT Voice Stream Update",
    content: base64Data,
    sha: sha
  };

  const options = {
    method: "put",
    headers: { "Authorization": "token " + GITHUB_TOKEN },
    contentType: "application/json",
    payload: JSON.stringify(payload)
  };

  UrlFetchApp.fetch(url, options);
  return ContentService.createTextOutput("IPT_SUCCESS");
}
