<!DOCTYPE html>
<html lang="ru" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>СККП — Система контроля качества поверхности</title>
    <!-- Tailwind CSS (v4) -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <style>
        /* Дополнительные стили для анимаций и тем */
        @keyframes pulse-red {
            0%, 100% { box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.7); }
            50% { box-shadow: 0 0 0 10px rgba(239, 68, 68, 0); }
        }
        .pulse-alert {
            animation: pulse-red 2s infinite;
        }
        
        @keyframes scan-line {
            0% { transform: translateY(-100%); }
            100% { transform: translateY(100%); }
        }
        .scanner::after {
            content: '';
            position: absolute;
            top: 0; left: 0; right: 0; bottom: 0;
            background: linear-gradient(to bottom, transparent, rgba(34, 197, 94, 0.2), transparent);
            animation: scan-line 3s linear infinite;
        }

        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: rgba(30, 41, 59, 0.3);
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: rgba(148, 163, 184, 0.5);
            border-radius: 3px;
        }
        
        .tab-transition {
            transition: opacity 0.3s ease-out, transform 0.3s ease-out;
        }

        /* Плавные переходы для темы */
        body, header, nav, main, section, div, button, select, input, span, h1, h2, h3, h4, td, th {
            transition: background-color 0.4s ease, border-color 0.4s ease, color 0.4s ease, box-shadow 0.4s ease;
        }
        
        /* Стили для светлой темы */
        html.light body {
            background-color: #f1f5f9 !important;
            color: #1e293b !important;
        }
        html.light #app-container {
            background-color: #f1f5f9 !important;
        }
        html.light header {
            background-color: #ffffff !important;
            border-color: #e2e8f0 !important;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05) !important;
        }
        html.light nav {
            background-color: #ffffff !important;
            border-color: #e2e8f0 !important;
        }
        html.light .nav-btn {
            color: #64748b !important;
        }
        html.light .nav-btn:hover {
            background-color: #f1f5f9 !important;
            color: #0f172a !important;
        }
        html.light .nav-btn.text-blue-400,
        html.light .nav-btn.bg-slate-700\/50 {
            background-color: rgba(37, 99, 235, 0.08) !important;
            color: #2563eb !important;
        }
        html.light .bg-slate-800, 
        html.light section.bg-slate-800,
        html.light div.bg-slate-800\/40 {
            background-color: #ffffff !important;
            border-color: #e2e8f0 !important;
            color: #1e293b !important;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.04) !important;
        }
        html.light .bg-slate-900,
        html.light div.bg-slate-900,
        html.light div.bg-slate-900\/80 {
            background-color: #f8fafc !important;
            color: #1e293b !important;
            border-color: #e2e8f0 !important;
        }
        html.light .bg-slate-950,
        html.light div.bg-slate-950\/80 {
            background-color: #edf2f7 !important;
            border-color: #cbd5e1 !important;
        }
        html.light .text-slate-100 { color: #0f172a !important; }
        html.light .text-slate-200 { color: #1e293b !important; }
        html.light .text-slate-300 { color: #334155 !important; }
        html.light .text-slate-400 { color: #64748b !important; }
        html.light .text-slate-500 { color: #94a3b8 !important; }
        
        html.light .border-slate-700,
        html.light .border-slate-800 { border-color: #e2e8f0 !important; }
        html.light .border-slate-700\/50,
        html.light .border-slate-700\/60 { border-color: rgba(226, 232, 240, 0.8) !important; }
        
        html.light select,
        html.light input {
            background-color: #f8fafc !important;
            color: #0f172a !important;
            border-color: #cbd5e1 !important;
        }
        html.light select:focus,
        html.light input:focus {
            border-color: #2563eb !important;
            background-color: #ffffff !important;
        }

        /* Доп улучшения Хаба */
        html.light #view-hub h3 {
            color: #1e293b !important;
        }
        html.light #view-hub p {
            color: #64748b !important;
        }
        html.light #view-hub div.bg-slate-800:hover {
            background-color: rgba(37, 99, 235, 0.03) !important;
            border-color: rgba(37, 99, 235, 0.3) !important;
        }

        #pass-quality-status {
            display: none;
        }
        
        /* Тексты показателей */
        html.light font-mono {
            color: #0f172a !important;
        }
        html.light span#current-coil-id {
            color: #2563eb !important;
        }
        html.light #auth-screen {
            background-color: #f1f5f9 !important;
        }
        html.light #auth-screen div {
            background-color: #ffffff !important;
            border-color: #e2e8f0 !important;
        }
        html.light #auth-screen h1 {
            color: #0f172a !important;
        }
        html.light #auth-screen label {
            color: #334155 !important;
        }
        html.light #auth-screen input {
            background-color: #f8fafc !important;
            color: #0f172a !important;
            border-color: #cbd5e1 !important;
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 font-sans min-h-screen flex flex-col custom-scrollbar transition-colors duration-300">

    <!-- Звуковое оповещение -->
    <audio id="alert-sound" src="https://assets.mixkit.co/active_storage/sfx/2869/2869-76.wav" preload="auto"></audio>

    <!-- Секция Авторизации -->
    <div id="auth-screen" class="fixed inset-0 flex items-center justify-center bg-slate-950 z-50 transition-all duration-500">
        <div class="bg-slate-800 p-8 rounded-xl border border-slate-700 shadow-2xl w-full max-w-md transform transition-all duration-300 scale-100">
            <div class="text-center mb-8">
                <div class="inline-flex items-center justify-center w-16 h-16 rounded-full bg-blue-600/10 text-blue-400 mb-4 border border-blue-500/30">
                    <i class="fa-solid fa-industry text-3xl"></i>
                </div>
                <h1 class="text-2xl font-bold tracking-wider text-slate-100">СККП</h1>
                <p class="text-xs text-slate-400 mt-1 uppercase tracking-widest">Система контроля качества проката</p>
            </div>
            
            <form id="login-form" class="space-y-6">
                <div>
                    <label class="block text-sm font-medium text-slate-300 mb-1">Имя пользователя</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-500">
                            <i class="fa-solid fa-user"></i>
                        </span>
                        <input type="text" id="login-username" required class="w-full pl-10 pr-4 py-2.5 bg-slate-900/50 border border-slate-700 rounded-lg text-slate-100 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition" placeholder="Введите логин">
                    </div>
                </div>
                
                <div>
                    <label class="block text-sm font-medium text-slate-300 mb-1">Пароль</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-500">
                            <i class="fa-solid fa-lock"></i>
                        </span>
                        <input type="password" id="login-password" required class="w-full pl-10 pr-4 py-2.5 bg-slate-900/50 border border-slate-700 rounded-lg text-slate-100 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition" placeholder="Введите пароль">
                    </div>
                </div>

                <div class="flex items-center justify-between text-sm">
                    <label class="flex items-center space-x-2 cursor-pointer">
                        <input type="checkbox" id="remember-me" class="rounded border-slate-700 text-blue-600 focus:ring-blue-500 bg-slate-900">
                        <span class="text-slate-400 select-none">Запомнить сессию</span>
                    </label>
                    <a href="#" onclick="alert('Обратитесь к администратору системы для сброса пароля.')" class="text-blue-400 hover:text-blue-300 transition">Забыли пароль?</a>
                </div>

                <button type="submit" class="w-full py-3 px-4 bg-blue-600 hover:bg-blue-500 text-white font-semibold rounded-lg shadow-lg shadow-blue-500/20 hover:shadow-blue-500/30 transform active:scale-95 transition-all duration-150">
                    Войти в систему
                </button>
            </form>

            <div class="mt-6 border-t border-slate-700/50 pt-4 text-center text-xs text-slate-500 space-y-1">
                
            </div>
        </div>
    </div>

    <!-- Основной интерфейс приложения (скрыт до логина) -->
    <div id="app-container" class="hidden flex-1 flex flex-col">
        <!-- Верхняя панель -->
        <header class="bg-slate-800 border-b border-slate-700/80 px-6 py-3 flex items-center justify-between shadow-md">
            <div class="flex items-center space-x-3 cursor-pointer" onclick="navigateTo('hub')">
                <i class="fa-solid fa-microscope text-2xl text-blue-400"></i>
                <div>
                    <h2 class="text-lg font-bold tracking-wide text-slate-100">СККП <span class="text-xs text-slate-500 font-normal ml-2">v2.4</span></h2>
                    <p class="text-[10px] text-slate-400 uppercase tracking-wider">Контроль качества проката</p>
                </div>
            </div>

            <!-- Текущие показатели в шапке -->
            <div class="hidden md:flex items-center space-x-6 text-sm">
                <div class="flex items-center space-x-2 bg-slate-900/50 px-3 py-1.5 rounded-lg border border-slate-700/50">
                    <span class="w-2 h-2 rounded-full bg-green-500 animate-pulse"></span>
                    <span class="text-slate-400">Линия:</span>
                    <span id="header-speed" class="font-mono font-bold text-slate-200">12.5 м/с</span>
                </div>
                <div class="flex items-center space-x-2 bg-slate-900/50 px-3 py-1.5 rounded-lg border border-slate-700/50">
                    <span class="text-slate-400">Дефектов за смену:</span>
                    <span id="header-defects-count" class="font-mono font-bold text-yellow-500">0</span>
                </div>
            </div>

            <!-- Профиль пользователя -->
            <div class="flex items-center space-x-4">
                <div class="text-right">
                    <div id="user-full-name" class="text-sm font-semibold text-slate-200">Иванов И.И.</div>
                    <div id="user-role-badge" class="text-xs px-2 py-0.5 rounded bg-blue-500/10 text-blue-400 border border-blue-500/20 font-medium capitalize">Оператор</div>
                </div>
                <div class="relative group">
                    <button id="user-menu-btn" class="w-10 h-10 rounded-full bg-slate-700 border border-slate-600 flex items-center justify-center text-slate-300 hover:text-white hover:border-slate-500 transition cursor-pointer">
                        <i class="fa-solid fa-user-gear text-lg"></i>
                    </button>
                    <!-- Выпадающее меню -->
                    <div class="absolute right-0 mt-2 w-48 bg-slate-800 border border-slate-700 rounded-lg shadow-xl opacity-0 invisible group-hover:opacity-100 group-hover:visible transition-all duration-200 z-50">
                        <a href="#" onclick="navigateTo('settings')" class="block px-4 py-2.5 text-sm text-slate-300 hover:bg-slate-700/50 hover:text-white"><i class="fa-solid fa-cog mr-2"></i> Настройки</a>
                        <hr class="border-slate-700/50">
                        <button onclick="logout()" class="w-full text-left block px-4 py-2.5 text-sm text-rose-400 hover:bg-slate-700/50 hover:text-rose-300"><i class="fa-solid fa-right-from-bracket mr-2"></i> Выход</button>
                    </div>
                </div>
            </div>
        </header>

        <!-- Главное меню (Навигация) -->
        <nav class="bg-slate-800/50 border-b border-slate-700/30 px-6 py-2 overflow-x-auto flex space-x-1 custom-scrollbar">
            <button onclick="navigateTo('hub')" data-nav="hub" class="nav-btn flex items-center space-x-2 px-4 py-2 rounded-lg text-sm font-medium transition text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-house"></i> <span>Главная</span>
            </button>
            <button onclick="navigateTo('dashboard')" data-nav="dashboard" class="nav-btn flex items-center space-x-2 px-4 py-2 rounded-lg text-sm font-medium transition text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-chart-line"></i> <span>Дашборд</span>
            </button>
            <button onclick="navigateTo('archive')" data-nav="archive" class="nav-btn flex items-center space-x-2 px-4 py-2 rounded-lg text-sm font-medium transition text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-box-archive"></i> <span>Архив</span>
            </button>
            <button onclick="navigateTo('passport')" data-nav="passport" class="nav-btn flex items-center space-x-2 px-4 py-2 rounded-lg text-sm font-medium transition text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-id-card"></i> <span>Паспорт рулона</span>
            </button>
            <button onclick="navigateTo('events')" data-nav="events" class="nav-btn flex items-center space-x-2 px-4 py-2 rounded-lg text-sm font-medium transition text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-list-check"></i> <span>События</span>
            </button>
            <button id="nav-admin" onclick="navigateTo('admin')" data-nav="admin" class="nav-btn hidden flex items-center space-x-2 px-4 py-2 rounded-lg text-sm font-medium transition text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-shield-halved"></i> <span>Администрирование</span>
            </button>
            <button onclick="navigateTo('settings')" data-nav="settings" class="nav-btn flex items-center space-x-2 px-4 py-2 rounded-lg text-sm font-medium transition text-slate-400 hover:text-slate-200 hover:bg-slate-800">
                <i class="fa-solid fa-sliders"></i> <span>Настройки</span>
            </button>
        </nav>

        <!-- Рабочая зона для модулей -->
        <main class="flex-1 p-6 overflow-y-auto custom-scrollbar relative">

            <!-- ================= ЦЕНТРАЛЬНОЕ МЕНЮ (ХАБ) ================= -->
            <section id="view-hub" class="view-content tab-transition space-y-6 flex flex-col justify-center items-center py-6">
                <div class="text-center mb-6">
                    <h2 class="text-xl md:text-2xl font-bold text-slate-100 flex items-center justify-center space-x-2">
                        <i class="fa-solid fa-industry text-blue-400"></i>
                        <span>Главная панель СККП</span>
                    </h2>
                    <p class="text-xs md:text-sm text-slate-400 mt-1">Выберите необходимый модуль контроля металлопроката</p>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 max-w-4xl w-full px-4">
                    <!-- Дашборд -->
                    <div onclick="navigateTo('dashboard')" class="bg-slate-800 hover:bg-slate-700/80 border border-slate-700/50 hover:border-blue-500 rounded-xl p-6 flex flex-col items-center justify-center text-center cursor-pointer transition-all duration-300 transform hover:-translate-y-1 hover:shadow-xl group">
                        <div class="w-16 h-16 rounded-full bg-blue-500/10 text-blue-400 flex items-center justify-center text-2xl mb-4 border border-blue-500/20 group-hover:bg-blue-500 group-hover:text-white transition-all duration-300">
                            <i class="fa-solid fa-chart-line"></i>
                        </div>
                        <h3 class="text-base font-semibold text-slate-200">Дашборд</h3>
                        <p class="text-[11px] text-slate-400 mt-2 max-w-xs leading-relaxed">Мониторинг параметров линии и оперативный контроль обнаруженных дефектов</p>
                    </div>

                    <!-- Архив дефектов -->
                    <div onclick="navigateTo('archive')" class="bg-slate-800 hover:bg-slate-700/80 border border-slate-700/50 hover:border-blue-500 rounded-xl p-6 flex flex-col items-center justify-center text-center cursor-pointer transition-all duration-300 transform hover:-translate-y-1 hover:shadow-xl group">
                        <div class="w-16 h-16 rounded-full bg-indigo-500/10 text-indigo-400 flex items-center justify-center text-2xl mb-4 border border-indigo-500/20 group-hover:bg-indigo-500 group-hover:text-white transition-all duration-300">
                            <i class="fa-solid fa-box-archive"></i>
                        </div>
                        <h3 class="text-base font-semibold text-slate-200">Архив дефектов</h3>
                        <p class="text-[11px] text-slate-400 mt-2 max-w-xs leading-relaxed">Электронный журнал дефектов с фильтрацией, сортировкой и экспортом</p>
                    </div>

                    <!-- Паспорт рулона -->
                    <div onclick="navigateTo('passport')" class="bg-slate-800 hover:bg-slate-700/80 border border-slate-700/50 hover:border-blue-500 rounded-xl p-6 flex flex-col items-center justify-center text-center cursor-pointer transition-all duration-300 transform hover:-translate-y-1 hover:shadow-xl group">
                        <div class="w-16 h-16 rounded-full bg-emerald-500/10 text-emerald-400 flex items-center justify-center text-2xl mb-4 border border-emerald-500/20 group-hover:bg-emerald-500 group-hover:text-white transition-all duration-300">
                            <i class="fa-solid fa-id-card"></i>
                        </div>
                        <h3 class="text-base font-semibold text-slate-200">Паспорт рулона</h3>
                        <p class="text-[11px] text-slate-400 mt-2 max-w-xs leading-relaxed">Детальная карточка рулона, SVG-развёртка полосы и графики сортности</p>
                    </div>

                    <!-- Журнал событий -->
                    <div onclick="navigateTo('events')" class="bg-slate-800 hover:bg-slate-700/80 border border-slate-700/50 hover:border-blue-500 rounded-xl p-6 flex flex-col items-center justify-center text-center cursor-pointer transition-all duration-300 transform hover:-translate-y-1 hover:shadow-xl group">
                        <div class="w-16 h-16 rounded-full bg-amber-500/10 text-amber-400 flex items-center justify-center text-2xl mb-4 border border-amber-500/20 group-hover:bg-amber-500 group-hover:text-white transition-all duration-300">
                            <i class="fa-solid fa-list-check"></i>
                        </div>
                        <h3 class="text-base font-semibold text-slate-200">Журнал событий</h3>
                        <p class="text-[11px] text-slate-400 mt-2 max-w-xs leading-relaxed">Системный аудит действий пользователей, сбоев оборудования и лог-уведомлений</p>
                    </div>

                    <!-- Администрирование -->
                    <div id="hub-admin" onclick="navigateTo('admin')" class="bg-slate-800 hover:bg-slate-700/80 border border-slate-700/50 hover:border-blue-500 rounded-xl p-6 hidden flex-col items-center justify-center text-center cursor-pointer transition-all duration-300 transform hover:-translate-y-1 hover:shadow-xl group">
                        <div class="w-16 h-16 rounded-full bg-rose-500/10 text-rose-400 flex items-center justify-center text-2xl mb-4 border border-rose-500/20 group-hover:bg-rose-500 group-hover:text-white transition-all duration-300">
                            <i class="fa-solid fa-shield-halved"></i>
                        </div>
                        <h3 class="text-base font-semibold text-slate-200">Администрирование</h3>
                        <p class="text-[11px] text-slate-400 mt-2 max-w-xs leading-relaxed">Управление пользователями, калибровка датчиков и контроль БД</p>
                    </div>

                    <!-- Настройки -->
                    <div onclick="navigateTo('settings')" class="bg-slate-800 hover:bg-slate-700/80 border border-slate-700/50 hover:border-blue-500 rounded-xl p-6 flex flex-col items-center justify-center text-center cursor-pointer transition-all duration-300 transform hover:-translate-y-1 hover:shadow-xl group">
                        <div class="w-16 h-16 rounded-full bg-slate-500/10 text-slate-400 flex items-center justify-center text-2xl mb-4 border border-slate-500/20 group-hover:bg-slate-500 group-hover:text-white transition-all duration-300">
                            <i class="fa-solid fa-sliders"></i>
                        </div>
                        <h3 class="text-base font-semibold text-slate-200">Настройки</h3>
                        <p class="text-[11px] text-slate-400 mt-2 max-w-xs leading-relaxed">Кастомизация внешнего вида (Тёмная/Светлая темы) и контакты саппорта</p>
                    </div>
                </div>
            </section>

            <!-- ================= ДАШБОРД ================= -->
             <section id="view-dashboard" class="view-content tab-transition space-y-6 hidden opacity-0 scale-95">
                <!-- Сетка показателей оборудования -->
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
                    <!-- Скорость -->
                    <div class="bg-slate-800/40 backdrop-blur-md p-5 rounded-2xl border border-slate-700/50 shadow-xl hover:border-blue-500/50 hover:shadow-blue-500/10 transition-all duration-300 group flex items-center justify-between">
                        <div>
                            <span class="text-xs font-semibold text-slate-400 uppercase tracking-widest">Скорость проката</span>
                            <div class="text-3xl font-bold font-mono text-slate-100 mt-2 flex items-baseline space-x-1" id="stat-speed">
                                <span>12.4</span> <span class="text-sm text-slate-500 font-sans">м/с</span>
                            </div>
                        </div>
                        <div class="p-4 bg-gradient-to-br from-blue-500/20 to-blue-600/5 text-blue-400 rounded-xl group-hover:scale-110 group-hover:text-blue-300 transition-all duration-300 border border-blue-500/20">
                            <i class="fa-solid fa-gauge-high text-2xl"></i>
                        </div>
                    </div>
                    
                    <!-- Температура -->
                    <div class="bg-slate-800/40 backdrop-blur-md p-5 rounded-2xl border border-slate-700/50 shadow-xl hover:border-orange-500/50 hover:shadow-orange-500/10 transition-all duration-300 group flex items-center justify-between">
                        <div>
                            <span class="text-xs font-semibold text-slate-400 uppercase tracking-widest">Температура полосы</span>
                            <div class="text-3xl font-bold font-mono text-slate-100 mt-2 flex items-baseline space-x-1" id="stat-temp">
                                <span>845</span> <span class="text-sm text-slate-500 font-sans">°C</span>
                            </div>
                        </div>
                        <div class="p-4 bg-gradient-to-br from-orange-500/20 to-orange-600/5 text-orange-400 rounded-xl group-hover:scale-110 group-hover:text-orange-300 transition-all duration-300 border border-orange-500/20">
                            <i class="fa-solid fa-temperature-high text-2xl"></i>
                        </div>
                    </div>

                    <!-- Толщина -->
                    <div class="bg-slate-800/40 backdrop-blur-md p-5 rounded-2xl border border-slate-700/50 shadow-xl hover:border-emerald-500/50 hover:shadow-emerald-500/10 transition-all duration-300 group flex items-center justify-between">
                        <div>
                            <span class="text-xs font-semibold text-slate-400 uppercase tracking-widest">Толщина проката</span>
                            <div class="text-3xl font-bold font-mono text-slate-100 mt-2 flex items-baseline space-x-1" id="stat-thickness">
                                <span>2.54</span> <span class="text-sm text-slate-500 font-sans">мм</span>
                            </div>
                        </div>
                        <div class="p-4 bg-gradient-to-br from-emerald-500/20 to-emerald-600/5 text-emerald-400 rounded-xl group-hover:scale-110 group-hover:text-emerald-300 transition-all duration-300 border border-emerald-500/20">
                            <i class="fa-solid fa-arrows-up-down text-2xl"></i>
                        </div>
                    </div>

                    <!-- Ширина -->
                    <div class="bg-slate-800/40 backdrop-blur-md p-5 rounded-2xl border border-slate-700/50 shadow-xl hover:border-purple-500/50 hover:shadow-purple-500/10 transition-all duration-300 group flex items-center justify-between">
                        <div>
                            <span class="text-xs font-semibold text-slate-400 uppercase tracking-widest">Ширина полосы</span>
                            <div class="text-3xl font-bold font-mono text-slate-100 mt-2 flex items-baseline space-x-1" id="stat-width">
                                <span>1250</span> <span class="text-sm text-slate-500 font-sans">мм</span>
                            </div>
                        </div>
                        <div class="p-4 bg-gradient-to-br from-purple-500/20 to-purple-600/5 text-purple-400 rounded-xl group-hover:scale-110 group-hover:text-purple-300 transition-all duration-300 border border-purple-500/20">
                            <i class="fa-solid fa-arrows-left-right text-2xl"></i>
                        </div>
                    </div>
                </div>

                <!-- Визуализатор рулона (Canvas) -->
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                    <div class="bg-slate-800/40 backdrop-blur-md p-6 rounded-2xl border border-slate-700/50 shadow-xl lg:col-span-2 flex flex-col">
                        <div class="flex items-center justify-between mb-4 flex-wrap gap-2">
                            <h3 class="text-lg font-bold text-slate-200 flex items-center space-x-3">
                                <span class="flex h-3 w-3 relative">
                                    <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-blue-400 opacity-75"></span>
                                    <span class="relative inline-flex rounded-full h-3 w-3 bg-blue-500"></span>
                                </span>
                                <span>Текущий рулон: <span id="current-coil-id" class="text-blue-400 font-mono">#N-340912</span></span>
                            </h3>
                            <div class="flex space-x-2 text-xs">
                                <span class="px-3 py-1.5 bg-slate-900/80 border border-slate-700/60 rounded-lg text-slate-400 flex items-center">
                                    Марка: <b class="text-slate-200 ml-1 font-semibold">08Ю</b>
                                </span>
                                <span class="px-3 py-1.5 bg-slate-900/80 border border-slate-700/60 rounded-lg text-slate-400 flex items-center">
                                    Длина: <b class="text-slate-200 ml-1 font-semibold">1200 м</b>
                                </span>
                            </div>
                        </div>
                        
                        <!-- Окно сканера Canvas -->
                        <div class="relative bg-slate-950/80 border border-slate-800 rounded-2xl p-4 flex-1 flex flex-col justify-center scanner overflow-hidden shadow-inner">
                            <canvas id="coil-canvas" class="w-full h-48 bg-slate-900 rounded-xl cursor-crosshair shadow-2xl"></canvas>
                            <div class="flex justify-between mt-3 text-xs text-slate-400 font-mono tracking-wider">
                                <span class="flex items-center"><i class="fa-solid fa-hourglass-start mr-1 text-slate-500"></i> 0 м (Старт)</span>
                                <span class="px-2 py-0.5 bg-blue-500/20 text-blue-400 border border-blue-500/20 rounded-md font-bold">Размотка: <span id="coil-progress">35%</span></span>
                                <span class="flex items-center">1200 м (Финиш) <i class="fa-solid fa-hourglass-end ml-1 text-slate-500"></i></span>
                            </div>
                        </div>
                    </div>

                    <!-- Лента последних дефектов -->
                    <div class="bg-slate-800/40 backdrop-blur-md p-6 rounded-2xl border border-slate-700/50 shadow-xl flex flex-col h-[380px]">
                        <h3 class="text-base font-bold text-slate-100 mb-4 flex items-center justify-between">
                            <span class="flex items-center space-x-2">
                                <i class="fa-regular fa-bell text-yellow-500 animate-swing"></i>
                                <span>Оперативные события</span>
                            </span>
                            <span class="text-2xs px-2 py-0.5 bg-slate-900 border border-slate-700/50 text-slate-400 rounded-full font-mono">Live</span>
                        </h3>
                        <div id="live-defect-stream" class="flex-1 overflow-y-auto custom-scrollbar space-y-3 pr-1">
                            <!-- Подгружается JS -->
                        </div>
                    </div>
                </div>

