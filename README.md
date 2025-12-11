<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>臨床推論トレーナー（医師国家試験モード）</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <!-- iPhoneでホーム画面に置きやすくする用 -->
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="default">
  <meta name="apple-mobile-web-app-title" content="臨床推論トレーナー">

  <style>
    * {
      box-sizing: border-box;
    }
    :root {
      --bg-main: #0f172a;
      --bg-surface: #f9fafb;
      --accent: #0ea5e9;
      --accent-dark: #0284c7;
      --text-main: #0f172a;
      --text-sub: #6b7280;
      --bubble-doctor: #0ea5e9;
      --bubble-patient: #e5e7eb;
    }
    body {
      margin: 0;
      font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text",
                   "Hiragino Sans", "Yu Gothic", sans-serif;
      background: radial-gradient(circle at top, #1f2933 0, #020617 55%);
      color: var(--text-main);
      display: flex;
      justify-content: center;
      align-items: stretch;
      min-height: 100vh;
    }
    .app {
      display: flex;
      flex-direction: column;
      width: 100%;
      max-width: 720px;
      height: 100vh;
      background: var(--bg-surface);
      box-shadow: 0 20px 40px rgba(15, 23, 42, 0.35);
    }

    /* ヘッダー（アプリタイトルバー風） */
    header {
      padding: 12px 18px 10px;
      border-bottom: 1px solid rgba(148, 163, 184, 0.35);
      background: linear-gradient(135deg, #0f172a, #0b5ed7);
      color: #f9fafb;
    }
    header .title-row {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 8px;
    }
    header .title {
      font-weight: 650;
      font-size: 18px;
      letter-spacing: 0.02em;
    }
    header .badge {
      padding: 2px 10px;
      border-radius: 999px;
      font-size: 10px;
      border: 1px solid rgba(148, 163, 184, 0.7);
      background: rgba(15, 23, 42, 0.6);
    }
    header .subtitle {
      margin-top: 6px;
      font-size: 11px;
      color: rgba(226, 232, 240, 0.9);
    }

    /* 症例選択エリア（カード風） */
    .case-wrapper {
      padding: 10px 14px 6px;
      background: #e5e7eb;
    }
    .case-card {
      background: #f9fafb;
      border-radius: 14px;
      border: 1px solid #e5e7eb;
      padding: 8px 10px 10px;
      box-shadow: 0 4px 10px rgba(15, 23, 42, 0.04);
    }
    .case-selector {
      display: flex;
      gap: 8px;
      margin-bottom: 4px;
    }
    .case-selector input {
      flex: 1;
      padding: 7px 10px;
      border-radius: 999px;
      border: 1px solid #d1d5db;
      font-size: 13px;
      background: #f3f4f6;
    }
    .case-selector input:focus {
      outline: none;
      border-color: var(--accent);
      background: #ffffff;
      box-shadow: 0 0 0 1px rgba(14, 165, 233, 0.2);
    }
    .case-selector button {
      padding: 6px 12px;
      border-radius: 999px;
      border: none;
      font-size: 13px;
      font-weight: 600;
      background: var(--accent);
      color: white;
      white-space: nowrap;
    }
    .case-selector button:active {
      background: var(--accent-dark);
    }
    .case-status {
      font-size: 11px;
      color: var(--text-sub);
      line-height: 1.4;
    }

    /* チャットエリア */
    main {
      flex: 1;
      overflow-y: auto;
      padding: 10px 10px 0;
      background: #edf2f7;
      scroll-behavior: smooth;
    }
    .system-message {
      text-align: center;
      font-size: 11px;
      color: #9ca3af;
      margin: 4px 0 10px;
    }
    .message-row {
      display: flex;
      margin-bottom: 10px;
    }
    .message-row.doctor {
      justify-content: flex-end;
    }
    .message-row.patient {
      justify-content: flex-start;
    }
    .bubble {
      max-width: 80%;
      padding: 9px 11px;
      border-radius: 18px;
      font-size: 14px;
      line-height: 1.4;
      word-wrap: break-word;
      white-space: pre-wrap;
      position: relative;
    }
    .doctor .bubble {
      background: var(--bubble-doctor);
      color: #ffffff;
      border-bottom-right-radius: 4px;
    }
    .patient .bubble {
      background: var(--bubble-patient);
      color: #111827;
      border-bottom-left-radius: 4px;
    }

    /* 入力フォーム（下に浮いた感じ） */
    form {
      padding: 6px 10px calc(env(safe-area-inset-bottom, 0) + 6px);
      background: transparent;
    }
    .input-shell {
      background: #ffffff;
      border-radius: 999px;
      border: 1px solid #d1d5db;
      padding: 4px 4px 4px 12px;
      display: flex;
      align-items: center;
      gap: 6px;
      box-shadow: 0 10px 30px rgba(15, 23, 42, 0.18);
    }
    form input {
      flex: 1;
      padding: 6px 4px;
      border-radius: 999px;
      border: none;
      font-size: 14px;
      background: transparent;
    }
    form input:focus {
      outline: none;
    }
    form button {
      border-radius: 999px;
      border: none;
      padding: 7px 14px;
      font-size: 14px;
      font-weight: 600;
      background: var(--accent);
      color: white;
    }
    form button:active {
      background: var(--accent-dark);
    }

    @media (min-width: 768px) {
      header {
        padding-inline: 24px;
      }
      .case-wrapper {
        padding-inline: 24px;
      }
      main {
        padding-inline: 18px;
      }
      form {
        padding-inline: 18px;
      }
    }
  </style>
</head>
<body>
  <div class="app">
    <header>
      <div class="title-row">
        <div class="title">臨床推論トレーナー</div>
        <div class="badge">Clinical Reasoning Lab</div>
      </div>
      <div class="subtitle">あなたが医師役。医師国家試験の症例が、患者さんとして答えてくれる。</div>
    </header>

    <div class="case-wrapper">
      <div class="case-card">
        <div class="case-selector">
          <input id="caseIdInput" placeholder="例: 117A-5 など（症例ID）">
          <button id="loadCaseBtn">症例セット</button>
        </div>
        <div id="caseStatus" class="case-status">
          症例IDを入力して「症例セット」を押すと、その症例の患者さんとして話し始めます。
        </div>
      </div>
    </div>

    <main id="chatContainer"></main>

    <form id="messageForm">
      <div class="input-shell">
        <input id="messageInput" placeholder="医師として質問を書いてください（例: 本日はどうされましたか？）">
        <button type="submit">送信</button>
      </div>
    </form>
  </div>

  <script>
    // ---- 症例データ ----
    const cases = {
      "sample-1": {
        title: "サンプル症例：上気道炎",
        note: "国試とは関係ないデモ症例です。",
        backdrop: "30歳男性。2日前からの発熱と咽頭痛を主訴に受診した患者さんを想定しています。",
        cc: "2日前からの発熱と、喉の痛みがつらくて来ました。",
        hpi: "2日前の夕方から38℃台の発熱と咽頭痛が出ました。咳は少しで、痰はあまり出ません。市販の解熱鎮痛薬を飲むと一時的に楽になりますが、また熱が上がってきます。呼吸が苦しい感じはありません。",
        pmh: "これまで大きな病気はありません。入院歴も手術歴もありません。",
        meds: "今は市販の解熱鎮痛薬（アセトアミノフェン配合）だけ飲んでいます。",
        allergies: "薬や食べ物のアレルギーは特に言われたことはありません。",
        family: "家族に特別な遺伝性の病気はありません。",
        social: "会社員で事務の仕事をしています。喫煙歴はなく、お酒は週に1〜2回ビールを1杯程度です。周囲で同じような風邪症状の人が何人かいます。",
        ros: "鼻水が少し出ますが、胸の痛みや強い呼吸苦、腹痛、下痢などはありません。",
        pe: "体温38.2℃、脈拍96/分、血圧120/70mmHg、呼吸数16/分、SpO2 98％（室内気）。咽頭発赤あり、扁桃に白苔ははっきりしません。頸部リンパ節が軽度腫大しています。胸部聴診で明らかなラ音は聴取しません。",
        labs_basic: "CRP軽度上昇、白血球数はやや増加していますが、著明な異常はありません。",
        labs_advanced: "迅速インフルエンザ抗原検査は陰性でした。",
        imaging: "胸部X線写真では明らかな浸潤影は認めません。",
        diagnosis: "かぜ症候群（上気道炎）",
        fallback: "すみません、その質問についてはこのサンプル症例では設定していません。別の聞き方で質問してみてください。"
      }
      // ここに "117A-5": { ... } などを今後追加していく
    };

    let currentCase = null;
    const chatContainer = document.getElementById("chatContainer");
    const caseIdInput = document.getElementById("caseIdInput");
    const loadCaseBtn = document.getElementById("loadCaseBtn");
    const messageForm = document.getElementById("messageForm");
    const messageInput = document.getElementById("messageInput");
    const caseStatus = document.getElementById("caseStatus");

    function addSystemMessage(text) {
      const div = document.createElement("div");
      div.className = "system-message";
      div.textContent = text;
      chatContainer.appendChild(div);
      chatContainer.scrollTop = chatContainer.scrollHeight;
    }

    function addChatMessage(text, sender) {
      const row = document.createElement("div");
      row.className = "message-row " + sender;
      const bubble = document.createElement("div");
      bubble.className = "bubble";
      bubble.textContent = text;
      row.appendChild(bubble);
      chatContainer.appendChild(row);
      chatContainer.scrollTop = chatContainer.scrollHeight;
    }

    function clearChat() {
      chatContainer.innerHTML = "";
    }

    function normalize(text) {
      return text.trim();
    }

    function getPatientReply(message) {
      if (!currentCase) {
        return "まずは上の欄に国家試験の問題番号（症例ID）を入力して「症例セット」を押してください。";
      }

      const m = normalize(message);

      if (/こんにちは|こんばんは|おはよう|よろしく|はじめまして/.test(m)) {
        return "よろしくお願いします。今日はよろしくお願いします。";
      }

      if (m.includes("どうされました") || m.includes("主訴") ||
          m.includes("一番つらい") || m.includes("今日は何") ||
          m.includes("どのような症状")) {
        return currentCase.cc || "なんとなく体調が悪くて来ました。";
      }

      if (m.includes("いつから") || m.includes("経過") ||
          m.includes("どのように") || m.includes("発症") ||
          m.includes("悪くなっ") || m.includes("良くなっ") ||
          m.includes("きっかけ")) {
        return currentCase.hpi || currentCase.cc || "特に変わりはありません。";
      }

      if (m.includes("既往") || m.includes("今まで") ||
          m.includes("大きな病気") || m.includes("入院") ||
          m.includes("手術")) {
        return currentCase.pmh || "これまで大きな病気や入院はありません。";
      }

      if (m.includes("お薬") || m.includes("内服") ||
          m.includes("飲んでいる薬") || m.includes("服用") ||
          m.includes("薬を飲んで")) {
        return currentCase.meds || "今は特に薬は飲んでいません。";
      }

      if (m.includes("アレルギー") || m.includes("薬で") ||
          m.includes("湿疹") || m.includes("じんましん") ||
          m.includes("食べ物で")) {
        return currentCase.allergies || "特にアレルギーはありません。";
      }

      if (m.includes("家族") || m.includes("ご家族") ||
          m.includes("遺伝") || m.includes("身内")) {
        return currentCase.family || "特に家族に大きな病気はいません。";
      }

      if (m.includes("お仕事") || m.includes("仕事") ||
          m.includes("職業") || m.includes("喫煙") ||
          m.includes("たばこ") || m.includes("タバコ") ||
          m.includes("飲酒") || m.includes("お酒") ||
          m.includes("生活") || m.includes("運動")) {
        return currentCase.social || "事務の仕事をしています。喫煙はなく、お酒は少しだけです。";
      }

      if (m.includes("他に") || m.includes("気になること") ||
          m.includes("ほかに") || m.includes("症状") ||
          m.includes("全身") || m.includes("他には")) {
        return currentCase.ros || "他には特に気になる症状はありません。";
      }

      if (m.includes("診察") || m.includes("身体診察") ||
          m.includes("触らせて") || m.includes("聴診") ||
          m.includes("バイタル") || m.includes("身体所見") ||
          m.includes("理学所見")) {
        return currentCase.pe || "診察では明らかな異常はありません。";
      }

      if (m.includes("採血") || m.includes("血液検査") ||
          m.includes("検査結果") || m.includes("生化学") ||
          m.includes("血算") || m.includes("データ")) {
        if (currentCase.labs_basic || currentCase.labs_advanced) {
          return [
            currentCase.labs_basic,
            currentCase.labs_advanced
          ].filter(Boolean).join("\n");
        }
        return "まだ採血の結果は出ていません。";
      }

      if (m.includes("レントゲン") || m.includes("X線") ||
          m.includes("CT") || m.includes("MRI") ||
          m.includes("超音波") || m.includes("エコー") ||
          m.includes("画像")) {
        return currentCase.imaging || "まだ画像検査は行っていません。";
      }

      if (m.includes("診断") || m.includes("病名") ||
          m.includes("何の病気") || m.includes("考えられる疾患") ||
          m.includes("最も考えられる")) {
        if (currentCase.diagnosis) {
          return "最終的には「" + currentCase.diagnosis + "」と説明を受けました。";
        }
        return "まだはっきりした病名は分かっていません。";
      }

      return currentCase.fallback ||
             "すみません、その質問がこの症例ではうまく設定されていません。別の聞き方で質問してみてもらえますか？";
    }

    function loadCaseById(id) {
      const key = id.trim();
      const c = cases[key];
      currentCase = c || null;
      clearChat();

      if (!c) {
        addSystemMessage("症例ID「" + key + "」は登録されていません。上のコード内の cases に症例を追加してください。");
        caseStatus.textContent = "症例ID「" + key + "」はまだ登録されていません。sample-1 のように、コード内の cases オブジェクトに症例を追加してください。";
        return;
      }

      caseStatus.textContent = "現在の症例: " + key + " ｜ " + (c.title || "無題症例");
      addSystemMessage("症例「" + key + " : " + (c.title || "無題症例") + "」を読み込みました。");

      if (c.note) {
        addSystemMessage(c.note);
      }
      if (c.backdrop) {
        addChatMessage(c.backdrop, "patient");
      } else {
        addChatMessage("◯歳◯性の患者さんとして話します。まずは「本日はどうされましたか？」などと聞いてください。", "patient");
      }
    }

    loadCaseBtn.addEventListener("click", () => {
      const id = caseIdInput.value || "sample-1";
      loadCaseById(id);
    });

    caseIdInput.addEventListener("keydown", (e) => {
      if (e.key === "Enter") {
        e.preventDefault();
        loadCaseById(caseIdInput.value || "sample-1");
      }
    });

    messageForm.addEventListener("submit", (e) => {
      e.preventDefault();
      const text = messageInput.value.trim();
      if (!text) return;
      addChatMessage(text, "doctor");
      const reply = getPatientReply(text);
      setTimeout(() => {
        addChatMessage(reply, "patient");
      }, 150);
      messageInput.value = "";
      messageInput.focus();
    });

    // 初回はサンプル症例を自動ロード
    loadCaseById("sample-1");
  </script>
</body>
</html>
