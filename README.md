<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cheesy Rust - Сервер</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; background-color: #f9f9f9; color: #1a1a1a; line-height: 1.6; transition: background 0.3s, color 0.3s; }
        
        /* Тёмная тема */
        body.dark { background-color: #121212; color: #e0e0e0; }
        body.dark nav { background: #1e1e1e; border-bottom: 1px solid #333; }
        body.dark .logo { color: #e74c3c; }
        body.dark .nav-links a { color: #aaa; }
        body.dark .hero { background: rgba(30, 30, 30, 0.8); }
        body.dark .hero p { color: #aaa; }
        body.dark .btn-secondary { background: #333; color: #e0e0e0; }
        body.dark .wipe-section { background: #1e1e1e; border-top: 1px solid #333; }
        body.dark .day { border-color: #444; }
        body.dark .day.active { background: #333; border-color: #555; }
        body.dark .socials { background: #1e1e1e; border-top: 1px solid #333; }
        body.dark .social-card { border-right: 1px solid #333; }
        body.dark .social-card:hover { background: #2a2a2a; }
        body.dark .social-card p { color: #e0e0e0; }
        body.dark .status-section { background: #121212; }
        body.dark .status-card { background: #1e1e1e; box-shadow: 0 4px 15px rgba(0,0,0,0.3); }
        body.dark .status-card .label { color: #888; }

        /* Навигация */
        nav { display: flex; justify-content: space-between; align-items: center; padding: 15px 40px; background: rgba(255,255,255,0.9); border-bottom: 1px solid #eee; transition: background 0.3s; backdrop-filter: blur(5px); position: sticky; top: 0; z-index: 100; }
        .nav-left { display: flex; align-items: center; gap: 30px; }
        .logo { font-weight: bold; font-size: 1.5em; color: #e74c3c; }
        .nav-links { display: flex; gap: 20px; list-style: none; }
        .nav-links a { text-decoration: none; color: #555; font-weight: 500; font-size: 0.95em; display: flex; align-items: center; gap: 5px; }
        .nav-links a:hover { color: #e74c3c; }
        
        /* Кнопка темы */
        .theme-toggle { background: none; border: none; font-size: 1.5em; cursor: pointer; padding: 5px 10px; border-radius: 50%; transition: background 0.3s; }
        .theme-toggle:hover { background: rgba(0,0,0,0.1); }
        body.dark .theme-toggle:hover { background: rgba(255,255,255,0.1); }

        /* Главный блок с фоновой картинкой */
        .hero { text-align: center; padding: 120px 20px 80px; background: linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), url('https://images.unsplash.com/photo-1507525428034-b723cf961d3e?ixlib=rb-4.0.3&auto=format&fit=crop&w=1920&q=80'); background-size: cover; background-position: center; color: #fff; transition: background 0.3s; }
        .hero h1 { font-size: 3em; margin-bottom: 15px; display: flex; align-items: center; justify-content: center; gap: 10px; text-shadow: 2px 2px 4px rgba(0,0,0,0.7); }
        .hero p { font-size: 1.2em; color: #eee; max-width: 600px; margin: 0 auto 30px; text-shadow: 1px 1px 3px rgba(0,0,0,0.7); }
        .hero-buttons { display: flex; gap: 15px; justify-content: center; }
        .btn-primary { background: #1a1a1a; color: #fff; padding: 12px 30px; border-radius: 25px; text-decoration: none; font-weight: 600; }
        .btn-secondary { background: #f0f0f0; color: #1a1a1a; padding: 12px 30px; border-radius: 25px; text-decoration: none; font-weight: 600; }
        .btn-primary:hover { background: #333; }
        .btn-secondary:hover { background: #e0e0e0; }

        /* Блок с датами */
        .wipe-section { background: #fff; padding: 40px; border-top: 1px solid #eee; transition: background 0.3s; }
        .wipe-container { display: flex; align-items: center; gap: 30px; max-width: 1000px; margin: 0 auto; }
        .wipe-text { flex: 1; color: #666; font-size: 0.9em; }
        .wipe-days { display: flex; gap: 10px; }
        .day { display: flex; flex-direction: column; align-items: center; padding: 10px 15px; border: 1px solid #ddd; border-radius: 10px; min-width: 60px; }
        .day.active { background: #f0f0f0; border-color: #ccc; }
        .day-name { font-size: 0.7em; text-transform: uppercase; color: #888; }
        .day-num { font-size: 1.2em; font-weight: bold; }
        .dot { width: 8px; height: 8px; border-radius: 50%; margin-top: 5px; }
        .dot.red { background: #e74c3c; }
        .dot.blue { background: #3498db; }

        /* Блок соцсетей */
        .socials { display: grid; grid-template-columns: repeat(2, 1fr); background: #fff; border-top: 1px solid #eee; transition: background 0.3s; }
        .social-card { padding: 40px; text-align: center; border-right: 1px solid #eee; text-decoration: none; color: inherit; transition: background 0.3s; }
        .social-card:last-child { border-right: none; }
        .social-card:hover { background: #f9f9f9; }
        .social-card h3 { font-size: 1.5em; margin-bottom: 5px; }
        .social-card p { color: #1a1a1a; font-size: 0.9em; font-weight: 600; }

        /* Блок статуса сервера */
        .status-section { padding: 60px 20px; text-align: center; background: #f9f9f9; transition: background 0.3s; }
        .status-card { background: #fff; padding: 30px; border-radius: 15px; max-width: 400px; margin: 0 auto; box-shadow: 0 4px 15px rgba(0,0,0,0.05); transition: background 0.3s; }
        .status-card .players { font-size: 3em; font-weight: bold; color: #27ae60; }
        .status-card .label { color: #888; font-size: 0.9em; text-transform: uppercase; letter-spacing: 1px; }
        .status-card .status-text { margin-bottom: 10px; font-weight: 500; }
    </style>
</head>
<body>

    <!-- Навигация -->
    <nav>
        <div class="nav-left">
            <div class="logo">🧀 Cheesy Rust</div>
            <ul class="nav-links">
                <li><a href="#">🏠 Home</a></li>
                <li><a href="#status">🖥️ Servers</a></li>
            </ul>
        </div>
        <button class="theme-toggle" onclick="toggleTheme()" id="themeBtn">🌙</button>
    </nav>

    <!-- Главный блок -->
    <section class="hero">
        <h1>Welcome to 🧀 Cheesy Rust</h1>
        <p>Кастомные плагины, стабильные серверы и постоянная поддержка — опыт Rust, который растёт и улучшается.</p>
        <div class="hero-buttons">
            <a href="#status" class="btn-primary">Выбрать сервер</a>
            <a href="#" class="btn-secondary">Купить VIP</a>
        </div>
    </section>

    <!-- Блок с датами вайпов -->
    <section class="wipe-section">
        <div class="wipe-container">
            <div class="wipe-text">
                Здесь вы можете узнать, когда и на каком сервере произойдёт вайп.
            </div>
            <div class="wipe-days">
                <div class="day active">
                    <span class="day-name">SU</span>
                    <span class="day-num">11</span>
                </div>
                <div class="day">
                    <span class="day-name">MO</span>
                    <span class="day-num">12</span>
                </div>
                <div class="day">
                    <span class="day-name">TU</span>
                    <span class="day-num">13</span>
                    <span class="dot red"></span>
                </div>
                <div class="day">
                    <span class="day-name">WE</span>
                    <span class="day-num">14</span>
                </div>
                <div class="day">
                    <span class="day-name">TH</span>
                    <span class="day-num">15</span>
                </div>
                <div class="day">
                    <span class="day-name">FR</span>
                    <span class="day-num">16</span>
                </div>
                <div class="day">
                    <span class="day-name">SA</span>
                    <span class="day-num">17</span>
                    <span class="dot blue"></span>
                </div>
                <div class="day">
                    <span class="day-name">SU</span>
                    <span class="day-num">18</span>
                </div>
            </div>
        </div>
    </section>

    <!-- Блок соцсетей -->
    <section class="socials">
        <a href="https://t.me/CheesyRust" target="_blank" class="social-card">
            <h3>Telegram</h3>
            <p>Присоединиться</p>
        </a>
        <a href="https://discord.gg/2CmS5FfnC" target="_blank" class="social-card">
            <h3>Discord</h3>
            <p>Присоединиться</p>
        </a>
    </section>

    <!-- Блок статуса сервера -->
    <section class="status-section" id="status">
        <div class="status-card">
            <div class="status-text">Сервер работает</div>
            <div class="players" id="player-count">0</div>
            <div class="label">игроков онлайн</div>
        </div>
        <div style="margin-top: 20px;">
            <a href="https://drive.google.com/file/d/1-m0nKPdTAx8X1m70qh0w-6MCTf1r_pni/view?usp=sharing" class="btn-primary" target="_blank">Скачать клиент</a>
            <a href="mailto:cheesyrust280@gmail.com" class="btn-secondary">Связаться с админом</a>
        </div>
    </section>

    <script>
        // Онлайн-заглушка
        document.getElementById('player-count').innerText = '0';

        // Переключение темы
        function toggleTheme() {
            const body = document.body;
            const btn = document.getElementById('themeBtn');
            body.classList.toggle('dark');
            if (body.classList.contains('dark')) {
                btn.innerText = '☀️';
                localStorage.setItem('theme', 'dark');
            } else {
                btn.innerText = '🌙';
                localStorage.setItem('theme', 'light');
            }
        }

        // Запоминаем выбор темы при загрузке
        if (localStorage.getItem('theme') === 'dark') {
            document.body.classList.add('dark');
            document.getElementById('themeBtn').innerText = '☀️';
        }
    </script>
</body>
</html>