<!-- Скрытый блок управления (доступен через API) -->
                <div class="hidden bg-slate-800 p-4 rounded-xl border border-slate-700/80 flex-wrap gap-4 items-center justify-between">
                    <div class="flex items-center space-x-4">
                        <span class="text-sm font-medium text-slate-400">Управление симулятором:</span>
                        <button onclick="toggleSimulation()" id="btn-sim-toggle" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-500 text-white text-sm font-medium rounded-lg shadow-sm transition active:scale-95 cursor-pointer">
                            <i class="fa-solid fa-play mr-1"></i> Запустить линию
                        </button>
                        <button onclick="triggerManualDefect()" class="px-4 py-2 bg-slate-700 hover:bg-slate-600 text-slate-200 text-sm font-medium rounded-lg border border-slate-600 transition active:scale-95 cursor-pointer">
                            <i class="fa-solid fa-bolt text-yellow-400 mr-1"></i> Симулировать дефект
                        </button>
                    </div>
                </div>
            </section>

            <!-- ================= АРХИВ ДЕФЕКТОВ ================= -->
            <section id="view-archive" class="view-content tab-transition hidden space-y-6">
                <div class="bg-slate-800 p-6 rounded-xl border border-slate-700/80">
                    <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-6">
                        <h2 class="text-xl font-bold text-slate-100 flex items-center space-x-2">
                            <i class="fa-solid fa-box-archive text-blue-400"></i>
                            <span>Электронный архив дефектов</span>
                        </h2>
                        <!-- Кнопки экспорта -->
                        <div class="flex space-x-2">
                            <button onclick="exportToCSV()" class="px-4 py-2 bg-slate-700 hover:bg-slate-600 text-slate-200 text-sm font-medium rounded-lg border border-slate-600 transition flex items-center cursor-pointer">
                                <i class="fa-solid fa-file-csv mr-2 text-green-400"></i> Экспорт CSV
                            </button>
                            <button onclick="printArchive()" class="px-4 py-2 bg-slate-700 hover:bg-slate-600 text-slate-200 text-sm font-medium rounded-lg border border-slate-600 transition flex items-center cursor-pointer">
                                <i class="fa-solid fa-print mr-2 text-blue-400"></i> Печать
                            </button>
                        </div>
                    </div>

                    <!-- Панель фильтров -->
                    <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6 p-4 bg-slate-900/50 rounded-lg border border-slate-700/50">
                        <div>
                            <label class="block text-xs font-semibold text-slate-400 uppercase mb-1">Поиск / Рулон</label>
                            <input type="text" id="filter-search" oninput="applyFilters()" placeholder="ID рулона..." class="w-full px-3 py-2 bg-slate-800 border border-slate-700 rounded-lg text-slate-200 text-sm focus:outline-none focus:border-blue-500">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-400 uppercase mb-1">Тип дефекта</label>
                            <select id="filter-type" onchange="applyFilters()" class="w-full px-3 py-2 bg-slate-800 border border-slate-700 rounded-lg text-slate-200 text-sm focus:outline-none focus:border-blue-500">
                                <option value="all">Все типы</option>
                                <option value="Царапина">Царапина</option>
                                <option value="Вмятина">Вмятина</option>
                                <option value="Раковина">Раковина</option>
                                <option value="Неметаллическое включение">Неметаллическое включение</option>
                                <option value="Разнотолщинность">Разнотолщинность</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-400 uppercase mb-1">Статус</label>
                            <select id="filter-status" onchange="applyFilters()" class="w-full px-3 py-2 bg-slate-800 border border-slate-700 rounded-lg text-slate-200 text-sm focus:outline-none focus:border-blue-500">
                                <option value="all">Все статусы</option>
                                <option value="Критический">Критический</option>
                                <option value="Существенный">Существенный</option>
                                <option value="Допустимый">Допустимый</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-400 uppercase mb-1">Сортировка</label>
                            <select id="filter-sort" onchange="applyFilters()" class="w-full px-3 py-2 bg-slate-800 border border-slate-700 rounded-lg text-slate-200 text-sm focus:outline-none focus:border-blue-500">
                                <option value="date_desc">Сначала новые</option>
                                <option value="date_asc">Сначала старые</option>
                                <option value="coil_id">По ID рулона</option>
                            </select>
                        </div>
                    </div>

                    <!-- Таблица архива -->
                    <div class="overflow-x-auto custom-scrollbar border border-slate-700/80 rounded-lg">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr class="bg-slate-900 text-slate-300 text-xs font-semibold uppercase border-b border-slate-700">
                                    <th class="p-4">ID дефекта</th>
                                    <th class="p-4">Рулон</th>
                                    <th class="p-4">Тип</th>
                                    <th class="p-4">Координата</th>
                                    <th class="p-4">Статус</th>
                                    <th class="p-4">Дата / Время</th>
                                    <th class="p-4 text-right">Действие</th>
                                </tr>
                            </thead>
                            <tbody id="archive-table-body" class="divide-y divide-slate-700/50 text-sm text-slate-300">
                                <!-- Рендерится JS -->
                            </tbody>
                        </table>
                    </div>

                    <!-- Пагинация -->
                    <div class="flex items-center justify-between mt-4">
                        <div class="text-xs text-slate-400">
                            Всего найдено: <span id="archive-total-records" class="font-bold text-slate-200">0</span>
                        </div>
                        <div class="flex space-x-1" id="archive-pagination">
                            <!-- Кнопки JS -->
                        </div>
                    </div>
                </div>
            </section>

            <!-- ================= ПАСПОРТ РУЛОНА ================= -->
            <section id="view-passport" class="view-content tab-transition hidden space-y-6">
                <div class="bg-slate-800 p-6 rounded-xl border border-slate-700/80">
                    <div class="flex items-center justify-between mb-6">
                        <h2 class="text-xl font-bold text-slate-100 flex items-center space-x-2">
                            <i class="fa-solid fa-id-card text-blue-400"></i>
                            <span>Паспорт рулона</span>
                        </h2>
                        <!-- Поиск рулона -->
                        <div class="flex space-x-2">
                            <select id="passport-coil-select" onchange="renderPassport()" class="px-3 py-2 bg-slate-900 border border-slate-700 rounded-lg text-slate-200 text-sm focus:outline-none focus:border-blue-500">
                                <!-- Загружается JS -->
                            </select>
                        </div>
                    </div>

                    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                        <!-- Метаданные рулона -->
                        <div class="bg-slate-900/50 p-5 rounded-xl border border-slate-700/50 space-y-4">
                            <h3 class="text-sm font-semibold uppercase text-slate-400 border-b border-slate-700 pb-2">Основные характеристики</h3>
                            <div class="grid grid-cols-2 gap-x-4 gap-y-3 text-sm">
                                <div>
                                    <span class="text-xs text-slate-500 block">ID рулона</span>
                                    <span class="font-semibold text-slate-200" id="pass-id">-</span>
                                </div>
                                <div>
                                    <span class="text-xs text-slate-500 block">Марка стали</span>
                                    <span class="font-semibold text-slate-200" id="pass-grade">-</span>
                                </div>
                                <div>
                                    <span class="text-xs text-slate-500 block">Длина</span>
                                    <span class="font-semibold text-slate-200" id="pass-length">-</span>
                                </div>
                                <div>
                                    <span class="text-xs text-slate-500 block">Толщина / Ширина</span>
                                    <span class="font-semibold text-slate-200" id="pass-dims">-</span>
                                </div>
                                <div>
                                    <span class="text-xs text-slate-500 block">Дата производства</span>
                                    <span class="font-semibold text-slate-200" id="pass-date">-</span>
                                </div>
                                <div>
                                    <span class="text-xs text-slate-500 block">Всего дефектов</span>
                                    <span class="font-semibold text-yellow-500" id="pass-total-def">-</span>
                                </div>
                            </div>
                            <!-- Качество / Сортность -->
                            <div class="mt-6 pt-4 border-t border-slate-700">
                                <span class="text-xs text-slate-500 block mb-1"></span>
                                <div id="pass-quality-status" class="px-3 py-2 text-center rounded-lg font-bold text-sm bg-emerald-500/10 text-emerald-400 border border-emerald-500/20">
                                    
                                </div>
                            </div>
                        </div>

                        <!-- Диаграмма качества -->
                        <div class="bg-slate-900/50 p-5 rounded-xl border border-slate-700/50 flex flex-col justify-center items-center relative h-[250px] lg:h-auto">
                            <h3 class="text-sm font-semibold uppercase text-slate-400 self-start mb-2">Статистика дефектов</h3>
                            <div class="w-48 h-48 relative">
                                <canvas id="passport-chart"></canvas>
                            </div>
                        </div>

                        <!-- Аналитика (Столбцы) -->
                        <div class="bg-slate-900/50 p-5 rounded-xl border border-slate-700/50 flex flex-col justify-center items-center relative h-[250px] lg:h-auto">
                            <h3 class="text-sm font-semibold uppercase text-slate-400 self-start mb-2">Распределение по критичности</h3>
                            <div class="w-full h-40">
                                <canvas id="passport-bar-chart"></canvas>
                            </div>
                        </div>
                    </div>

                    <!-- SVG Карта расположения дефектов -->
                    <div class="mt-6">
                        <h3 class="text-sm font-semibold uppercase text-slate-400 mb-2">SVG-Карта дефектов (Развертка полосы)</h3>
                        <div class="bg-slate-950 p-4 border border-slate-800 rounded-xl relative overflow-x-auto custom-scrollbar">
                            <div class="min-w-[600px] relative">
                                <svg id="passport-svg" viewBox="0 0 1000 150" class="w-full h-24 bg-slate-900/50 rounded-md border border-slate-800">
                                    <!-- Отрисовка SVG JS -->
                                </svg>
                                <div class="flex justify-between text-slate-500 font-mono text-[10px] mt-1 px-1">
                                    <span>0 м</span>
                                    <span>Ширина: 1250 мм</span>
                                    <span>Конец рулона</span>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Галерея снимков -->
                    <div class="mt-6">
                        <h3 class="text-sm font-semibold uppercase text-slate-400 mb-3">Снимки дефектов (Лайтбокс)</h3>
                        <div class="grid grid-cols-2 sm:grid-cols-4 md:grid-cols-6 gap-4" id="passport-gallery">
                            <!-- Фотографии JS -->
                        </div>
                    </div>
                </div>
            </section>

            <!-- ================= ЖУРНАЛ СОБЫТИЙ ================= -->
            <section id="view-events" class="view-content tab-transition hidden space-y-6">
                <div class="bg-slate-800 p-6 rounded-xl border border-slate-700/80">
                    <div class="flex items-center justify-between mb-6">
                        <h2 class="text-xl font-bold text-slate-100 flex items-center space-x-2">
                            <i class="fa-solid fa-list-check text-blue-400"></i>
                            <span>Системный журнал событий</span>
                        </h2>
                        <!-- Опции уведомлений -->
                        <div class="flex items-center space-x-4 text-sm bg-slate-900/50 px-3 py-1.5 rounded-lg border border-slate-700/50">
                            <label class="flex items-center space-x-2 cursor-pointer">
                                <input type="checkbox" id="setting-notify-popup" checked class="rounded bg-slate-800 text-blue-600 focus:ring-blue-500 border-slate-700">
                                <span class="text-slate-400">Всплывающие</span>
                            </label>
                            <label class="flex items-center space-x-2 cursor-pointer">
                                <input type="checkbox" id="setting-notify-sound" checked class="rounded bg-slate-800 text-blue-600 focus:ring-blue-500 border-slate-700">
                                <span class="text-slate-400">Звук</span>
                            </label>
                        </div>
                    </div>

                    <!-- Фильтры лога -->
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
                        <div>
                            <select id="log-filter-level" onchange="renderLog()" class="w-full px-3 py-2 bg-slate-900 border border-slate-700 rounded-lg text-slate-200 text-sm focus:outline-none focus:border-blue-500">
                                <option value="all">Все уровни важности</option>
                                <option value="INFO">INFO (Информация)</option>
                                <option value="WARN">WARN (Предупреждение)</option>
                                <option value="ERROR">ERROR (Критическая ошибка)</option>
                            </select>
                        </div>
                        <div class="relative">
                            <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-500">
                                <i class="fa-solid fa-magnifying-glass"></i>
                            </span>
                            <input type="text" id="log-filter-search" oninput="renderLog()" placeholder="Поиск по тексту события..." class="w-full pl-10 pr-3 py-2 bg-slate-900 border border-slate-700 rounded-lg text-slate-200 text-sm focus:outline-none focus:border-blue-500">
                        </div>
                        <button onclick="clearLog()" id="btn-clear-log" class="hidden px-4 py-2 bg-slate-700/50 hover:bg-rose-900/30 text-rose-400 border border-rose-500/20 text-sm font-medium rounded-lg transition active:scale-95 cursor-pointer">
                            <i class="fa-solid fa-trash-can mr-2"></i> Очистить журнал (Admin)
                        </button>
                    </div>

                    <!-- Лог-панель -->
                    <div class="bg-slate-950 p-4 border border-slate-800 rounded-xl">
                        <div id="log-container" class="h-96 overflow-y-auto custom-scrollbar font-mono text-xs space-y-2 p-1">
                            <!-- Заполняется JS -->
                        </div>
                    </div>
                </div>
            </section>

            <!-- ================= АДМИНИСТРИРОВАНИЕ ================= -->
            <section id="view-admin" class="view-content tab-transition hidden space-y-6">
                <!-- Управление пользователями -->
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                    <div class="bg-slate-800 p-6 rounded-xl border border-slate-700/80 lg:col-span-2">
                        <h3 class="text-lg font-bold text-slate-100 mb-4 flex items-center space-x-2">
                            <i class="fa-solid fa-users text-blue-400"></i>
                            <span>Управление пользователями</span>
                        </h3>
                        <div class="overflow-x-auto border border-slate-700/80 rounded-lg mb-4">
                            <table class="w-full text-left border-collapse">
                                <thead>
                                    <tr class="bg-slate-900 text-slate-300 text-xs font-semibold uppercase border-b border-slate-700">
                                        <th class="p-4">ФИО</th>
                                        <th class="p-4">Логин</th>
                                        <th class="p-4">Роль</th>
                                        <th class="p-4 text-right">Удалить</th>
                                    </tr>
                                </thead>
                                <tbody id="user-table-body" class="divide-y divide-slate-700/50 text-sm text-slate-300">
                                    <!-- Загружается JS -->
                                </tbody>
                            </table>
                        </div>

                        <!-- Добавление нового -->
                        <form id="admin-add-user-form" class="bg-slate-900/50 p-4 rounded-lg border border-slate-700/50 flex flex-wrap gap-4 items-end">
                            <div class="flex-1 min-w-[150px]">
                                <label class="block text-xs font-semibold text-slate-400 uppercase mb-1">ФИО</label>
                                <input type="text" id="add-user-fio" required placeholder="Петров П.П." class="w-full px-3 py-2 bg-slate-800 border border-slate-700 rounded-lg text-slate-200 text-sm">
                            </div>
                            <div class="flex-1 min-w-[150px]">
                                <label class="block text-xs font-semibold text-slate-400 uppercase mb-1">Логин</label>
                                <input type="text" id="add-user-login" required placeholder="petrov" class="w-full px-3 py-2 bg-slate-800 border border-slate-700 rounded-lg text-slate-200 text-sm">
                            </div>
                            <div class="flex-1 min-w-[150px]">
                                <label class="block text-xs font-semibold text-slate-400 uppercase mb-1">Пароль</label>
                                <input type="password" id="add-user-pass" required placeholder="••••••" class="w-full px-3 py-2 bg-slate-800 border border-slate-700 rounded-lg text-slate-200 text-sm">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-slate-400 uppercase mb-1">Роль</label>
                                <select id="add-user-role" class="w-full px-3 py-2 bg-slate-800 border border-slate-700 rounded-lg text-slate-200 text-sm">
                                    <option value="operator">Оператор</option>
                                    <option value="admin">Администратор</option>
                                </select>
                            </div>
                            <button type="submit" class="px-4 py-2.5 bg-blue-600 hover:bg-blue-500 text-white font-medium rounded-lg text-sm shadow cursor-pointer transition">Создать</button>
                        </form>
                    </div>

                    <!-- Мониторинг оборудования -->
                    <div class="bg-slate-800 p-6 rounded-xl border border-slate-700/80">
                        <h3 class="text-lg font-bold text-slate-100 mb-4 flex items-center space-x-2">
                            <i class="fa-solid fa-server text-blue-400"></i>
                            <span>Оборудование</span>
                        </h3>
                        <div class="space-y-4 text-sm">
                            <div class="flex items-center justify-between p-3 bg-slate-900 rounded-lg border border-slate-700/50">
                                <span class="text-slate-300">Камера-детектор 1 (Верх)</span>
                                <span class="flex items-center text-emerald-400 font-medium"><span class="w-2.5 h-2.5 rounded-full bg-emerald-500 mr-2 animate-pulse"></span> ONLINE</span>
                            </div>
                            <div class="flex items-center justify-between p-3 bg-slate-900 rounded-lg border border-slate-700/50">
                                <span class="text-slate-300">Камера-детектор 2 (Низ)</span>
                                <span class="flex items-center text-emerald-400 font-medium"><span class="w-2.5 h-2.5 rounded-full bg-emerald-500 mr-2 animate-pulse"></span> ONLINE</span>
                            </div>
                            <div class="flex items-center justify-between p-3 bg-slate-900 rounded-lg border border-slate-700/50">
                                <span class="text-slate-300">Сервер обработки ML</span>
                                <span class="flex items-center text-emerald-400 font-medium"><span class="w-2.5 h-2.5 rounded-full bg-emerald-500 mr-2 animate-pulse"></span> ONLINE</span>
                            </div>
                            <div class="flex items-center justify-between p-3 bg-slate-900 rounded-lg border border-slate-700/50">
                                <span class="text-slate-300">База данных СККП</span>
                                <span class="flex items-center text-emerald-400 font-medium"><span class="w-2.5 h-2.5 rounded-full bg-emerald-500 mr-2 animate-pulse"></span> ONLINE</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Пороговые значения и Глубина архива -->
                <div class="bg-slate-800 p-6 rounded-xl border border-slate-700/80 grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                        <h3 class="text-base font-bold text-slate-100 mb-3 flex items-center space-x-2">
                            <i class="fa-solid fa-sliders text-blue-400"></i>
                            <span>Параметры тревог и порогов</span>
                        </h3>
                        <div class="space-y-4 bg-slate-900/50 p-4 rounded-lg border border-slate-700/50">
                            <div>
                                <label class="flex justify-between text-xs font-semibold text-slate-400 uppercase mb-1">
                                    <span>Макс. скорость проката (м/с)</span>
                                    <span id="label-thr-speed" class="text-slate-200">15.0</span>
                                </label>
                                <input type="range" id="thr-speed" min="5" max="25" step="0.5" value="15" oninput="updateAdminThr()" class="w-full accent-blue-500 bg-slate-800 rounded">
                            </div>
                            <div>
                                <label class="flex justify-between text-xs font-semibold text-slate-400 uppercase mb-1">
                                    <span>Крит. температура (°C)</span>
                                    <span id="label-thr-temp" class="text-slate-200">900</span>
                                </label>
                                <input type="range" id="thr-temp" min="700" max="1100" step="10" value="900" oninput="updateAdminThr()" class="w-full accent-blue-500 bg-slate-800 rounded">
                            </div>
                        </div>
                    </div>

                    <div>
                        <h3 class="text-base font-bold text-slate-100 mb-3 flex items-center space-x-2">
                            <i class="fa-solid fa-database text-blue-400"></i>
                            <span>Очистка & Квота хранилища</span>
                        </h3>
                        <div class="bg-slate-900/50 p-4 rounded-lg border border-slate-700/50 flex flex-col justify-between h-[124px]">
                            <div class="flex items-center justify-between text-sm">
                                <span class="text-slate-400">Глубина архива</span>
                                <select id="setting-archive-depth" onchange="updateAdminThr()" class="px-2 py-1 bg-slate-800 border border-slate-700 rounded text-slate-200">
                                    <option value="30">30 дней</option>
                                    <option value="90">90 дней</option>
                                    <option value="180">180 дней</option>
                                </select>
                            </div>
                            <button onclick="resetAllData()" class="w-full py-2 bg-rose-600 hover:bg-rose-500 text-white font-medium text-xs rounded-lg transition active:scale-95 cursor-pointer flex items-center justify-center">
                                <i class="fa-solid fa-triangle-exclamation mr-2"></i> Сбросить ВСЕ данные до заводских
                            </button>
                        </div>
                    </div>
                </div>
            </section>

            <!-- ================= НАСТРОЙКИ ================= -->
            <section id="view-settings" class="view-content tab-transition hidden space-y-6">
                <div class="bg-slate-800 p-6 rounded-xl border border-slate-700/80 max-w-2xl mx-auto">
                    <h2 class="text-xl font-bold text-slate-100 mb-6 flex items-center space-x-2">
                        <i class="fa-solid fa-cog text-blue-400"></i>
                        <span>Настройки системы</span>
                    </h2>

                    <div class="space-y-6">
                        <!-- Тема оформления -->
                        <div class="flex items-center justify-between p-4 bg-slate-900/50 rounded-lg border border-slate-700/50">
                            <div>
                                <span class="text-sm font-semibold text-slate-200">Тема оформления интерфейса</span>
                                <p class="text-xs text-slate-500 mt-0.5">Выберите комфортную цветовую схему для работы.</p>
                            </div>
                            <div class="flex bg-slate-800 p-1 border border-slate-700 rounded-lg">
                                <button onclick="setTheme('dark')" id="theme-dark-btn" class="px-3 py-1.5 text-xs font-semibold rounded bg-slate-700 text-slate-100 cursor-pointer">Тёмная</button>
                                <button onclick="setTheme('light')" id="theme-light-btn" class="px-3 py-1.5 text-xs font-semibold rounded text-slate-400 cursor-pointer">Светлая</button>
                            </div>
                        </div>

                        <!-- О системе -->
                        <div class="p-4 bg-slate-900/50 rounded-lg border border-slate-700/50 space-y-3">
                            <span class="text-sm font-semibold text-slate-200 block border-b border-slate-700 pb-1">Информация о ПО</span>
                            <div class="grid grid-cols-2 gap-y-2 text-xs">
                                <span class="text-slate-400">Разработка:</span>
                                <span class="text-slate-200">ПАО «НЛМК» / Отдел ПромАвтоматики</span>
                                <span class="text-slate-400">Лицензия:</span>
                                <span class="text-slate-200">Корпоративная (Enterprise)</span>
                                <span class="text-slate-400">Версия сборки:</span>
                                <span class="text-slate-200 font-mono">2.4.0-rev2026</span>
                            </div>
                        </div>

                        <!-- Контакты -->
                        <div class="p-4 bg-slate-900/50 rounded-lg border border-slate-700/50 flex items-center space-x-4">
                            <div class="text-3xl text-blue-400/80"><i class="fa-solid fa-headset"></i></div>
                            <div>
                                <span class="text-sm font-semibold text-slate-200">Техническая поддержка</span>
                                <p class="text-xs text-slate-400 mt-1">Внутренний телефон: <span class="font-mono text-slate-200">3-14-15</span></p>
                                <p class="text-xs text-slate-400">Email: <span class="font-mono text-slate-200">support_tech@company.ru</span></p>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

        </main>
    </div>

    <!-- Модальное окно (Лайтбокс галереи) -->
    <div id="lightbox-modal" class="fixed inset-0 bg-black/90 z-[100] hidden items-center justify-center p-4" onclick="closeLightbox()">
        <button class="absolute top-4 right-4 text-slate-400 hover:text-white text-3xl cursor-pointer"><i class="fa-solid fa-xmark"></i></button>
        <img id="lightbox-img" src="" class="max-h-[90vh] max-w-[90vw] object-contain rounded border border-slate-800 shadow-2xl">
    </div>

    <!-- Тосты всплывающих уведомлений -->
    <div id="toast-container" class="fixed bottom-4 right-4 z-50 space-y-2 max-w-sm"></div>


    <!-- ======================================================= -->
    <!-- JAVASCRIPT ЛОГИКА СИСТЕМЫ -->
    <!-- ======================================================= -->
    <script>
        // --- 1. ШАБЛОНЫ И СТРУКТУРЫ ДАННЫХ ---
        const DEFECT_TYPES = ["Царапина", "Вмятина", "Раковина", "Неметаллическое включение", "Разнотолщинность"];
        const DEFECT_SEVERITY = ["Критический", "Существенный", "Допустимый"];
        const COILS = [
            { id: "N-340912", grade: "08Ю", length: 1200, width: 1250, thickness: 2.54, date: "2026-02-15" },
            { id: "N-340913", grade: "08ПС", length: 1500, width: 1500, thickness: 3.0, date: "2026-02-16" },
            { id: "N-340914", grade: "Ст3сп", length: 1000, width: 1000, thickness: 1.8, date: "2026-02-17" },
            { id: "N-340915", grade: "08Ю", length: 1300, width: 1250, thickness: 2.54, date: "2026-02-18" },
            { id: "N-340916", grade: "08ПС", length: 1100, width: 1250, thickness: 2.0, date: "2026-02-19" },
            { id: "N-340917", grade: "10ХСНД", length: 900, width: 1600, thickness: 4.5, date: "2026-02-20" },
            { id: "N-340918", grade: "Ст3сп", length: 1200, width: 1250, thickness: 2.5, date: "2026-02-21" },
            { id: "N-340919", grade: "08Ю", length: 1400, width: 1250, thickness: 2.2, date: "2026-02-22" },
            { id: "N-340920", grade: "08ПС", length: 1600, width: 1500, thickness: 3.2, date: "2026-02-23" },
            { id: "N-340921", grade: "15ХСНД", length: 850, width: 1800, thickness: 5.0, date: "2026-02-24" }
        ];

        // Исходные пользователи
        const DEFAULT_USERS = [
            { id: 1, login: "admin", fio: "Сидоров А.А.", role: "admin", passHash: "a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae3" }, // admin123
            { id: 2, login: "operator", fio: "Иванов И.И.", role: "operator", passHash: "9252ee36d337f766185fc4666f40c7499684178da81b37f4019ec1433f48a974" } // op123
        ];

        // --- 2. УПРАВЛЕНИЕ ХРАНИЛИЩЕМ (Try-Catch / Аудит) ---
        function getStorageItem(key, defaultValue) {
            try {
                const item = localStorage.getItem(key);
                return item ? JSON.parse(item) : defaultValue;
            } catch (error) {
                console.error(`Ошибка чтения localStorage (${key}):`, error);
                addLog('ERROR', `Повреждение базы данных в ключе ${key}. Восстановление.`, 'Система');
                return defaultValue;
            }
        }

        function setStorageItem(key, value) {
            try {
                localStorage.setItem(key, JSON.stringify(value));
            } catch (error) {
                console.error(`Ошибка записи в localStorage (${key}):`, error);
                // Очистка при превышении квоты (контроль)
                if (error.name === 'QuotaExceededError') {
                    addLog('ERROR', 'Квота хранилища превышена! Удаление устаревших дефектов.', 'Система');
                    trimDefectArchive();
                }
            }
        }

        // --- 3. ИНИЦИАЛИЗАЦИЯ СИСТЕМЫ ---
        if (!localStorage.getItem('skkp_users')) setStorageItem('skkp_users', DEFAULT_USERS);
        if (!localStorage.getItem('skkp_thresholds')) {
            setStorageItem('skkp_thresholds', { speed: 15.0, temp: 900, archiveDepth: 30 });
        }
        if (!localStorage.getItem('skkp_logs')) setStorageItem('skkp_logs', []);
        
        let defectArchive = getStorageItem('skkp_defects', []);
        
        // Принудительное обновление для новых реалистичных изображений
        if (defectArchive.length < 30) {
            generateDemoDefects();
        }

        function generateDemoDefects() {
            const demo = [];
            const count = 40; // Больше дефектов для демонстрации
            const now = Date.now();
            
            for (let i = 0; i < count; i++) {
                const coil = COILS[Math.floor(Math.random() * COILS.length)];
                const type = DEFECT_TYPES[Math.floor(Math.random() * DEFECT_TYPES.length)];
                const severity = DEFECT_SEVERITY[Math.floor(Math.random() * DEFECT_SEVERITY.length)];
                const x = Math.floor(Math.random() * coil.length);
                const y = Math.floor(Math.random() * coil.width);
                const dateStr = new Date(now - Math.random() * 86400000 * 5).toISOString();

                // Создаем динамический скриншот дефекта (SVG data URL)
                const img = generateSyntheticImage(type);

                demo.push({
                    id: 'D-' + (1000 + i),
                    coilId: coil.id,
                    type: type,
                    severity: severity,
                    coordX: x,
                    coordY: y,
                    timestamp: dateStr,
                    image: img
                });
            }
            defectArchive = demo;
            setStorageItem('skkp_defects', defectArchive);
        }

        function generateSyntheticImage(type) {
            // Рисуем на скрытом холсте псевдо-дефект высокой реалистичности
            const canvas = document.createElement('canvas');
            canvas.width = 400;
            canvas.height = 400;
            const ctx = canvas.getContext('2d');
            
            // 1. Создаем реалистичный металлический фон (матовый серый с текстурой окалины)
            ctx.fillStyle = '#64748b';
            ctx.fillRect(0, 0, 400, 400);

            // Текстурный градиент для объема листа
            const lightGrad = ctx.createLinearGradient(0, 0, 400, 0);
            lightGrad.addColorStop(0, 'rgba(148, 163, 184, 0.4)');
            lightGrad.addColorStop(0.3, 'rgba(226, 232, 240, 0.6)');
            lightGrad.addColorStop(0.5, 'rgba(71, 85, 105, 0.3)');
            lightGrad.addColorStop(0.8, 'rgba(226, 232, 240, 0.5)');
            lightGrad.addColorStop(1, 'rgba(148, 163, 184, 0.4)');
            ctx.fillStyle = lightGrad;
            ctx.fillRect(0, 0, 400, 400);

            // 2. Добавляем горизонтальные риски (продольные полосы проката)
            ctx.lineWidth = 1;
            for(let i = 0; i < 150; i++) {
                let y = Math.random() * 400;
                let opacity = Math.random() * 0.25;
                ctx.strokeStyle = Math.random() > 0.5 ? `rgba(255, 255, 255, ${opacity})` : `rgba(15, 23, 42, ${opacity * 0.8})`;
                ctx.beginPath();
                ctx.moveTo(0, y);
                ctx.lineTo(400, y + (Math.random() - 0.5) * 5);
                ctx.stroke();
            }

            // Добавление шума (зернистости сенсора камеры)
            const imgData = ctx.getImageData(0, 0, 400, 400);
            const data = imgData.data;
            for (let i = 0; i < data.length; i += 4) {
                const noise = (Math.random() - 0.5) * 22;
                data[i]     = Math.min(255, Math.max(0, data[i] + noise));
                data[i + 1] = Math.min(255, Math.max(0, data[i + 1] + noise));
                data[i + 2] = Math.min(255, Math.max(0, data[i + 2] + noise));
            }
            ctx.putImageData(imgData, 0, 0);

            // 3. Логика генерации специфичных дефектов С РАНДОМНЫМ РАСПОЛОЖЕНИЕМ
            if (type === "Царапина") {
                // Несколько параллельных царапин (сдир)
                const startX = 30 + Math.random() * 120;
                const startY = 30 + Math.random() * 120;
                const endX = 220 + Math.random() * 150;
                const endY = 200 + Math.random() * 150;

                for(let offset = -6; offset <= 6; offset += 3) {
                    ctx.shadowColor = 'rgba(0,0,0,0.4)';
                    ctx.shadowBlur = 3;

                    // Глубокая борозда
                    ctx.strokeStyle = 'rgba(23, 37, 84, 0.95)';
                    ctx.lineWidth = 2 + Math.random() * 3;
                    ctx.lineCap = 'round';
                    ctx.beginPath();
                    ctx.moveTo(startX, startY + offset);
                    ctx.quadraticCurveTo(startX + (endX - startX)/2 + (Math.random() - 0.5) * 60, startY + (endY - startY)/2 + (Math.random() - 0.5) * 60, endX, endY + offset);
                    ctx.stroke();
                    
                    // Металлический заусенец (светлый блик по кромке)
                    ctx.shadowColor = 'transparent';
                    ctx.strokeStyle = 'rgba(255, 255, 255, 0.85)';
                    ctx.lineWidth = 1 + Math.random();
                    ctx.beginPath();
                    ctx.moveTo(startX + 1, startY + offset + 1.5);
                    ctx.quadraticCurveTo(startX + (endX - startX)/2 + (Math.random() - 0.5) * 60, startY + (endY - startY)/2 + (Math.random() - 0.5) * 60, endX + 1, endY + offset + 1.5);
                    ctx.stroke();
                }
            } else if (type === "Вмятина") {
                const count = 1 + Math.floor(Math.random() * 3); // от 1 до 3 вмятин
                for (let v = 0; v < count; v++) {
                    const centerX = 80 + Math.random() * 240;
                    const centerY = 80 + Math.random() * 240;
                    const radius = 30 + Math.random() * 40;

                    // Внешняя тень вмятины (вогнутость)
                    const outerGrad = ctx.createRadialGradient(centerX - 10, centerY - 10, radius * 0.4, centerX, centerY, radius);
                    outerGrad.addColorStop(0, 'rgba(15, 23, 42, 0.85)');
                    outerGrad.addColorStop(0.6, 'rgba(100, 116, 139, 0.3)');
                    outerGrad.addColorStop(1, 'transparent');
                    ctx.fillStyle = outerGrad;
                    ctx.beginPath();
                    ctx.arc(centerX, centerY, radius, 0, Math.PI*2);
                    ctx.fill();

                    // Срез светового блика от деформации
                    const innerGrad = ctx.createRadialGradient(centerX + 15, centerY + 15, radius * 0.2, centerX + 10, centerY + 10, radius * 0.8);
                    innerGrad.addColorStop(0, 'rgba(255, 255, 255, 0.55)');
                    innerGrad.addColorStop(0.5, 'rgba(255, 255, 255, 0.1)');
                    innerGrad.addColorStop(1, 'transparent');
                    ctx.fillStyle = innerGrad;
                    ctx.beginPath();
                    ctx.arc(centerX, centerY, radius, 0, Math.PI*2);
                    ctx.fill();
                }
            } else if (type === "Раковина") {
                // Реалистичные очаги раковин (дефектов травления/коррозии)
                const clusterCount = 2 + Math.floor(Math.random() * 6);
                for (let k = 0; k < clusterCount; k++) {
                    let rx = 50 + Math.random() * 300;
                    let ry = 50 + Math.random() * 300;
                    let size = 10 + Math.random() * 20;

                    // Область изменения цвета вокруг раковины (ржавый оксидный налет)
                    const rustGrad = ctx.createRadialGradient(rx, ry, size * 0.2, rx, ry, size * 1.5);
                    rustGrad.addColorStop(0, 'rgba(146, 64, 14, 0.6)');
                    rustGrad.addColorStop(0.5, 'rgba(120, 53, 4, 0.2)');
                    rustGrad.addColorStop(1, 'transparent');
                    ctx.fillStyle = rustGrad;
                    ctx.beginPath();
                    ctx.arc(rx, ry, size * 1.5, 0, Math.PI * 2);
                    ctx.fill();

                    // Контуры каверны
                    ctx.fillStyle = 'rgba(24, 24, 27, 0.95)';
                    ctx.shadowColor = 'rgba(0,0,0,0.7)';
                    ctx.shadowBlur = 6;
                    ctx.beginPath();
                    ctx.moveTo(rx, ry);
                    for (let i = 0; i < 12; i++) {
                        let angle = (i / 12) * Math.PI * 2;
                        let r = size * (0.6 + Math.random() * 0.6);
                        let cx = rx + Math.cos(angle) * r;
                        let cy = ry + Math.sin(angle) * r;
                        ctx.lineTo(cx, cy);
                    }
                    ctx.closePath();
                    ctx.fill();

                    // Внутренние глубокие трещины
                    ctx.strokeStyle = '#000000';
                    ctx.lineWidth = 2;
                    ctx.shadowBlur = 0;
                    ctx.beginPath();
                    ctx.moveTo(rx - size * 0.4, ry);
                    ctx.lineTo(rx + size * 0.4, ry + (Math.random() - 0.5) * size);
                    ctx.stroke();

                    // Острый светлый блик по выкрошенному металлическому краю
                    ctx.strokeStyle = 'rgba(255, 255, 255, 0.65)';
                    ctx.lineWidth = 1.5;
                    ctx.beginPath();
                    for (let i = 0; i < 6; i++) {
                        let angle = (i / 12) * Math.PI * 2;
                        let r = size * (0.65 + Math.random() * 0.3);
                        let cx = rx + Math.cos(angle) * r;
                        let cy = ry + Math.sin(angle) * r;
                        if (i === 0) ctx.moveTo(cx, cy);
                        else ctx.lineTo(cx, cy);
                    }
                    ctx.stroke();
                }
            } else if (type === "Неметаллическое включение") {
                // Куски шлака, прессованные в металл
                const count = 1 + Math.floor(Math.random() * 4);
                for(let k = 0; k < count; k++) {
                    let baseX = 60 + Math.random() * 280;
                    let baseY = 60 + Math.random() * 280;
                    
                    ctx.fillStyle = 'rgba(24, 24, 27, 0.95)';
                    ctx.beginPath();
                    ctx.moveTo(baseX, baseY);
                    ctx.lineTo(baseX + 30 + Math.random()*30, baseY - 20 + Math.random()*20);
                    ctx.lineTo(baseX + 50 + Math.random()*30, baseY + 30 + Math.random()*30);
                    ctx.lineTo(baseX - 10 + Math.random()*30, baseY + 50 + Math.random()*30);
                    ctx.closePath();
                    ctx.fill();
                    
                    // Ореол выгорания/изменения цвета
                    const halo = ctx.createRadialGradient(baseX+20, baseY+20, 10, baseX+20, baseY+20, 80);
                    halo.addColorStop(0, 'rgba(49, 46, 129, 0.4)');
                    halo.addColorStop(0.5, 'rgba(124, 58, 237, 0.15)');
                    halo.addColorStop(1, 'transparent');
                    ctx.fillStyle = halo;
                    ctx.fillRect(baseX - 50, baseY - 50, 200, 200);
                }
            } else { // Разнотолщинность (Цвета побежалости)
                const startY = Math.random() * 150;
                const grad = ctx.createLinearGradient(0, startY, 400, startY + 250);
                grad.addColorStop(0, 'rgba(30, 27, 75, 0.4)');
                grad.addColorStop(0.2, 'rgba(126, 34, 206, 0.3)');
                grad.addColorStop(0.4, 'rgba(194, 65, 12, 0.25)');
                grad.addColorStop(0.6, 'rgba(255, 255, 255, 0)');
                grad.addColorStop(0.8, 'rgba(194, 65, 12, 0.25)');
                grad.addColorStop(1, 'rgba(30, 27, 75, 0.4)');
                ctx.fillStyle = grad;
                ctx.fillRect(0, 0, 400, 400);

                // Деформация кромки в произвольном месте
                const lineX1 = 30 + Math.random() * 80;
                const lineX2 = 290 + Math.random() * 80;
                ctx.strokeStyle = 'rgba(244, 63, 94, 0.6)';
                ctx.lineWidth = 3;
                ctx.setLineDash([15, 20]);
                ctx.beginPath(); ctx.moveTo(lineX1, 0); ctx.lineTo(lineX1, 400); ctx.stroke();
                ctx.beginPath(); ctx.moveTo(lineX2, 0); ctx.lineTo(lineX2, 400); ctx.stroke();
                ctx.setLineDash([]);
            }

            // Возвращаем изображение
            return canvas.toDataURL();
        }

        // --- 4. АВТОРИЗАЦИЯ И БЕЗОПАСНОСТЬ (SHA-256) ---
        let currentUser = null;
        let sessionTimer = null;
        const SESSION_TIMEOUT = 15 * 60 * 1000; // 15 минут

        // Вспомогательное хеширование (SHA-256 через Web Crypto API)
        async function sha256(message) {
            const msgBuffer = new TextEncoder().encode(message);
            const hashBuffer = await crypto.subtle.digest('SHA-256', msgBuffer);
            const hashArray = Array.from(new Uint8Array(hashBuffer));
            return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
        }

        document.getElementById('login-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const loginVal = document.getElementById('login-username').value;
            const passVal = document.getElementById('login-password').value;
            let users = getStorageItem('skkp_users', DEFAULT_USERS);
            
            // Гарантированный вход для учетных записей по умолчанию
            if (loginVal === 'admin' && passVal === 'admin123') {
                const adminUser = users.find(u => u.login === 'admin') || { id: 1, login: "admin", fio: "Сидоров А.А.", role: "admin" };
                loginSuccess(adminUser);
                return;
            }
            if (loginVal === 'operator' && passVal === 'op123') {
                const opUser = users.find(u => u.login === 'operator') || { id: 2, login: "operator", fio: "Иванов И.И.", role: "operator" };
                loginSuccess(opUser);
                return;
            }

            const typedHash = await sha256(passVal);
            const foundUser = users.find(u => u.login === loginVal && u.passHash === typedHash);

            if (foundUser) {
                loginSuccess(foundUser);
            } else {
                alert("Неверное имя пользователя или пароль!");
                addLog('WARN', `Неудачная попытка входа с логином: ${loginVal}`, 'Система');
            }
        });

        function loginSuccess(user) {
            currentUser = user;
            document.getElementById('auth-screen').classList.add('opacity-0', 'invisible');
            document.getElementById('app-container').classList.remove('hidden');
            
            document.getElementById('user-full-name').textContent = user.fio;
            document.getElementById('user-role-badge').textContent = user.role === 'admin' ? 'Администратор' : 'Оператор';
            
            if (user.role === 'admin') {
                document.getElementById('nav-admin').classList.remove('hidden');
                if (document.getElementById('hub-admin')) {
                    document.getElementById('hub-admin').classList.remove('hidden');
                    document.getElementById('hub-admin').classList.add('flex');
                }
                document.getElementById('btn-clear-log').classList.remove('hidden');
            } else {
                document.getElementById('nav-admin').classList.add('hidden');
                if (document.getElementById('hub-admin')) {
                    document.getElementById('hub-admin').classList.add('hidden');
                    document.getElementById('hub-admin').classList.remove('flex');
                }
                document.getElementById('btn-clear-log').classList.add('hidden');
            }

            addLog('INFO', `Пользователь ${user.fio} вошел в систему.`, user.fio);
            resetSessionTimer();
            setupActivityListeners();

            // Инициализация рабочих экранов и запуск процессов
            navigateTo('hub');
            startLiveSimulation();
            updatePassportDropdown();
        }

        function logout() {
            addLog('INFO', `Пользователь ${currentUser?.fio} вышел из системы.`, currentUser?.fio || 'Система');
            currentUser = null;
            clearTimeout(sessionTimer);
            document.getElementById('auth-screen').classList.remove('opacity-0', 'invisible');
            document.getElementById('app-container').classList.add('hidden');
            document.getElementById('login-form').reset();
            stopLiveSimulation();
        }

        // Сессионный контроль (бездействие)
        function resetSessionTimer() {
            clearTimeout(sessionTimer);
            sessionTimer = setTimeout(() => {
                alert("Сессия завершена из-за бездействия.");
                logout();
            }, SESSION_TIMEOUT);
        }

        function setupActivityListeners() {
            ['mousedown', 'mousemove', 'keypress', 'scroll', 'touchstart'].forEach(name => {
                document.addEventListener(name, resetSessionTimer, true);
            });
        }

        // --- 5. МАРШРУТИЗАЦИЯ / НАВИГАЦИЯ ---
        function navigateTo(viewId) {
            // Скрыть все вкладки
            document.querySelectorAll('.view-content').forEach(el => {
                el.classList.add('hidden', 'opacity-0', 'scale-95');
            });
            // Показать выбранную
            const target = document.getElementById(`view-${viewId}`);
            target.classList.remove('hidden');
            // Плавное появление
            setTimeout(() => {
                target.classList.remove('opacity-0', 'scale-95');
            }, 50);

            // Обновление кнопок меню
            document.querySelectorAll('.nav-btn').forEach(btn => {
                btn.classList.remove('text-blue-400', 'bg-slate-700/50');
                btn.classList.add('text-slate-400');
            });
            const activeBtn = document.querySelector(`[data-nav="${viewId}"]`);
            if (activeBtn) {
                activeBtn.classList.remove('text-slate-400');
                activeBtn.classList.add('text-blue-400', 'bg-slate-700/50');
            }

            // Хуки рендеринга разделов
            if (viewId === 'archive') renderArchive();
            if (viewId === 'passport') renderPassport();
            if (viewId === 'events') renderLog();
            if (viewId === 'admin') renderAdmin();
        }

        // --- 6. ТОСТЫ И УВЕДОМЛЕНИЯ ---
        function showToast(title, message, severity) {
            const popupChecked = document.getElementById('setting-notify-popup').checked;
            const soundChecked = document.getElementById('setting-notify-sound').checked;

            if (popupChecked) {
                const container = document.getElementById('toast-container');
                const toast = document.createElement('div');
                
                let colorClass = 'border-blue-500 bg-slate-800 text-blue-400';
                let icon = 'fa-info-circle';
                
                if (severity === 'Критический' || severity === 'ERROR') {
                    colorClass = 'border-rose-500 bg-rose-950/90 text-rose-200 pulse-alert';
                    icon = 'fa-triangle-exclamation';
                } else if (severity === 'Существенный' || severity === 'WARN') {
                    colorClass = 'border-yellow-500 bg-slate-800 text-yellow-400';
                    icon = 'fa-circle-exclamation';
                }

                toast.className = `p-4 border-l-4 rounded shadow-lg flex items-start space-x-3 transition-all duration-300 transform translate-x-full ${colorClass}`;
                toast.innerHTML = `
                    <i class="fa-solid ${icon} mt-1 text-base"></i>
                    <div class="flex-1">
                        <span class="font-bold text-xs block uppercase">${title}</span>
                        <span class="text-xs text-slate-300">${message}</span>
                    </div>
                `;
                container.appendChild(toast);
                setTimeout(() => toast.classList.remove('translate-x-full'), 10);
                setTimeout(() => {
                    toast.classList.add('opacity-0');
                    setTimeout(() => toast.remove(), 300);
                }, 5000);
            }

            if (soundChecked && (severity === 'Критический' || severity === 'ERROR')) {
                const sound = document.getElementById('alert-sound');
                sound.currentTime = 0;
                sound.play().catch(e => {}); // Игнорируем блокировки автоплея
            }
        }

        // --- 7. СИСТЕМНЫЙ ЖУРНАЛ (Аудит) ---
        function addLog(level, message, user = 'Система') {
            const logs = getStorageItem('skkp_logs', []);
            logs.unshift({
                level: level,
                text: message,
                user: user,
                time: new Date().toISOString()
            });
            // Обрезка квоты (храним 1000 записей)
            if (logs.length > 1000) logs.pop();
            setStorageItem('skkp_logs', logs);
            
            // Если вкладка активна - перерендерить
            if (!document.getElementById('view-events').classList.contains('hidden')) {
                renderLog();
            }
        }

        function renderLog() {
            const logs = getStorageItem('skkp_logs', []);
            const filterLevel = document.getElementById('log-filter-level').value;
            const filterSearch = document.getElementById('log-filter-search').value.toLowerCase();
            const container = document.getElementById('log-container');
            
            let html = '';
            
            const filtered = logs.filter(l => {
                const matchLvl = filterLevel === 'all' || l.level === filterLevel;
                const matchSearch = l.text.toLowerCase().includes(filterSearch) || l.user.toLowerCase().includes(filterSearch);
                return matchLvl && matchSearch;
            });

            filtered.forEach(l => {
                let clr = 'text-slate-400';
                if (l.level === 'WARN') clr = 'text-yellow-400';
                if (l.level === 'ERROR') clr = 'text-rose-400 font-bold';
                
                html += `<div class="border-b border-slate-900 pb-1 flex space-x-4">
                    <span class="text-slate-500 font-bold">[${l.time.replace('T', ' ').substring(0, 19)}]</span>
                    <span class="${clr} font-semibold w-12">${l.level}</span>
                    <span class="text-slate-300 flex-1">${escapeHTML(l.text)}</span>
                    <span class="text-slate-500 italic">(${escapeHTML(l.user)})</span>
                </div>`;
            });
            
            container.innerHTML = html || `<div class="text-slate-500 text-center py-4">События не найдены</div>`;
        }

        function clearLog() {
            if (confirm("Вы действительно хотите удалить все записи журнала?")) {
                setStorageItem('skkp_logs', []);
                addLog('INFO', 'Журнал событий очищен администратором.', currentUser.fio);
                renderLog();
            }
        }

        // --- 8. ДАШБОРД: СИМУЛЯТОР & CANVAS РУЛОНА ---
        let simulationInterval = null;
        let simProgress = 0;
        let simSpeed = 12.4;
        let currentCoilIdx = 0;

        function startLiveSimulation() {
            const thresholds = getStorageItem('skkp_thresholds', { speed: 15.0, temp: 900 });
            
            if (simulationInterval) clearInterval(simulationInterval);
            
            simulationInterval = setInterval(() => {
                // Колебания параметров
                simSpeed = (12 + Math.random() * 2).toFixed(1);
                const temp = Math.floor(820 + Math.random() * 40);
                const thickness = (2.5 + (Math.random() - 0.5) * 0.05).toFixed(2);
                
                // Проверка порогов
                if (simSpeed > thresholds.speed) {
                    addLog('WARN', `Превышена скорость линии: ${simSpeed} м/с (Порог: ${thresholds.speed})`, 'Датчик');
                    showToast('Предупреждение', `Превышена скорость проката: ${simSpeed} м/с!`, 'WARN');
                }
                if (temp > thresholds.temp) {
                    addLog('WARN', `Критический нагрев: ${temp}°C (Порог: ${thresholds.temp})`, 'Датчик');
                    showToast('Предупреждение', `Температура превысила норму: ${temp}°C`, 'WARN');
                }

                // Обновление UI Дашборда
                document.getElementById('header-speed').textContent = `${simSpeed} м/с`;
                document.getElementById('stat-speed').textContent = `${simSpeed} м/с`;
                document.getElementById('stat-temp').textContent = `${temp} °C`;
                document.getElementById('stat-thickness').textContent = `${thickness} мм`;

                // Симуляция дефектов авто-генератором (редкая)
                if (Math.random() < 0.03) {
                    generateSimulatedDefect();
                }

                // Прогресс рулона
                simProgress += 0.5;
                if (simProgress >= 100) {
                    addLog('INFO', `Завершен контроль рулона ${COILS[currentCoilIdx].id}`, 'Система');
                    simProgress = 0;
                    currentCoilIdx = (currentCoilIdx + 1) % COILS.length;
                    document.getElementById('current-coil-id').textContent = `#${COILS[currentCoilIdx].id}`;
                }
                
                document.getElementById('coil-progress').textContent = `${Math.floor(simProgress)}%`;
                drawCoilCanvas();

            }, 1000);
        }

        function stopLiveSimulation() {
            clearInterval(simulationInterval);
            simulationInterval = null;
            document.getElementById('btn-sim-toggle').innerHTML = `<i class="fa-solid fa-play mr-1"></i> Запустить линию`;
            document.getElementById('btn-sim-toggle').className = "px-4 py-2 bg-emerald-600 hover:bg-emerald-500 text-white text-sm font-medium rounded-lg shadow-sm transition active:scale-95 cursor-pointer";
        }

        function toggleSimulation() {
            if (simulationInterval) {
                stopLiveSimulation();
            } else {
                startLiveSimulation();
                document.getElementById('btn-sim-toggle').innerHTML = `<i class="fa-solid fa-pause mr-1"></i> Остановить`;
                document.getElementById('btn-sim-toggle').className = "px-4 py-2 bg-slate-700 hover:bg-slate-600 text-slate-200 text-sm font-medium rounded-lg border border-slate-600 transition active:scale-95 cursor-pointer";
            }
        }

        function triggerManualDefect() {
            generateSimulatedDefect();
        }

        function generateSimulatedDefect() {
            const coil = COILS[currentCoilIdx];
            const type = DEFECT_TYPES[Math.floor(Math.random() * DEFECT_TYPES.length)];
            const severity = DEFECT_SEVERITY[Math.floor(Math.random() * DEFECT_SEVERITY.length)];
            const x = Math.floor((simProgress / 100) * coil.length);
            const y = Math.floor(Math.random() * coil.width);

            const newDefect = {
                id: 'D-' + (defectArchive.length + 1001),
                coilId: coil.id,
                type: type,
                severity: severity,
                coordX: x,
                coordY: y,
                timestamp: new Date().toISOString(),
                image: generateSyntheticImage(type)
            };

            defectArchive.unshift(newDefect);
            setStorageItem('skkp_defects', defectArchive);

            // Оповещение
            addLog('WARN', `Обнаружен дефект "${type}" (${severity}) на рулоне ${coil.id}`, 'ML Детектор');
            showToast(`Дефект: ${type}`, `Рулон ${coil.id}, Координата: ${x}м`, severity);

            // Перерисовать ленту на Дашборде
            renderDashboardStream();
        }

        function drawCoilCanvas() {
            const canvas = document.getElementById('coil-canvas');
            if (!canvas) return;
            
            // Динамическая адаптация разрешения
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight || 200;
            
            const ctx = canvas.getContext('2d');
            const w = canvas.width;
            const h = canvas.height;

            // 1. Градиентный фон рабочей области (космос/темный антрацит)
            const bgGrad = ctx.createLinearGradient(0, 0, 0, h);
            bgGrad.addColorStop(0, '#090d16');
            bgGrad.addColorStop(1, '#111827');
            ctx.fillStyle = bgGrad;
            ctx.fillRect(0, 0, w, h);

            // 2. Сетка оборудования (визуальный техно-стиль)
            ctx.strokeStyle = 'rgba(30, 41, 59, 0.4)';
            ctx.lineWidth = 1;
            const gridSize = 25;
            for(let x = 0; x < w; x += gridSize) {
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, h); ctx.stroke();
            }
            for(let y = 0; y < h; y += gridSize) {
                ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(w, y); ctx.stroke();
            }

            // 3. Рисуем ТРЕХМЕРНЫЙ ЭФФЕКТ РУЛОНА (смотанная часть в начале)
            const coilLeft = 40;
            const coilY = h / 2;
            const coilRadius = 35 + (1 - simProgress/100) * 15; // Уменьшается по мере разматывания

            // Боковина рулона
            ctx.fillStyle = '#475569';
            ctx.beginPath();
            ctx.ellipse(coilLeft, coilY, coilRadius * 0.4, coilRadius, 0, 0, Math.PI * 2);
            ctx.fill();
            ctx.strokeStyle = '#cbd5e1';
            ctx.stroke();

            // Внутреннее отверстие рулона
            ctx.fillStyle = '#0f172a';
            ctx.beginPath();
            ctx.ellipse(coilLeft, coilY, (coilRadius * 0.4) * 0.3, coilRadius * 0.3, 0, 0, Math.PI * 2);
            ctx.fill();
            ctx.strokeStyle = '#334155';
            ctx.stroke();

            // 4. ПОЛОСА МЕТАЛЛА (Разворачивающаяся лента)
            const stripX = coilLeft;
            const stripY = h / 2 - h / 4;
            const stripW = w - stripX - 60;
            const stripH = h / 2;

            // Необработанная часть (темный металл)
            const unprocGrad = ctx.createLinearGradient(stripX, stripY, stripX + stripW, stripY);
            unprocGrad.addColorStop(0, '#1e293b');
            unprocGrad.addColorStop(1, '#334155');
            ctx.fillStyle = unprocGrad;
            ctx.fillRect(stripX, stripY, stripW, stripH);

            // Пройденная (обработанная) часть - ярче + текстура
            const progressRatio = simProgress / 100;
            const procW = stripW * progressRatio;
            if(procW > 0) {
                const procGrad = ctx.createLinearGradient(stripX, stripY, stripX + procW, stripY);
                procGrad.addColorStop(0, '#475569');
                procGrad.addColorStop(1, '#64748b');
                ctx.fillStyle = procGrad;
                ctx.fillRect(stripX, stripY, procW, stripH);
                
                // Эффект нагрева кромки (если температура высокая)
                ctx.fillStyle = 'rgba(249, 115, 22, 0.05)';
                ctx.fillRect(stripX, stripY, procW, 4);
                ctx.fillRect(stripX, stripY + stripH - 4, procW, 4);
            }

            // 5. ЛИНИЯ СКАНИРОВАНИЯ (Лазерный луч)
            const scanX = stripX + procW;
            if (scanX < stripX + stripW) {
                // Лазерный луч
                ctx.strokeStyle = '#22c55e';
                ctx.lineWidth = 3;
                ctx.shadowColor = '#22c55e';
                ctx.shadowBlur = 10;
                ctx.beginPath();
                ctx.moveTo(scanX, stripY - 10);
                ctx.lineTo(scanX, stripY + stripH + 10);
                ctx.stroke();
                
                // Тень и свечение лазера
                ctx.shadowBlur = 0;
                const laserGrad = ctx.createLinearGradient(scanX - 25, 0, scanX, 0);
                laserGrad.addColorStop(0, 'transparent');
                laserGrad.addColorStop(1, 'rgba(34, 197, 94, 0.25)');
                ctx.fillStyle = laserGrad;
                ctx.fillRect(scanX - 25, stripY, 25, stripH);
            }

            // 6. ОТРИСОВКА ДЕФЕКТОВ
            const currentCoilId = COILS[currentCoilIdx].id;
            const currentDefects = defectArchive.filter(d => d.coilId === currentCoilId);

            currentDefects.forEach(d => {
                const ratioX = d.coordX / COILS[currentCoilIdx].length;
                const pX = stripX + stripW * ratioX;
                const pY = stripY + (d.coordY / COILS[currentCoilIdx].width) * stripH;

                // Отображаем дефекты, которые уже попали под лазерный сканер
                if (pX <= scanX) {
                    ctx.beginPath();
                    ctx.arc(pX, pY, 6, 0, Math.PI * 2);
                    
                    let defColor = '#3b82f6';
                    if (d.severity === 'Критический') {
                        defColor = '#f43f5e';
                        // Пульсирующее свечение для критических
                        ctx.shadowColor = '#f43f5e';
                        ctx.shadowBlur = 8;
                    } else if (d.severity === 'Существенный') {
                        defColor = '#eab308';
                    }
                    
                    ctx.fillStyle = defColor;
                    ctx.fill();
                    ctx.strokeStyle = '#ffffff';
                    ctx.lineWidth = 1.5;
                    ctx.stroke();
                    ctx.shadowBlur = 0; // Сброс теней
                }
            });
        }

        function renderDashboardStream() {
            const stream = document.getElementById('live-defect-stream');
            let html = '';

            // Берем последние 10 дефектов
            const slice = defectArchive.slice(0, 10);
            document.getElementById('header-defects-count').textContent = defectArchive.length;

            slice.forEach(d => {
                let badgeClr = 'bg-blue-500/10 text-blue-400 border-blue-500/20';
                if (d.severity === 'Критический') badgeClr = 'bg-rose-500/10 text-rose-400 border-rose-500/20';
                if (d.severity === 'Существенный') badgeClr = 'bg-yellow-500/10 text-yellow-400 border-yellow-500/20';

                html += `
                    <div class="p-3 bg-slate-900/40 backdrop-blur-sm rounded-xl border border-slate-800/80 flex items-center justify-between hover:border-slate-700/50 hover:bg-slate-800/40 transition-all duration-300 shadow-sm">
                        <div class="flex items-center space-x-3">
                            <div class="relative group">
                                <img src="${d.image}" class="w-11 h-11 rounded-lg border border-slate-700/50 object-cover group-hover:border-blue-500/50 transition">
                                <span class="absolute -top-1 -right-1 w-2 h-2 rounded-full ${d.severity === 'Критический' ? 'bg-rose-500' : (d.severity === 'Существенный' ? 'bg-yellow-500' : 'bg-blue-500')} animate-pulse"></span>
                            </div>
                            <div>
                                <span class="text-xs font-bold text-slate-100 block">${d.type}</span>
                                <span class="text-[10px] text-slate-400 block mt-0.5 flex items-center">
                                    <i class="fa-solid fa-scroll text-[9px] mr-1 text-slate-500"></i>
                                    Рулон: <b class="text-slate-300 ml-1 font-mono">${d.coilId}</b> 
                                    <span class="mx-1 text-slate-600">|</span> 
                                    <span class="text-slate-400 font-mono">${d.coordX}м</span>
                                </span>
                            </div>
                        </div>
                        <span class="text-[10px] px-2.5 py-1 rounded-md font-semibold border ${badgeClr} shadow-sm tracking-wide">${d.severity}</span>
                    </div>
                `;
            });

            stream.innerHTML = html || `<div class="text-center text-slate-500 text-xs py-10">Пока дефекты не зафиксированы</div>`;
        }

        // --- 9. АРХИВ ДЕФЕКТОВ: ТАБЛИЦА, ФИЛЬТРЫ, ЭКСПОРТ ---
        let archivePage = 1;
        const itemsPerPage = 8;
        let filteredArchive = [];

        function renderArchive() {
            const search = document.getElementById('filter-search').value.toLowerCase();
            const type = document.getElementById('filter-type').value;
            const status = document.getElementById('filter-status').value;
            const sort = document.getElementById('filter-sort').value;

            filteredArchive = defectArchive.filter(d => {
                const matchSearch = d.coilId.toLowerCase().includes(search) || d.id.toLowerCase().includes(search);
                const matchType = type === 'all' || d.type === type;
                const matchStatus = status === 'all' || d.severity === status;
                return matchSearch && matchType && matchStatus;
            });

            // Сортировка
            if (sort === 'date_desc') filteredArchive.sort((a,b) => new Date(b.timestamp) - new Date(a.timestamp));
            else if (sort === 'date_asc') filteredArchive.sort((a,b) => new Date(a.timestamp) - new Date(b.timestamp));
            else if (sort === 'coil_id') filteredArchive.sort((a,b) => a.coilId.localeCompare(b.coilId));

            document.getElementById('archive-total-records').textContent = filteredArchive.length;

            renderArchiveTable();
        }

        function applyFilters() {
            archivePage = 1;
            renderArchive();
        }

        function renderArchiveTable() {
            const tbody = document.getElementById('archive-table-body');
            tbody.innerHTML = '';

            const start = (archivePage - 1) * itemsPerPage;
            const pageItems = filteredArchive.slice(start, start + itemsPerPage);

            pageItems.forEach(d => {
                let statusClr = 'bg-blue-500/10 text-blue-400 border-blue-500/20';
                if (d.severity === 'Критический') statusClr = 'bg-rose-500/10 text-rose-400 border-rose-500/20';
                if (d.severity === 'Существенный') statusClr = 'bg-yellow-500/10 text-yellow-400 border-yellow-500/20';

                tbody.innerHTML += `
                    <tr class="hover:bg-slate-800/30">
                        <td class="p-4 font-mono font-bold text-slate-400">${d.id}</td>
                        <td class="p-4">${d.coilId}</td>
                        <td class="p-4 font-semibold">${d.type}</td>
                        <td class="p-4 text-xs text-slate-400 font-mono">X: ${d.coordX}м / Y: ${d.coordY}мм</td>
                        <td class="p-4">
                            <span class="text-xs px-2 py-0.5 rounded border font-medium ${statusClr}">${d.severity}</span>
                        </td>
                        <td class="p-4 text-xs text-slate-400">${d.timestamp.replace('T', ' ').substring(0, 16)}</td>
                        <td class="p-4 text-right">
                            <button onclick="viewCoilPassport('${d.coilId}')" class="px-2 py-1 bg-slate-700 hover:bg-blue-600 hover:text-white rounded text-xs transition cursor-pointer">
                                Паспорт
                            </button>
                        </td>
                    </tr>
                `;
            });

            renderPagination();
        }

        function renderPagination() {
            const container = document.getElementById('archive-pagination');
            container.innerHTML = '';
            const totalPages = Math.ceil(filteredArchive.length / itemsPerPage) || 1;

            for (let i = 1; i <= totalPages; i++) {
                const isActive = i === archivePage;
                container.innerHTML += `
                    <button onclick="changePage(${i})" class="px-3 py-1 rounded text-xs font-semibold cursor-pointer ${isActive ? 'bg-blue-600 text-white' : 'bg-slate-700 text-slate-400 hover:bg-slate-600'}">
                        ${i}
                    </button>
                `;
            }
        }

        function changePage(page) {
            archivePage = page;
            renderArchiveTable();
        }

        function exportToCSV() {
            let csvContent = "data:text/csv;charset=utf-8,ID;Coil_ID;Type;Severity;Coord_X;Coord_Y;Timestamp\n";
            filteredArchive.forEach(d => {
                csvContent += `${d.id};${d.coilId};${d.type};${d.severity};${d.coordX};${d.coordY};${d.timestamp}\n`;
            });
            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `skkp_archive_${Date.now()}.csv`);
            document.body.appendChild(link);
            link.click();
            link.remove();
            addLog('INFO', 'Выполнен экспорт архива в CSV.', currentUser.fio);
        }

        function printArchive() {
            window.print();
        }

        function viewCoilPassport(coilId) {
            document.getElementById('passport-coil-select').value = coilId;
            navigateTo('passport');
        }

        // --- 10. ПАСПОРТ РУЛОНА: МЕТАДАННЫЕ, SVG, CHARTS ---
        let passChart = null;
        let passBarChart = null;

        function updatePassportDropdown() {
            const select = document.getElementById('passport-coil-select');
            select.innerHTML = '';
            COILS.forEach(c => {
                select.innerHTML += `<option value="${c.id}">Рулон ${c.id}</option>`;
            });
            if (COILS.length > 0) renderPassport();
        }

        function renderPassport() {
            const selectedId = document.getElementById('passport-coil-select').value;
            const coil = COILS.find(c => c.id === selectedId);
            if (!coil) return;

            const defs = defectArchive.filter(d => d.coilId === selectedId);

            // Наполнение метаданных
            document.getElementById('pass-id').textContent = coil.id;
            document.getElementById('pass-grade').textContent = coil.grade;
            document.getElementById('pass-length').textContent = `${coil.length} м`;
            document.getElementById('pass-dims').textContent = `${coil.thickness} мм / ${coil.width} мм`;
            document.getElementById('pass-date').textContent = coil.date;
            document.getElementById('pass-total-def').textContent = defs.length;

            const statusEl = document.getElementById('pass-quality-status');
            const criticals = defs.filter(d => d.severity === 'Критический').length;
            if (criticals > 0) {
                statusEl.textContent = 'БРАК (Не годен)';
                statusEl.className = 'px-3 py-2 text-center rounded-lg font-bold text-sm bg-rose-500/10 text-rose-400 border border-rose-500/20';
            } else if (defs.length > 5) {
                statusEl.textContent = '2 СОРТ';
                statusEl.className = 'px-3 py-2 text-center rounded-lg font-bold text-sm bg-yellow-500/10 text-yellow-400 border border-yellow-500/20';
            } else {
                statusEl.textContent = 'ГОДЕН (1 сорт)';
                statusEl.className = 'px-3 py-2 text-center rounded-lg font-bold text-sm bg-emerald-500/10 text-emerald-400 border border-emerald-500/20';
            }

            // Рендер Графиков Chart.js
            renderPassportCharts(defs);

            // Рендер SVG-карты
            renderPassportSVG(defs, coil);

            // Рендер Галереи
            renderPassportGallery(defs);
        }

        function renderPassportCharts(defs) {
            // Круговая
            const typeCounts = {};
            DEFECT_TYPES.forEach(t => typeCounts[t] = 0);
            defs.forEach(d => typeCounts[d.type] = (typeCounts[d.type] || 0) + 1);

            const typeData = Object.values(typeCounts);

            if (passChart) passChart.destroy();
            const ctx = document.getElementById('passport-chart').getContext('2d');
            passChart = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: DEFECT_TYPES,
                    datasets: [{
                        data: typeData,
                        backgroundColor: ['#3b82f6', '#f59e0b', '#10b981', '#a855f7', '#64748b'],
                        borderColor: '#1e293b',
                        borderWidth: 2
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } }
                }
            });

            // Столбчатая
            const sevCounts = { 'Критический': 0, 'Существенный': 0, 'Допустимый': 0 };
            defs.forEach(d => sevCounts[d.severity]++);

            if (passBarChart) passBarChart.destroy();
            const ctxBar = document.getElementById('passport-bar-chart').getContext('2d');
            passBarChart = new Chart(ctxBar, {
                type: 'bar',
                data: {
                    labels: Object.keys(sevCounts),
                    datasets: [{
                        label: 'Кол-во',
                        data: Object.values(sevCounts),
                        backgroundColor: ['#f43f5e', '#eab308', '#3b82f6'],
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } },
                    scales: {
                        y: { ticks: { color: '#64748b', stepSize: 1 }, grid: { color: '#334155' } },
                        x: { ticks: { color: '#64748b' }, grid: { color: 'transparent' } }
                    }
                }
            });
        }

        function renderPassportSVG(defs, coil) {
            const svg = document.getElementById('passport-svg');
            let html = `
                <!-- Фон рулона -->
                <rect x="0" y="20" width="1000" height="110" fill="#1e293b" rx="4"/>
                <line x1="0" y1="20" x2="1000" y2="20" stroke="#334155" stroke-dasharray="5,5"/>
                <line x1="0" y1="130" x2="1000" y2="130" stroke="#334155" stroke-dasharray="5,5"/>
            `;

            defs.forEach(d => {
                const ratioX = d.coordX / coil.length;
                const ratioY = d.coordY / coil.width;

                const circleX = ratioX * 1000;
                const circleY = 20 + ratioY * 110;

                let color = '#3b82f6';
                if (d.severity === 'Критический') color = '#f43f5e';
                if (d.severity === 'Существенный') color = '#eab308';

                html += `
                    <circle cx="${circleX}" cy="${circleY}" r="6" fill="${color}" stroke="#0f172a" stroke-width="2">
                        <title>${d.type} (${d.severity})&#10;Расстояние: ${d.coordX}м&#10;Y: ${d.coordY}мм</title>
                    </circle>
                `;
            });

            svg.innerHTML = html;
        }

        function renderPassportGallery(defs) {
            const gal = document.getElementById('passport-gallery');
            gal.innerHTML = '';
            
            if (defs.length === 0) {
                gal.innerHTML = `<span class="text-xs text-slate-500 col-span-full text-center">Изображения отсутствуют</span>`;
                return;
            }

            defs.forEach(d => {
                gal.innerHTML += `
                    <div class="group relative aspect-square bg-slate-950 border border-slate-700/50 rounded overflow-hidden cursor-pointer" onclick="openLightbox('${d.image}')">
                        <img src="${d.image}" class="w-full h-full object-cover group-hover:scale-110 transition duration-300">
                        <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-transparent flex items-end p-2 opacity-0 group-hover:opacity-100 transition">
                            <span class="text-[10px] text-slate-200 truncate font-semibold">${d.type}</span>
                        </div>
                    </div>
                `;
            });
        }

        function openLightbox(src) {
            const modal = document.getElementById('lightbox-modal');
            const img = document.getElementById('lightbox-img');
            img.src = src;
            modal.classList.remove('hidden');
            modal.classList.add('flex');
        }

        function closeLightbox() {
            const modal = document.getElementById('lightbox-modal');
            modal.classList.add('hidden');
            modal.classList.remove('flex');
        }

        // --- 11. АДМИНИСТРИРОВАНИЕ: USER CRUD, НАСТРОЙКИ ---
        function renderAdmin() {
            const users = getStorageItem('skkp_users', DEFAULT_USERS);
            const tbody = document.getElementById('user-table-body');
            tbody.innerHTML = '';

            users.forEach(u => {
                // Нельзя удалить самого себя или админа по умолчанию
                const isProtected = u.login === 'admin' || u.id === currentUser.id;
                
                tbody.innerHTML += `
                    <tr class="hover:bg-slate-800/30">
                        <td class="p-4 font-semibold">${escapeHTML(u.fio)}</td>
                        <td class="p-4 text-slate-400">${escapeHTML(u.login)}</td>
                        <td class="p-4"><span class="text-xs px-2 py-0.5 bg-slate-700 rounded capitalize">${u.role}</span></td>
                        <td class="p-4 text-right">
                            <button ${isProtected ? 'disabled' : ''} onclick="deleteUser(${u.id})" class="text-rose-400 hover:text-rose-300 disabled:text-slate-600 disabled:cursor-not-allowed transition cursor-pointer">
                                <i class="fa-solid fa-user-minus"></i>
                            </button>
                        </td>
                    </tr>
                `;
            });

            // Подгрузка порогов
            const thr = getStorageItem('skkp_thresholds', { speed: 15.0, temp: 900, archiveDepth: 30 });
            document.getElementById('thr-speed').value = thr.speed;
            document.getElementById('label-thr-speed').textContent = thr.speed;
            document.getElementById('thr-temp').value = thr.temp;
            document.getElementById('label-thr-temp').textContent = thr.temp;
            document.getElementById('setting-archive-depth').value = thr.archiveDepth || 30;
        }

        document.getElementById('admin-add-user-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const fio = document.getElementById('add-user-fio').value;
            const login = document.getElementById('add-user-login').value;
            const pass = document.getElementById('add-user-pass').value;
            const role = document.getElementById('add-user-role').value;

            const users = getStorageItem('skkp_users', DEFAULT_USERS);

            if (users.find(u => u.login === login)) {
                alert("Пользователь с таким логином уже существует!");
                return;
            }

            const hash = await sha256(pass);
            users.push({
                id: Date.now(),
                login: login,
                fio: fio,
                role: role,
                passHash: hash
            });

            setStorageItem('skkp_users', users);
            addLog('INFO', `Добавлен новый пользователь ${fio} (${role})`, currentUser.fio);
            document.getElementById('admin-add-user-form').reset();
            renderAdmin();
        });

        function deleteUser(id) {
            let users = getStorageItem('skkp_users', DEFAULT_USERS);
            const userToDel = users.find(u => u.id === id);
            if (userToDel && confirm(`Удалить пользователя ${userToDel.fio}?`)) {
                users = users.filter(u => u.id !== id);
                setStorageItem('skkp_users', users);
                addLog('INFO', `Пользователь ${userToDel.fio} удален.`, currentUser.fio);
                renderAdmin();
            }
        }

        function updateAdminThr() {
            const speed = parseFloat(document.getElementById('thr-speed').value);
            const temp = parseInt(document.getElementById('thr-temp').value);
            const depth = parseInt(document.getElementById('setting-archive-depth').value);

            document.getElementById('label-thr-speed').textContent = speed.toFixed(1);
            document.getElementById('label-thr-temp').textContent = temp;

            setStorageItem('skkp_thresholds', { speed, temp, archiveDepth: depth });
            trimDefectArchive();
        }

        function trimDefectArchive() {
            const thr = getStorageItem('skkp_thresholds', { archiveDepth: 30 });
            const maxDays = thr.archiveDepth || 30;
            const cutoff = Date.now() - (maxDays * 24 * 60 * 60 * 1000);

            let changed = false;
            defectArchive = defectArchive.filter(d => {
                const time = new Date(d.timestamp).getTime();
                if (time < cutoff) {
                    changed = true;
                    return false;
                }
                return true;
            });

            if (changed) {
                setStorageItem('skkp_defects', defectArchive);
                addLog('INFO', `Выполнена очистка устаревших данных (глубина хранения: ${maxDays} дн.)`, 'Система');
            }
        }

        function resetAllData() {
            if (confirm("ВНИМАНИЕ! Будет произведен полный сброс БД до демо-настроек. Продолжить?")) {
                localStorage.clear();
                alert("Данные сброшены. Требуется перезагрузка сессии.");
                location.reload();
            }
        }

        // --- 12. НАСТРОЙКИ: ТЕМЫ ---
        function setTheme(theme) {
            const html = document.documentElement;
            const darkBtn = document.getElementById('theme-dark-btn');
            const lightBtn = document.getElementById('theme-light-btn');

            if (theme === 'light') {
                html.classList.remove('dark');
                html.classList.add('light');
                darkBtn.className = "px-3 py-1.5 text-xs font-semibold rounded text-slate-500 cursor-pointer";
                lightBtn.className = "px-3 py-1.5 text-xs font-semibold rounded bg-white text-slate-900 shadow border border-slate-200 cursor-pointer";
            } else {
                html.classList.add('dark');
                html.classList.remove('light');
                darkBtn.className = "px-3 py-1.5 text-xs font-semibold rounded bg-slate-700 text-slate-100 cursor-pointer";
                lightBtn.className = "px-3 py-1.5 text-xs font-semibold rounded text-slate-400 cursor-pointer";
            }
        }

        // Вспомогательное экранирование
        function escapeHTML(str) {
            return str.replace(/[&<>'"]/g, 
                tag => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;' }[tag] || tag)
            );
        }

        // Рендер по дефолту (лента дефектов)
        renderDashboardStream();

    </script>
</body>
</html>
