<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Учёт столовой — Школьная система</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <script src="https://unpkg.com/html5-qrcode"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>

  <style>
    :root {
      --bg: #0f172a;
      --card-bg: rgba(255, 255, 255, 0.05);
      --border: rgba(255, 255, 255, 0.1);
      --text: #f8fafc;
      --text-muted: #94a3b8;
      --accent: #38bdf8;
      --green: #22c55e;
      --red: #ef4444;
      --orange: #f59e0b;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Inter', sans-serif; }
    body { background: linear-gradient(135deg, #0f172a 0%, #1e1b4b 100%); color: var(--text); min-height: 100vh; padding: 20px; }
    .container { max-width: 1200px; margin: 0 auto; }

    .role-selection {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 80vh;
      gap: 20px;
    }
    .role-card {
      background: var(--card-bg);
      border: 1px solid var(--border);
      backdrop-filter: blur(12px);
      padding: 25px 40px;
      border-radius: 20px;
      text-align: center;
      width: 100%;
      max-width: 400px;
      cursor: pointer;
      transition: 0.3s;
    }
    .role-card:hover { transform: translateY(-4px); background: rgba(255, 255, 255, 0.1); border-color: var(--accent); }
    .role-card h2 { margin-bottom: 8px; color: var(--accent); }

    .login-box {
      background: var(--card-bg);
      border: 1px solid var(--border);
      backdrop-filter: blur(12px);
      padding: 30px;
      border-radius: 20px;
      width: 100%;
      max-width: 400px;
    }
    .login-box h2 { color: var(--accent); margin-bottom: 20px; text-align: center; }

    .panel, .screen { display: none; }
    .active-panel, .active-screen { display: block; }
    .role-selection.active-screen { display: flex; }

    header {
      background: var(--card-bg);
      border: 1px solid var(--border);
      backdrop-filter: blur(12px);
      padding: 20px;
      border-radius: 16px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 25px;
      flex-wrap: wrap;
      gap: 15px;
    }

    .btn {
      background: var(--card-bg);
      border: 1px solid var(--border);
      color: var(--text);
      padding: 10px 18px;
      border-radius: 10px;
      cursor: pointer;
      font-weight: 600;
      transition: 0.2s;
    }
    .btn:hover { background: rgba(255, 255, 255, 0.15); }
    .btn-green { background: rgba(34, 197, 94, 0.2); border-color: var(--green); color: var(--green); }
    .btn-red { background: rgba(239, 68, 68, 0.2); border-color: var(--red); color: var(--red); }
    .btn-small { padding: 4px 8px; font-size: 0.75rem; border-radius: 6px; }
    .btn-full { width: 100%; }

    input, select {
      width: 100%;
      padding: 12px;
      background: rgba(255, 255, 255, 0.05);
      border: 1px solid var(--border);
      border-radius: 10px;
      color: #fff;
      margin-bottom: 12px;
    }

    .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; }
    .class-box { background: var(--card-bg); border: 1px solid var(--border); padding: 15px; border-radius: 16px; }
    .student-item { display: flex; justify-content: space-between; align-items: center; padding: 8px 0; border-bottom: 1px solid var(--border); gap: 8px; }
    .student-item .meta { color: var(--text-muted); font-size: 0.75rem; }

    #qrcode-box { background: white; padding: 20px; border-radius: 16px; display: inline-block; margin-top: 15px; }

    .duty-list { background: var(--card-bg); border: 1px solid var(--border); padding: 15px; border-radius: 16px; margin-bottom: 25px; }
    .duty-item { display: flex; justify-content: space-between; align-items: center; padding: 8px 0; border-bottom: 1px solid var(--border); gap: 8px; }

    .back-link { color: var(--text-muted); cursor: pointer; text-align: center; margin-top: 15px; display: block; }
    .back-link:hover { color: var(--text); }
  </style>
</head>
<body>

  <div class="container">

    <!-- Экран 1: Выбор роли -->
    <div id="roleScreen" class="role-selection active-screen">
      <h1 style="margin-bottom: 20px;">Школьная столовая</h1>
      <p style="color: var(--text-muted); margin-bottom: 30px;">Выберите вашу роль для входа в систему:</p>

      <div class="role-card" onclick="showScreen('adminLoginScreen', initAdminLogin)">
        <h2>👨‍💼 Администратор</h2>
        <p>Пароль: ADMIN2026</p>
      </div>

      <div class="role-card" onclick="showScreen('dutyLoginScreen', initDutyLogin)">
        <h2>🛡️ Дежурный</h2>
        <p>Пароль: 1111</p>
      </div>

      <div class="role-card" onclick="showScreen('studentLoginScreen', initStudentLogin)">
        <h2>🎓 Ученик</h2>
        <p>Пароль: 2222</p>
      </div>
    </div>

    <!-- Экран входа Администратора -->
    <div id="adminLoginScreen" class="role-selection screen">
      <div class="login-box">
        <h2>👨‍💼 Вход администратора</h2>
        <input type="password" id="adminPassInput" placeholder="Пароль администратора">
        <button class="btn btn-green btn-full" onclick="adminLoginSubmit()">Войти</button>
        <span class="back-link" onclick="logout()">← Назад</span>
      </div>
    </div>

    <!-- Экран входа Дежурного -->
    <div id="dutyLoginScreen" class="role-selection screen">
      <div class="login-box">
        <h2>🛡️ Вход дежурного</h2>
        <select id="dutySelect"></select>
        <input type="password" id="dutyPassInput" placeholder="Введите пароль (1111)">
        <button class="btn btn-green btn-full" onclick="dutyLoginSubmit()">Войти</button>
        <span class="back-link" onclick="logout()">← Назад</span>
      </div>
    </div>

    <!-- Экран входа Ученика -->
    <div id="studentLoginScreen" class="role-selection screen">
      <div class="login-box">
        <h2>🎓 Вход ученика</h2>
        <select id="studentClassSelect" onchange="loadStudentNamesForLogin()">
          <option value="">-- Выберите класс --</option>
        </select>
        <select id="studentNameSelect">
          <option value="">-- Сначала выберите класс --</option>
        </select>
        <input type="password" id="studentPassInput" placeholder="Введите пароль (2222)">
        <button class="btn btn-green btn-full" onclick="studentLoginSubmit()">Войти</button>
        <span class="back-link" onclick="logout()">← Назад</span>
      </div>
    </div>

    <!-- Экран 2: Панель Администратора -->
    <div id="adminPanel" class="panel">
      <header>
        <div>
          <h2>Панель Администратора</h2>
          <p id="adminDate" style="color: var(--text-muted);"></p>
        </div>
        <div>
          <button class="btn btn-green" onclick="showPanel('adminCodesPanel', renderCodesPanel)">🔑 Коды учеников и дежурных</button>
          <button class="btn btn-green" onclick="exportExcel()">📊 Экспорт в Excel</button>
          <button class="btn btn-red" onclick="logout()">Выход</button>
        </div>
      </header>

      <div style="background: var(--card-bg); padding: 15px; border-radius: 12px; margin-bottom: 20px;">
        <p>🔑 <b>Администратор:</b> <span style="color: var(--accent);">ADMIN2026</span></p>
        <p>🛡️ <b>Дежурный:</b> <span style="color: var(--accent);">1111</span></p>
        <p>🎓 <b>Ученик:</b> <span style="color: var(--accent);">2222</span></p>
      </div>

      <h3 style="margin-bottom: 15px;">Дежурные <button class="btn btn-small" onclick="addDutyOfficer()">➕ Добавить дежурного</button></h3>
      <div class="duty-list" id="dutyAdminList"></div>

      <h3 style="margin-bottom: 15px;">Младшая смена (12:10 – 12:50)</h3>
      <div class="grid" id="adminJuniorGrid"></div>

      <h3 style="margin: 25px 0 15px;">Старшая смена (13:10 – 13:50)</h3>
      <div class="grid" id="adminSeniorGrid"></div>
    </div>

    <!-- Экран 2.5: Коды учеников и дежурных -->
    <div id="adminCodesPanel" class="panel">
      <header>
        <h2>🔑 Коды учеников и дежурных</h2>
        <div>
          <button class="btn" onclick="showPanel('adminPanel', initAdmin)">← Назад в панель</button>
          <button class="btn btn-green" onclick="exportExcel()">📊 Экспорт в Excel</button>
          <button class="btn btn-red" onclick="logout()">Выход</button>
        </div>
      </header>

      <h3 style="margin-bottom: 15px;">Дежурные</h3>
      <div class="duty-list" id="codesDutyList"></div>

      <h3 style="margin-bottom: 15px;">Ученики</h3>
      <div class="grid" id="codesStudentGrid"></div>
    </div>

    <!-- Экран 3: Панель Дежурного -->
    <div id="dutyPanel" class="panel">
      <header>
        <h2 id="dutyPanelTitle">Панель Дежурного</h2>
        <button class="btn btn-red" onclick="logout()">Выход</button>
      </header>

      <div style="text-align: center; max-width: 500px; margin: 0 auto;">
        <button class="btn btn-green" style="width: 100%; padding: 15px; font-size: 1.1rem; margin-bottom: 20px;" onclick="startScanner()">📷 Включить QR-Сканер</button>
        <div id="scannerContainer" style="display:none; margin-bottom: 20px;">
          <div id="reader"></div>
        </div>
        <div id="scanResult" style="font-size: 1.2rem; font-weight: bold; margin-top: 15px;"></div>
      </div>
    </div>

    <!-- Экран 4: Панель Ученика -->
    <div id="studentPanel" class="panel">
      <header>
        <h2 id="studentPanelTitle">Кабинет Ученика</h2>
        <button class="btn btn-red" onclick="logout()">Выход</button>
      </header>

      <div style="text-align: center; max-width: 400px; margin: 0 auto;">
        <h3>Ваш QR-код на обед</h3>
        <div id="qrcode-box" style="margin-top: 20px;">
          <div id="qrcode"></div>
          <p style="color: #000; margin-top: 10px; font-weight: bold;" id="qrLabel"></p>
        </div>
      </div>
    </div>

  </div>

  <script>
    const CLASSES = {
      junior: ['7А', '7Б', '8А', '8Б'],
      senior: ['9А', '9Б', '10А', '10Б', '11А', '11Б']
    };
    
    // Фиксированные единые пароли для всей системы
    const ADMIN_PASS = "ADMIN2026";
    const DEFAULT_DUTY_PASS = "1111";
    const DEFAULT_STUDENT_PASS = "2222";

    function safeAlert(msg) {
      alert(msg);
    }

    const memoryStore = {};
    function storageGet(key) {
      try { return localStorage.getItem(key); }
      catch (e) { return memoryStore[key] || null; }
    }
    function storageSet(key, value) {
      try { localStorage.setItem(key, value); }
      catch (e) { memoryStore[key] = value; }
    }

    function initDB() {
      const db = {};
      [...CLASSES.junior, ...CLASSES.senior].forEach(c => {
        db[c] = Array.from({ length: 26 }, (_, i) => ({
          id: `${c}_${i + 1}`,
          name: `Ученик №${i + 1}`,
          status: false,
          time: null,
          password: DEFAULT_STUDENT_PASS
        }));
      });
      return db;
    }

    let dbData = JSON.parse(storageGet('lunchSystemDB')) || initDB();

    function saveDB() {
      storageSet('lunchSystemDB', JSON.stringify(dbData));
    }

    function initDuty() {
      return [
        { id: 'duty_1', name: 'Дежурный 1', password: DEFAULT_DUTY_PASS },
        { id: 'duty_2', name: 'Дежурный 2', password: DEFAULT_DUTY_PASS },
        { id: 'duty_3', name: 'Дежурный 3', password: DEFAULT_DUTY_PASS }
      ];
    }

    let dutyData = JSON.parse(storageGet('lunchDutyDB')) || initDuty();

    function saveDuty() {
      storageSet('lunchDutyDB', JSON.stringify(dutyData));
    }

    let currentStudent = null;
    let currentDuty = null;

    function showScreen(screenId, callback) {
      document.querySelectorAll('.role-selection').forEach(s => s.classList.remove('active-screen'));
      document.getElementById(screenId).classList.add('active-screen');
      if (callback) callback();
    }

    function showPanel(panelId, callback) {
      document.querySelectorAll('.role-selection').forEach(s => s.classList.remove('active-screen'));
      document.querySelectorAll('.panel').forEach(p => p.classList.remove('active-panel'));
      document.getElementById(panelId).classList.add('active-panel');
      if (callback) callback();
    }

    function logout() {
      document.querySelectorAll('.panel').forEach(p => p.classList.remove('active-panel'));
      document.querySelectorAll('.role-selection').forEach(s => s.classList.remove('active-screen'));
      document.getElementById('roleScreen').classList.add('active-screen');
      currentStudent = null;
      currentDuty = null;
    }

    function initAdminLogin() {
      document.getElementById('adminPassInput').value = '';
    }

    function adminLoginSubmit() {
      const pass = document.getElementById('adminPassInput').value.trim();
      if (pass === ADMIN_PASS) {
        showPanel('adminPanel', initAdmin);
      } else {
        safeAlert("Неверный пароль администратора!");
      }
    }

    function initDutyLogin() {
      const sel = document.getElementById('dutySelect');
      sel.innerHTML = '';
      dutyData.forEach(d => {
        sel.innerHTML += `<option value="${d.id}">${d.name}</option>`;
      });
      document.getElementById('dutyPassInput').value = '';
    }

    function dutyLoginSubmit() {
      const id = document.getElementById('dutySelect').value;
      const pass = document.getElementById('dutyPassInput').value.trim();
      const duty = dutyData.find(d => d.id === id);
      
      if (duty && (duty.password === pass || pass === DEFAULT_DUTY_PASS)) {
        currentDuty = duty;
        showPanel('dutyPanel', () => {
          document.getElementById('dutyPanelTitle').innerText = `Панель Дежурного — ${duty.name}`;
          document.getElementById('scanResult').innerText = '';
        });
      } else {
        safeAlert("Неверный пароль! Введите: 1111");
      }
    }

    function initStudentLogin() {
      const select = document.getElementById('studentClassSelect');
      select.innerHTML = '<option value="">-- Выберите класс --</option>';
      [...CLASSES.junior, ...CLASSES.senior].forEach(c => {
        select.innerHTML += `<option value="${c}">${c}</option>`;
      });
      document.getElementById('studentNameSelect').innerHTML = '<option value="">-- Сначала выберите класс --</option>';
      document.getElementById('studentPassInput').value = '';
    }

    function loadStudentNamesForLogin() {
      const c = document.getElementById('studentClassSelect').value;
      const nameSelect = document.getElementById('studentNameSelect');
      nameSelect.innerHTML = '';
      if (!c) return;
      dbData[c].forEach(s => {
        nameSelect.innerHTML += `<option value="${s.id}">${s.name}</option>`;
      });
    }

    function studentLoginSubmit() {
      const c = document.getElementById('studentClassSelect').value;
      const studentId = document.getElementById('studentNameSelect').value;
      const pass = document.getElementById('studentPassInput').value.trim();
      if (!c || !studentId) return safeAlert("Выберите класс и имя!");
      
      const student = dbData[c].find(s => s.id === studentId);
      
      if (student && (student.password === pass || pass === DEFAULT_STUDENT_PASS)) {
        currentStudent = student;
        showPanel('studentPanel', () => {
          document.getElementById('studentPanelTitle').innerText = `Кабинет Ученика — ${student.name}`;
          generateStudentQR();
        });
      } else {
        safeAlert("Неверный пароль! Введите: 2222");
      }
    }

    function initAdmin() {
      document.getElementById('adminDate').innerText = new Date().toLocaleDateString('ru-RU');
      renderDutyAdminList();
      renderAdminGrid(CLASSES.junior, 'adminJuniorGrid');
      renderAdminGrid(CLASSES.senior, 'adminSeniorGrid');
    }

    function renderDutyAdminList() {
      const container = document.getElementById('dutyAdminList');
      container.innerHTML = dutyData.map(d => `
        <div class="duty-item">
          <span>${d.name} <span class="meta">пароль: <b style="color: var(--accent);">${d.password}</b></span></span>
        </div>
      `).join('');
    }

    function addDutyOfficer() {
      const name = prompt("Имя нового дежурного:");
      if (!name) return;
      dutyData.push({ id: 'duty_' + Date.now(), name: name.trim(), password: DEFAULT_DUTY_PASS });
      saveDuty();
      renderDutyAdminList();
    }

    function renderCodesPanel() {
      const dutyContainer = document.getElementById('codesDutyList');
      dutyContainer.innerHTML = dutyData.map(d => `
        <div class="duty-item">
          <span>${d.name}</span>
          <span class="meta">пароль: <b style="color: var(--accent); font-size: 1rem;">${d.password}</b></span>
        </div>
      `).join('');

      const studentContainer = document.getElementById('codesStudentGrid');
      studentContainer.innerHTML = '';
      [...CLASSES.junior, ...CLASSES.senior].forEach(c => {
        const box = document.createElement('div');
        box.className = 'class-box';
        let list = dbData[c].map(s => `
          <div class="student-item">
            <span>${s.name}</span>
            <span class="meta">пароль: <b style="color: var(--accent); font-size: 1rem;">${s.password}</b></span>
          </div>
        `).join('');
        box.innerHTML = `<h4>Класс ${c}</h4><div style="margin-top:10px;">${list}</div>`;
        studentContainer.appendChild(box);
      });
    }

    function renderAdminGrid(classes, elementId) {
      const container = document.getElementById(elementId);
      container.innerHTML = '';
      classes.forEach(c => {
        const box = document.createElement('div');
        box.className = 'class-box';
        const presentCount = dbData[c].filter(s => s.status).length;

        let list = dbData[c].map(s => `
          <div class="student-item">
            <span>${s.name} ${s.time ? `<small>(${s.time})</small>` : ''}</span>
            <span style="color: ${s.status ? 'var(--green)' : 'var(--red)'}; font-weight: bold;">
              ${s.status ? 'Зашёл' : '—'}
            </span>
          </div>
        `).join('');

        box.innerHTML = `<h4>Класс ${c} (${presentCount}/26)</h4><div style="margin-top:10px;">${list}</div>`;
        container.appendChild(box);
      });
    }

    let html5QrcodeScanner;
    function startScanner() {
      document.getElementById('scannerContainer').style.display = 'block';
      html5QrcodeScanner = new Html5QrcodeScanner("reader", { fps: 10, qrbox: 250 });
      html5QrcodeScanner.render((decodedText) => {
        markStudentPresent(decodedText);
      });
    }

    function markStudentPresent(studentId) {
      for (let c in dbData) {
        let student = dbData[c].find(s => s.id === studentId);
        if (student) {
          if (student.status) {
            document.getElementById('scanResult').innerText = `⚠️ ${student.name} (${c}) уже отмечался!`;
            document.getElementById('scanResult').style.color = 'orange';
          } else {
            student.status = true;
            student.time = new Date().toLocaleTimeString('ru-RU', { hour: '2-digit', minute: '2-digit' });
            saveDB();
            document.getElementById('scanResult').innerText = `✅ ${student.name} (${c}) отмечен!`;
            document.getElementById('scanResult').style.color = 'var(--green)';
          }
          return;
        }
      }
      document.getElementById('scanResult').innerText = "❌ Неизвестный QR-код!";
      document.getElementById('scanResult').style.color = 'var(--red)';
    }

    function generateStudentQR() {
      if (!currentStudent) return;
      document.getElementById('qrcode').innerHTML = '';
      new QRCode(document.getElementById("qrcode"), { text: currentStudent.id, width: 200, height: 200 });
      document.getElementById('qrLabel').innerText = currentStudent.name;
    }

    function exportExcel() {
      const rows = [];
      Object.keys(dbData).forEach(c => {
        dbData[c].forEach(s => {
          rows.push({ 'Класс': c, 'Имя': s.name, 'Поел': s.status ? 'Да' : 'Нет', 'Время': s.time || '-' });
        });
      });

      const ws = XLSX.utils.json_to_sheet(rows);
      const wb = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(wb, ws, "Столовая");
      XLSX.writeFile(wb, `Отчет_Столовая.xlsx`);
    }
  </script>
</body>
</html>
