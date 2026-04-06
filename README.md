<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GuardSIX - نظام المحاكم ووزارة العدل</title>
    <style>
        :root {
            --bg-dark: #121212;
            --card-bg: #1e1e1e;
            --input-bg: #2d2d2d;
            --accent-color: #ff9800;
            --text-main: #ffffff;
            --text-muted: #aaaaaa;
            --btn-green: #2e7d32;
            --btn-red: #c62828;
            --border-color: #2d2d2d;
        }

        body {
            margin: 0; padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #2c2c2c;
            height: 100vh;
            display: flex; justify-content: center; align-items: center;
            color: var(--text-main);
            direction: rtl;
        }

        .system-container {
            width: 95%; max-width: 1200px; height: 750px;
            background-color: var(--bg-dark); border-radius: 12px;
            display: flex; overflow: hidden;
            box-shadow: 0 10px 30px rgba(0,0,0,0.7);
            position: relative;
        }

        .login-top-btn {
            position: absolute;
            top: 20px; left: 20px;
            background-color: var(--accent-color);
            color: #000; padding: 10px 15px;
            border-radius: 6px; font-weight: bold;
            cursor: pointer; border: none; z-index: 10;
        }

        .sidebar {
            width: 220px; background-color: #181818;
            padding: 20px 10px; display: flex; flex-direction: column;
            border-left: 1px solid var(--border-color);
        }

        .profile-section {
            display: flex; align-items: center; padding-bottom: 20px;
            border-bottom: 1px solid var(--border-color); margin-bottom: 20px;
        }

        .avatar {
            width: 40px; height: 40px; background-color: var(--accent-color);
            border-radius: 50%; display: flex; align-items: center; justify-content: center;
            font-weight: bold; color: #000; margin-left: 10px;
        }

        .profile-info h4 { margin: 0; font-size: 14px; }
        .profile-info span { font-size: 11px; color: var(--accent-color); }

        .nav-item {
            padding: 12px 15px; border-radius: 8px; cursor: pointer;
            font-size: 14px; margin-bottom: 5px; transition: 0.2s;
            color: var(--text-muted);
        }

        .nav-item:hover, .nav-item.active {
            background-color: #2d2d2d; color: var(--text-main);
        }

        .main-content {
            flex: 1; padding: 25px;
            display: flex; flex-direction: column; gap: 20px;
            overflow-y: auto; margin-top: 40px;
        }

        .header-title { font-size: 22px; font-weight: bold; margin-bottom: 10px; }

        .card {
            background-color: var(--card-bg); border-radius: 10px;
            padding: 20px; display: flex; flex-direction: column; gap: 15px;
        }

        .card.full-width { grid-column: span 2; }

        .card-title {
            font-size: 14px; color: var(--text-muted);
            border-bottom: 1px solid var(--border-color); padding-bottom: 10px;
        }

        .info-row {
            display: flex; justify-content: space-between;
            padding: 8px 0; border-bottom: 1px solid #2a2a2a; font-size: 13px;
        }

        .info-label { color: var(--text-muted); }
        .info-value { font-weight: bold; }

        input, select, textarea {
            background-color: var(--input-bg); border: 1px solid #444;
            border-radius: 6px; padding: 10px; color: #fff; text-align: right;
        }

        textarea { resize: none; height: 80px; font-family: inherit; }

        button {
            background-color: var(--accent-color); border: none;
            border-radius: 6px; padding: 10px 15px; cursor: pointer;
            font-weight: bold; color: #000; transition: 0.2s;
        }
        
        button:hover { opacity: 0.9; }

        .btn-action { color: #fff; text-align: center;}
        .btn-suspend { background-color: var(--btn-red); }
        .btn-restore { background-color: var(--btn-green); }

        .data-table { width: 100%; border-collapse: collapse; font-size: 14px; }
        .data-table th, .data-table td { text-align: right; padding: 12px; border-bottom: 1px solid var(--border-color); }
        .data-table th { color: var(--text-muted); font-weight: normal; }

        .modal {
            display: none; position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background-color: rgba(0,0,0,0.8);
            justify-content: center; align-items: center; z-index: 1000;
        }

        .modal-content {
            background-color: var(--bg-dark); width: 450px; padding: 25px;
            border-radius: 12px; display: flex; flex-direction: column; gap: 15px;
            border: 1px solid #333;
        }

        .citizens-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .box-sub { background-color: #262626; padding: 10px; border-radius: 6px; text-align: center; }
    </style>
</head>
<body>

    <div class="system-container">
        
        <button id="btn-login-trigger" class="login-top-btn" onclick="openLoginModal()">تسجيل دخول - وزارة العدل</button>
        <button id="btn-logout-trigger" class="login-top-btn" style="display:none; background-color:var(--btn-red); color:#fff;" onclick="logout()">تسجيل الخروج</button>

        <div class="sidebar">
            <div class="profile-section" id="sidebar-profile" style="display:none;">
                <div class="avatar" id="judge-avatar">ق</div>
                <div class="profile-info">
                    <h4 id="judge-name">القاضي</h4>
                    <span>وزارة العدل</span>
                </div>
            </div>
            
            <div id="nav-create-court" class="nav-item active" onclick="switchSection('create-court')">رفع قضية</div>
            <div id="nav-request-lawyer" class="nav-item" onclick="switchSection('request-lawyer')">طلب محامي</div>
            <div id="nav-contact-justice" class="nav-item" onclick="switchSection('contact-justice')">تواصل مع العدل</div>
            
            <div id="nav-citizens" class="nav-item" style="display:none;" onclick="switchSection('citizens')">بيانات المواطنين</div>
            <div id="nav-courts-list" class="nav-item" style="display:none;" onclick="switchSection('courts-list')">جدول القضايا</div>
            <div id="nav-finance" class="nav-item" style="display:none;" onclick="switchSection('finance')">الميزانية</div>
            <div id="nav-employees" class="nav-item" style="display:none;" onclick="switchSection('employees')">إدارة الموظفين</div>
            <div id="nav-tickets" class="nav-item" style="display:none;" onclick="switchSection('tickets')">التذاكر والرسائل</div>
        </div>

        <div class="main-content">
            
            <div id="section-create-court">
                <div class="header-title">رفع قضية / بلاغ جديد</div>
                <div class="card full-width">
                    <div class="card-title">تعبئة بيانات الشكوى</div>
                    <div style="display:grid; grid-template-columns: 1fr 1fr; gap:15px;">
                        <input type="text" placeholder="اسمك الكامل (المدعي)">
                        <input type="text" placeholder="اسم الشخص الذي تشتكيه (المدعى عليه)">
                        <input type="text" placeholder="موضوع القضية / التهمة" style="grid-column: span 2;">
                        <textarea placeholder="اشرح تفاصيل ما حدث..." style="grid-column: span 2;"></textarea>
                        <button class="btn-action btn-restore" style="grid-column: span 2;" onclick="alert('تم إرسال بلاغك!')">تقديم القضية رسمياً</button>
                    </div>
                </div>
            </div>

            <div id="section-request-lawyer" style="display:none;">
                <div class="header-title">طلب توكيل محامي</div>
                <div class="card full-width">
                    <div class="card-title">طلب محامي عام من وزارة العدل</div>
                    <div style="display:flex; flex-direction:column; gap:15px;">
                        <input type="text" placeholder="اسمك بالكامل">
                        <textarea placeholder="لماذا تحتاج محامي؟"></textarea>
                        <button class="btn-action btn-restore" onclick="alert('تم إرسال طلبك!')">إرسال الطلب</button>
                    </div>
                </div>
            </div>

            <div id="section-contact-justice" style="display:none;">
                <div class="header-title">الدعم الفني والتواصل</div>
                <div class="card full-width">
                    <div class="card-title">أرسل رسالة مباشرة إلى الإدارة</div>
                    <div style="display:flex; flex-direction:column; gap:15px;">
                        <input type="text" id="ticket-sender" placeholder="الاسم الكامل">
                        <textarea id="ticket-message" placeholder="اكتب رسالتك هنا..."></textarea>
                        <button class="btn-action btn-restore" onclick="sendTicket()">إرسال الرسالة</button>
                    </div>
                </div>
            </div>


            <div id="section-citizens" style="display:none;">
                <div class="header-title">Citizen Database</div>
                <div class="card full-width">
                    <div class="card-title">Search by Citizen ID</div>
                    <div style="display:flex; gap:10px;">
                        <input type="text" id="search-id-field" placeholder="9287" style="flex:1;">
                        <button onclick="alert('جاري البحث...')">🔍</button>
                    </div>
                </div>
                <div class="citizens-grid">
                    <div class="card">
                        <div class="card-title">👤 Citizen Info</div>
                        <div class="info-row"><span class="info-label">Name</span><span class="info-value">Kane Kee</span></div>
                        <div class="info-row"><span class="info-label">Job / Company</span><span class="info-value">Judiciary</span></div>
                        <div class="info-row"><span class="info-label">Citizen ID</span><span class="info-value">9287</span></div>
                    </div>
                    <div class="card">
                        <div class="card-title">💵 Money</div>
                        <div class="info-row"><span class="info-label">Cash</span><span class="info-value" style="color:#4caf50;">$75,122,064</span></div>
                        <div class="info-row"><span class="info-label">Bank</span><span class="info-value" style="color:#2196f3;">$77,376</span></div>
                    </div>
                    <div class="card">
                        <div class="card-title">📌 Fines Summary</div>
                        <div style="display:flex; gap:10px;">
                            <div class="box-sub" style="flex:1; border:1px solid var(--btn-red); color:var(--btn-red);"><small>Unpaid</small><br><strong>$0</strong></div>
                            <div class="box-sub" style="flex:1; border:1px solid var(--btn-green); color:var(--btn-green);"><small>Paid</small><br><strong>$0</strong></div>
                        </div>
                    </div>
                    <div class="card">
                        <div class="card-title">🚫 Service Control</div>
                        <div style="display:flex; gap:10px;">
                            <button class="btn-action btn-suspend" style="flex:1;">🚫 Suspend</button>
                            <button class="btn-action btn-restore" style="flex:1;">✔ Restore</button>
                        </div>
                    </div>
                </div>
            </div>

            <div id="section-courts-list" style="display:none;">
                <div class="header-title">جدول القضايا النشطة</div>
                <div class="card full-width">
                    <div class="card-title">تسجيل قضية جديدة (خاص بالعدل)</div>
                    <div style="display:grid; grid-template-columns: 1fr 1fr; gap:15px;">
                        <input type="text" id="new-case-defendant" placeholder="اسم المتهم">
                        <input type="text" id="new-case-charge" placeholder="التهمة">
                        <textarea id="new-case-details" placeholder="اشرح تفاصيل ما حدث..." style="grid-column: span 2;"></textarea>
                        <input type="text" id="new-case-img" placeholder="رابط صورة الدليل (اختياري)">
                        <input type="text" id="new-case-video" placeholder="رابط فيديو الدليل (اختياري)">
                        <button class="btn-action btn-restore" style="grid-column: span 2;" onclick="addNewCase()">إرسال القضية</button>
                    </div>
                </div>
                <div class="card full-width" style="margin-top: 15px;">
                    <table class="data-table">
                        <thead><tr><th>رقم القضية</th><th>المدعي</th><th>المدعى عليه</th><th>التهمة</th><th>الإجراءات</th></tr></thead>
                        <tbody id="cases-tbody">
                            <tr id="row-1001">
                                <td>#1001</td><td class="plaintiff">وزارة العدل</td><td class="defendant">محمد العتيبي</td><td class="charge">سرقة بنك مركزي</td>
                                <td><button onclick="openEditModal('1001')">تعديل</button> <button class="btn-action btn-suspend" style="padding:5px 10px; font-size:12px;" onclick="this.parentElement.parentElement.remove()">حذف</button></td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>

            <div id="section-finance" style="display:none;">
                <div class="header-title">ميزانية وزارة العدل</div>
                <div class="card full-width">
                    <div class="card-title">خزينة المحكمة</div>
                    <span id="treasury-amount" style="font-size:32px; color:#4caf50; font-weight:bold;">$1,500,000</span>
                    <div style="display:flex; gap:15px; margin-top:15px;">
                        <input type="number" id="finance-amount" placeholder="0.00" style="flex:1;">
                        <button class="btn-action btn-restore" onclick="updateFinance('deposit')">إيداع فلوس</button>
                        <button class="btn-action btn-suspend" onclick="updateFinance('withdraw')">سحب فلوس</button>
                    </div>
                </div>
            </div>

            <div id="section-employees" style="display:none;">
                <div class="header-title">إدارة شؤون الموظفين</div>
                <div class="card full-width">
                    <div class="card-title">تعيين موظف جديد</div>
                    <div style="display:grid; grid-template-columns: 1fr 1fr; gap:15px;">
                        <input type="text" id="emp-name" placeholder="اسم الموظف الكامل">
                        <input type="text" id="emp-id" placeholder="رقم الهوية (ID)">
                        <select id="emp-rank" onchange="checkRankForPass()">
                            <option value="محامي">محامي</option>
                            <option value="مستشار قانوني">مستشار قانوني</option>
                            <option value="قاضي عام">قاضي عام</option>
                            <option value="قاضي عسكري">قاضي عسكري</option>
                            <option value="قاضي فيدرالي">قاضي فيدرالي</option>
                            <option value="نائب وزير العدل">نائب وزير العدل</option>
                            <option value="وزير العدل">وزير العدل</option>
                        </select>
                        <input type="text" id="emp-office" placeholder="اسم مكتب المحاماة (للمحامين فقط)">
                        <input type="password" id="emp-pass" placeholder="الرمز السري (لقاضي عام وفوق)" style="display:none; grid-column: span 2;">
                        <button class="btn-action btn-restore" style="grid-column: span 2;" onclick="addEmployee()">تعيين وتثبيت الموظف</button>
                    </div>
                </div>
                <div class="card full-width" style="margin-top:15px;">
                    <table class="data-table">
                        <thead><tr><th>الهوية</th><th>الاسم</th><th>الرتبة</th><th>المكتب</th><th>الإجراءات</th></tr></thead>
                        <tbody id="employees-tbody"></tbody>
                    </table>
                </div>
            </div>

            <div id="section-tickets" style="display:none;">
                <div class="header-title">مركز تذاكر التواصل</div>
                <div class="card full-width">
                    <table class="data-table">
                        <thead><tr><th>المرسل</th><th>الرسالة</th><th>الحالة</th><th>الإجراءات</th></tr></thead>
                        <tbody id="tickets-tbody">
                            <tr><td>خالد الأحمد</td><td>أريد الاستفسار عن موعد جلستي القادمة</td><td><span style="color:var(--accent-color);">قيد الانتظار</span></td><td><button onclick="this.parentElement.parentElement.remove()">إغلاق التذكرة</button></td></tr>
                        </tbody>
                    </table>
                </div>
            </div>

        </div>
    </div>

    <div class="modal" id="loginModal">
        <div class="modal-content">
            <div style="text-align:center; font-weight:bold;">بوابة موظفي وزارة العدل</div>
            <input type="text" id="login-name" placeholder="اسم الموظف">
            <input type="password" id="login-code" placeholder="الكود الخاص">
            <div style="display:flex; gap:10px;">
                <button class="btn-action btn-restore" style="flex:2;" onclick="checkLogin()">دخول</button>
                <button class="btn-action btn-suspend" style="flex:1;" onclick="closeModal()">إلغاء</button>
            </div>
        </div>
    </div>

    <div class="modal" id="editModal">
        <div class="modal-content">
            <div style="text-align:center; font-weight:bold;">تعديل القضية</div>
            <input type="hidden" id="edit-case-id">
            <input type="text" id="edit-defendant" placeholder="اسم المتهم">
            <input type="text" id="edit-charge" placeholder="التهمة">
            <div style="display:flex; gap:10px;">
                <button class="btn-action btn-restore" style="flex:2;" onclick="saveEditedCase()">حفظ</button>
                <button class="btn-action btn-suspend" style="flex:1;" onclick="closeEditModal()">إلغاء</button>
            </div>
        </div>
    </div>

    <script>
        const ministryUsers = { "عامر": "0000" };
        let currentBalance = 1500000;

        function switchSection(sectionName) {
            document.querySelectorAll('.main-content > div').forEach(div => div.style.display = 'none');
            document.getElementById('section-' + sectionName).style.display = 'block';
            document.querySelectorAll('.nav-item').forEach(item => item.classList.remove('active'));
            document.getElementById('nav-' + sectionName).classList.add('active');
        }

        function openLoginModal() { document.getElementById('loginModal').style.display = 'flex'; }
        function closeModal() { document.getElementById('loginModal').style.display = 'none'; }

        function checkLogin() {
            let nameInput = document.getElementById('login-name').value;
            let codeInput = document.getElementById('login-code').value;
            if (ministryUsers[nameInput] === codeInput) {
                document.getElementById('btn-login-trigger').style.display = 'none';
                document.getElementById('btn-logout-trigger').style.display = 'block';
                document.getElementById('sidebar-profile').style.display = 'flex';
                document.getElementById('judge-name').innerText = nameInput;

                ['nav-create-court', 'nav-request-lawyer', 'nav-contact-justice'].forEach(id => document.getElementById(id).style.display = 'none');
                ['nav-citizens', 'nav-courts-list', 'nav-finance', 'nav-employees', 'nav-tickets'].forEach(id => document.getElementById(id).style.display = 'block');
                closeModal();
                switchSection('citizens');
            } else { alert("بيانات الدخول غير صحيحة!"); }
        }

        function logout() { location.reload(); }

        function addNewCase() {
            let def = document.getElementById('new-case-defendant').value;
            let charge = document.getElementById('new-case-charge').value;
            if(!def || !charge) { alert("املأ البيانات أولاً!"); return; }
            let id = Math.floor(Math.random() * 1000) + 1000;
            let row = `<tr id="row-${id}"><td>#${id}</td><td>وزارة العدل</td><td class="defendant">${def}</td><td class="charge">${charge}</td><td><button onclick="openEditModal('${id}')">تعديل</button> <button class="btn-action btn-suspend" style="padding:5px 10px; font-size:12px;" onclick="this.parentElement.parentElement.remove()">حذف</button></td></tr>`;
            document.getElementById('cases-tbody').insertAdjacentHTML('beforeend', row);
            
            document.getElementById('new-case-defendant').value = "";
            document.getElementById('new-case-charge').value = "";
            document.getElementById('new-case-details').value = "";
            alert("تم إرسال القضية للجدول بنجاح!");
        }

        function openEditModal(caseId) {
            let row = document.getElementById(`row-${caseId}`);
            document.getElementById('edit-case-id').value = caseId;
            document.getElementById('edit-defendant').value = row.querySelector('.defendant').innerText;
            document.getElementById('edit-charge').value = row.querySelector('.charge').innerText;
            document.getElementById('editModal').style.display = 'flex';
        }
        function closeEditModal() { document.getElementById('editModal').style.display = 'none'; }
        
        function saveEditedCase() {
            let id = document.getElementById('edit-case-id').value;
            let row = document.getElementById(`row-${id}`);
            row.querySelector('.defendant').innerText = document.getElementById('edit-defendant').value;
            row.querySelector('.charge').innerText = document.getElementById('edit-charge').value;
            closeEditModal();
        }

        function updateFinance(type) {
            let amount = parseFloat(document.getElementById('finance-amount').value);
            if(isNaN(amount) || amount <= 0) { alert("أدخل مبلغاً صحيحاً!"); return; }
            if(type === 'withdraw' && amount > currentBalance) { alert("الرصيد لا يكفي!"); return; }
            currentBalance += (type === 'deposit' ? amount : -amount);
            document.getElementById('treasury-amount').innerText = "$" + currentBalance.toLocaleString();
            document.getElementById('finance-amount').value = "";
        }

        function checkRankForPass() {
            let rank = document.getElementById('emp-rank').value;
            let needPass = ['قاضي عام', 'قاضي عسكري', 'قاضي فيدرالي', 'نائب وزير العدل', 'وزير العدل'].includes(rank);
            document.getElementById('emp-pass').style.display = needPass ? 'block' : 'none';
        }

        function addEmployee() {
            let name = document.getElementById('emp-name').value;
            let id = document.getElementById('emp-id').value;
            let rank = document.getElementById('emp-rank').value;
            let office = document.getElementById('emp-office').value || "-";
            if(!name || !id) { alert("املأ الحقول الإجبارية (الاسم والهوية)!"); return; }
            
            let row = `<tr><td>${id}</td><td>${name}</td><td>${rank}</td><td>${office}</td><td><button class="btn-action btn-suspend" onclick="this.parentElement.parentElement.remove()">فصل</button></td></tr>`;
            document.getElementById('employees-tbody').insertAdjacentHTML('beforeend', row);
            
            document.getElementById('emp-name').value = "";
            document.getElementById('emp-id').value = "";
            document.getElementById('emp-office').value = "";
            document.getElementById('emp-pass').value = "";
            alert("تم التعيين!");
        }

        function sendTicket() {
            let sender = document.getElementById('ticket-sender').value;
            let msg = document.getElementById('ticket-message').value;
            if(!sender || !msg) { alert("اكتب رسالتك أولاً!"); return; }
            let row = `<tr><td>${sender}</td><td>${msg}</td><td><span style="color:var(--accent-color);">قيد الانتظار</span></td><td><button onclick="this.parentElement.parentElement.remove()">إغلاق التذكرة</button></td></tr>`;
            document.getElementById('tickets-tbody').insertAdjacentHTML('beforeend', row);
            
            document.getElementById('ticket-sender').value = "";
            document.getElementById('ticket-message').value = "";
            alert("تم إرسال تذكرتك بنجاح.");
        }
    </script>
</body>
</html>
