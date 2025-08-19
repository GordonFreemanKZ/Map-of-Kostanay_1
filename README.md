<!doctype html>
<html lang="ru">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Радужная страница для большого текста</title>
  <style>
    :root{
      --sheet-bg-light: rgba(255,255,255,0.88);
      --sheet-bg-dark: rgba(0,0,0,0.55);
      --text-light: #111;
      --text-dark: #f4f4f4;
      --accent: rgba(255,255,255,0.6);
    }

    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, Cantarell, "Helvetica Neue", Arial, "Noto Sans", "Apple Color Emoji", "Segoe UI Emoji";
      line-height:1.6;
      /* Анимированный радужный фон */
      background: linear-gradient(120deg, #ff0066, #ff9900, #ffee00, #33cc33, #00bbff, #6633ff, #cc00ff, #ff0066);
      background-size: 600% 600%;
      animation: rainbow 18s ease infinite;
      min-height:100%;
    }
    @keyframes rainbow{
      0%{background-position:0% 50%}
      50%{background-position:100% 50%}
      100%{background-position:0% 50%}
    }

    .wrap{ 
      display:grid; 
      grid-template-rows:auto 1fr; 
      min-height:100vh; 
    }

    .toolbar{
      position: sticky;
      top: 0;
      z-index: 10;
      display:flex;
      gap:.5rem;
      flex-wrap:wrap;
      padding: .75rem 1rem;
      backdrop-filter: blur(8px) saturate(120%);
      background: rgba(255,255,255,0.25);
      border-bottom: 1px solid rgba(255,255,255,0.35);
    }
    @media (prefers-color-scheme: dark){
      .toolbar{background: rgba(0,0,0,0.35); border-bottom-color: rgba(255,255,255,0.2)}
    }

    .btn{
      appearance:none; border:none; cursor:pointer; 
      padding:.6rem .9rem; border-radius:999px; 
      background: rgba(255,255,255,0.8);
      box-shadow: 0 1px 2px rgba(0,0,0,.1), inset 0 0 0 1px rgba(0,0,0,.05);
      transition: transform .05s ease, box-shadow .2s ease, background .2s ease;
      font-weight:600; letter-spacing:.2px;
    }
    .btn:hover{ box-shadow: 0 6px 18px rgba(0,0,0,.15) }
    .btn:active{ transform: translateY(1px) }
    .btn.secondary{ background: rgba(255,255,255,0.6) }
    .btn.danger{ background: rgba(255,255,255,0.75); color:#a1002a }
    .btn input{ display:none }

    .spacer{flex:1}

    .sheet{
      width:min(1200px, 92vw);
      margin: 2rem auto 3rem;
      padding: clamp(16px, 2vw, 32px);
      border-radius: 24px;
      background: var(--sheet-bg-light);
      color: var(--text-light);
      box-shadow: 0 20px 60px rgba(0,0,0,.25);
      backdrop-filter: blur(10px) saturate(120%);
    }
    @media (prefers-color-scheme: dark){
      .sheet{ background: var(--sheet-bg-dark); color: var(--text-dark) }
    }

    h1{
      margin:0 0 .6rem 0; font-size: clamp(1.4rem, 1.6rem + 1vw, 2.2rem);
    }
    .hint{opacity:.8; font-size:.95rem; margin-bottom:1rem}

    #editor{
      min-height: 70vh;
      outline: none;
      white-space: pre-wrap; /* сохраняет переводы строк */
      word-wrap: break-word;
      overflow-wrap: anywhere;
      font-size: clamp(16px, 1rem + .3vw, 20px);
      line-height: 1.7;
    }
    /* Плейсхолдер для пустого контента */
    #editor:empty:before{
      content: 'Вставьте или напечатайте ваш текст здесь…';
      opacity:.55;
    }

    /* Печать — чистый лист без фона/тулбара */
    @media print{
      body{ animation: none; background: #fff; }
      .toolbar{ display:none }
      .sheet{ box-shadow:none; background:#fff; color:#000; width:100%; margin:0; border-radius:0; }
      #editor{ min-height:auto }
    }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="toolbar">
      <button class="btn" id="copyBtn" title="Скопировать всё (Ctrl/Cmd+C)">Копировать</button>
      <button class="btn" id="downloadTxt" title="Скачать как .txt">Скачать .txt</button>
      <button class="btn secondary" id="downloadHtml" title="Скачать как .html">Скачать .html</button>
      <label class="btn secondary" title="Импорт .txt">
        Импорт .txt
        <input type="file" id="importTxt" accept="text/plain" />
      </label>
      <div class="spacer"></div>
      <button class="btn danger" id="clearBtn" title="Очистить страницу">Очистить</button>
      <button class="btn" id="printBtn" title="Печать / PDF">Печать</button>
    </div>

    <main class="sheet" role="main" aria-label="Текстовый лист">
      <h1>Ваш большой текст</h1>
      <p class="hint">Просто вставьте сюда (Ctrl/Cmd+V) или начните печатать. Текст автоматически сохраняется в браузере и не покидает ваш компьютер.</p>
      <div id="editor" contenteditable="true" spellcheck="false"></div>
    </main>
  </div>

  <script>
    const editor = document.getElementById('editor');
    const copyBtn = document.getElementById('copyBtn');
    const clearBtn = document.getElementById('clearBtn');
    const downloadTxt = document.getElementById('downloadTxt');
    const downloadHtml = document.getElementById('downloadHtml');
    const importTxt = document.getElementById('importTxt');
    const printBtn = document.getElementById('printBtn');

    // Автосохранение в LocalStorage
    const STORAGE_KEY = 'rainbow-bigtext-v1';
    function save(){ localStorage.setItem(STORAGE_KEY, editor.innerHTML); }
    function load(){ const data = localStorage.getItem(STORAGE_KEY); if(data) editor.innerHTML = data; }
    load();
    let saveTimer;
    editor.addEventListener('input', () => { clearTimeout(saveTimer); saveTimer = setTimeout(save, 300); });

    // Копировать всё
    copyBtn.addEventListener('click', async () => {
      const selection = window.getSelection();
      const range = document.createRange();
      range.selectNodeContents(editor);
      selection.removeAllRanges();
      selection.addRange(range);
      try{
        await navigator.clipboard.writeText(editor.innerText);
      }catch(e){ /* молча — не везде разрешено */ }
    });

    // Очистка
    clearBtn.addEventListener('click', () => {
      if(confirm('Очистить весь текст?')){ editor.innerHTML=''; save(); }
    });

    // Скачать как TXT
    downloadTxt.addEventListener('click', () => {
      const blob = new Blob([editor.innerText], {type:'text/plain;charset=utf-8'});
      const a = document.createElement('a');
      a.href = URL.createObjectURL(blob);
      a.download = 'text.txt';
      a.click();
      URL.revokeObjectURL(a.href);
    });

    // Скачать как HTML (со стилями)
    downloadHtml.addEventListener('click', () => {
      const htmlDoc = `<!doctype html><html lang="ru"><meta charset="utf-8"><title>Текст</title><style>body{font-family:system-ui,Segoe UI,Roboto,Arial;line-height:1.7;padding:24px;max-width:900px;margin:0 auto;white-space:pre-wrap;word-wrap:break-word;overflow-wrap:anywhere}</style><body>${editor.innerHTML}</body></html>`;
      const blob = new Blob([htmlDoc], {type:'text/html;charset=utf-8'});
      const a = document.createElement('a');
      a.href = URL.createObjectURL(blob);
      a.download = 'text.html';
      a.click();
      URL.revokeObjectURL(a.href);
    });

    // Импорт .txt
    importTxt.addEventListener('change', async (e) => {
      const file = e.target.files?.[0];
      if(!file) return;
      const text = await file.text();
      editor.textContent = text; // сохраняем переносы строк
      save();
      importTxt.value = '';
    });

    // Печать
    printBtn.addEventListener('click', () => window.print());

    // Клавиатурные шорткаты: Ctrl/Cmd+S — скачать .txt
    window.addEventListener('keydown', (e) => {
      if((e.ctrlKey || e.metaKey) && e.key.toLowerCase() === 's'){
        e.preventDefault();
        downloadTxt.click();
      }
    });
  </script>
</body>
</html>
