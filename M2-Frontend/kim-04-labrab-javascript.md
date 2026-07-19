# Лабораторная работа

## Тема: Javascript и визуализация данных

**Цель работы:** Освоить навыки работы с DOM-элементами, обработки событий, загрузки и визуализации данных с использованием JavaScript.

**Продолжительность:** 6 академических часов

**Необходимые инструменты:** Текстовый редактор (VS Code), браузер (Google Chrome), Python с FastAPI (для заданий 8-10).

---

## Подготовка окружения

<details>
<summary><b>Настройки окружения</b></summary>

### Настройка FastAPI сервера (для заданий 8-10)
server.py:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from fastapi.responses import FileResponse
import os
```

app = FastAPI()

### Разрешаем CORS для разработки

```
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/")
async def root():
    return {"message": "FastAPI сервер запущен"}

@app.get("/api/data/txt")
async def get_txt_data():
    """Возвращает содержимое текстового файла"""
    file_path = "data/sample.txt"
    if os.path.exists(file_path):
        with open(file_path, "r", encoding="utf-8") as f:
            return {"content": f.read()}
    return {"error": "Файл не найден"}

@app.get("/api/data/csv")
async def get_csv_data():
    """Возвращает содержимое CSV файла"""
    file_path = "data/sample.csv"
    if os.path.exists(file_path):
        with open(file_path, "r", encoding="utf-8") as f:
            import csv
            reader = csv.DictReader(f)
            return {"data": list(reader)}
    return {"error": "Файл не найден"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="127.0.0.1", port=8000)
data/sample.txt:
```

```
csv
id,name,age,city,score
1,Анна,25,Москва,4.8
2,Борис,30,Санкт-Петербург,4.2
3,Виктория,22,Казань,4.9
4,Глеб,28,Новосибирск,3.7
5,Дарья,35,Екатеринбург,4.5
6,Евгений,27,Москва,4.1
7,Жанна,24,Санкт-Петербург,4.6
8,Иван,31,Казань,3.9
```

### Запуск сервера:

```
bash
pip install fastapi uvicorn
uvicorn server:app --reload
```
</details>


# Задание 1. Доступ к DOM-элементам и их стилизация

**Описание:** Создайте страницу с несколькими элементами и измените их свойства через JavaScript.

**Требования:**

- Создайте блок `<div>` с текстом и кнопку.
- При загрузке страницы измените цвет фона, размер шрифта и цвет текста блока.
- Добавьте обработчик, который скрывает/показывает блок при клике на кнопку.
- Измените стиль элемента при наведении мыши.

<details>
<summary><b>Пример решения</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 1</title>
    <style>
        #myBlock {
            padding: 20px;
            margin: 10px 0;
            border: 2px solid #333;
            transition: all 0.3s;
        }
        .highlight {
            background-color: yellow !important;
        }
    </style>
</head>
<body>
    <h1>Задание 1: Работа с DOM</h1>
    <div id="myBlock">Это блок для стилизации</div>
    <button id="toggleBtn">Скрыть/Показать</button>
    <button id="highlightBtn">Подсветить</button>

    <script>
        // Получение элементов
        const block = document.getElementById('myBlock');
        const toggleBtn = document.getElementById('toggleBtn');
        const highlightBtn = document.getElementById('highlightBtn');

        // Стилизация при загрузке
        window.addEventListener('DOMContentLoaded', () => {
            block.style.backgroundColor = '#f0f8ff';
            block.style.color = '#2c3e50';
            block.style.fontSize = '18px';
            block.style.fontWeight = 'bold';
            block.style.borderRadius = '8px';
        });

        // Скрытие/показ блока
        toggleBtn.addEventListener('click', () => {
            if (block.style.display === 'none') {
                block.style.display = 'block';
                toggleBtn.textContent = 'Скрыть';
            } else {
                block.style.display = 'none';
                toggleBtn.textContent = 'Показать';
            }
        });

        // Добавление/удаление класса
        highlightBtn.addEventListener('click', () => {
            block.classList.toggle('highlight');
        });

        // События наведения
        block.addEventListener('mouseenter', () => {
            block.style.boxShadow = '0 4px 8px rgba(0,0,0,0.2)';
            block.style.transform = 'scale(1.02)';
        });

        block.addEventListener('mouseleave', () => {
            block.style.boxShadow = 'none';
            block.style.transform = 'scale(1)';
        });
    </script>
</body>
</html>
```
</details>

# Задание 2. Обработка групп элементов по классам

**Описание:** Создайте несколько элементов с одинаковым классом и выполните с ними групповые операции.

**Требования:**

- Создайте 5 карточек с классом `card`.
- При клике на любую карточку она должна менять цвет.
- Добавьте кнопку "Сбросить", которая возвращает все карточки в исходное состояние.
- Реализуйте подсчет количества кликов по каждой карточке.

<details>
<summary><b>Пример решения</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 2</title>
    <style>
        .card {
            display: inline-block;
            width: 120px;
            height: 120px;
            margin: 10px;
            background-color: #3498db;
            color: white;
            text-align: center;
            line-height: 120px;
            font-size: 18px;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
            user-select: none;
        }
        .card.active {
            background-color: #e74c3c;
            transform: scale(1.1);
        }
        .card:hover {
            transform: scale(1.05);
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
        }
        .stats {
            margin-top: 20px;
            padding: 15px;
            background-color: #f8f9fa;
            border-radius: 8px;
        }
        .stats span {
            margin-right: 15px;
        }
    </style>
</head>
<body>
    <h1>Задание 2: Групповые операции</h1>
    
    <div id="cardContainer"></div>
    <button id="resetBtn">Сбросить все</button>
    
    <div class="stats">
        <h3>Статистика кликов:</h3>
        <div id="statsContainer"></div>
    </div>

    <script>
        const cardContainer = document.getElementById('cardContainer');
        const statsContainer = document.getElementById('statsContainer');
        const resetBtn = document.getElementById('resetBtn');

        // Создание карточек
        const colors = ['#3498db', '#2ecc71', '#f39c12', '#9b59b6', '#e67e22'];
        const names = ['Карточка 1', 'Карточка 2', 'Карточка 3', 'Карточка 4', 'Карточка 5'];
        let clickCounts = new Array(5).fill(0);

        function createCards() {
            names.forEach((name, index) => {
                const card = document.createElement('div');
                card.className = 'card';
                card.textContent = name;
                card.style.backgroundColor = colors[index];
                card.dataset.index = index;
                cardContainer.appendChild(card);
            });
        }

        // Обновление статистики
        function updateStats() {
            statsContainer.innerHTML = '';
            clickCounts.forEach((count, index) => {
                const span = document.createElement('span');
                span.textContent = `${names[index]}: ${count}`;
                statsContainer.appendChild(span);
            });
        }

        // Обработка кликов по карточкам (делегирование)
        cardContainer.addEventListener('click', (event) => {
            const card = event.target.closest('.card');
            if (!card) return;

            const index = parseInt(card.dataset.index);
            card.classList.toggle('active');
            clickCounts[index]++;
            updateStats();
        });

        // Сброс
        resetBtn.addEventListener('click', () => {
            const cards = document.querySelectorAll('.card');
            cards.forEach(card => card.classList.remove('active'));
            clickCounts.fill(0);
            updateStats();
        });

        // Инициализация
        createCards();
        updateStats();
    </script>
</body>
</html>
```
</details>

# Задание 3. Обработка события onclick на гиперссылке

**Описание:** Создайте несколько гиперссылок и обработайте клики по ним.

**Требования:**

- Создайте 3-4 ссылки с разными URL.
- При клике на ссылку предотвращайте переход по ней.
- Выводите информацию о клике в консоль и на страницу.
- Реализуйте подсчет кликов по каждой ссылке.

<details>
<summary><b>Пример решения</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 3</title>
    <style>
        .link-item {
            padding: 10px;
            margin: 5px 0;
            background-color: #f8f9fa;
            border-radius: 4px;
            cursor: pointer;
        }
        .link-item:hover {
            background-color: #e9ecef;
        }
        .link-item a {
            text-decoration: none;
            color: #3498db;
            font-weight: bold;
        }
        .link-item a:hover {
            text-decoration: underline;
        }
        .link-stats {
            margin-left: 20px;
            color: #666;
            font-size: 14px;
        }
        #logContainer {
            margin-top: 20px;
            padding: 15px;
            background-color: #f8f9fa;
            border: 1px solid #ddd;
            border-radius: 4px;
            max-height: 200px;
            overflow-y: auto;
            font-family: monospace;
            font-size: 14px;
        }
        #logContainer div {
            padding: 3px 0;
            border-bottom: 1px solid #eee;
        }
    </style>
</head>
<body>
    <h1>Задание 3: Обработка ссылок</h1>
    
    <div id="linksContainer"></div>
    <button id="clearLogBtn">Очистить лог</button>
    
    <h3>Лог кликов:</h3>
    <div id="logContainer"></div>

    <script>
        const links = [
            { text: 'Google', url: 'https://google.com' },
            { text: 'GitHub', url: 'https://github.com' },
            { text: 'MDN', url: 'https://developer.mozilla.org' },
            { text: 'YouTube', url: 'https://youtube.com' }
        ];

        const linksContainer = document.getElementById('linksContainer');
        const logContainer = document.getElementById('logContainer');
        const clearLogBtn = document.getElementById('clearLogBtn');

        let clickLog = [];
        let clickStats = {};

        // Функция добавления записи в лог
        function addLogEntry(text, url) {
            const timestamp = new Date().toLocaleTimeString();
            const entry = `[${timestamp}] Клик по ссылке: "${text}" (${url})`;
            clickLog.push(entry);
            
            // Обновление статистики
            clickStats[text] = (clickStats[text] || 0) + 1;
            
            renderLog();
            renderStats();
        }

        // Отображение лога
        function renderLog() {
            logContainer.innerHTML = '';
            clickLog.slice(-20).forEach(entry => {
                const div = document.createElement('div');
                div.textContent = entry;
                logContainer.appendChild(div);
            });
            logContainer.scrollTop = logContainer.scrollHeight;
        }

        // Отображение статистики
        function renderStats() {
            const statElements = document.querySelectorAll('.link-stats');
            statElements.forEach(el => {
                const text = el.dataset.linkText;
                el.textContent = `Кликов: ${clickStats[text] || 0}`;
            });
        }

        // Создание ссылок
        links.forEach((link, index) => {
            const wrapper = document.createElement('div');
            wrapper.className = 'link-item';
            
            const a = document.createElement('a');
            a.href = link.url;
            a.textContent = link.text;
            a.target = '_blank';
            
            const stats = document.createElement('span');
            stats.className = 'link-stats';
            stats.dataset.linkText = link.text;
            stats.textContent = 'Кликов: 0';
            
            wrapper.appendChild(a);
            wrapper.appendChild(stats);
            linksContainer.appendChild(wrapper);

            // Обработка клика
            a.addEventListener('click', (event) => {
                event.preventDefault();
                addLogEntry(link.text, link.url);
            });
        });

        // Очистка лога
        clearLogBtn.addEventListener('click', () => {
            clickLog = [];
            clickStats = {};
            renderLog();
            renderStats();
        });
    </script>
</body>
</html>
```
</details>

# Задание 4. Обработка события onchange на select

**Описание:** Создайте выпадающий список и обрабатывайте изменение его значения.

**Требования:**

- Создайте `<select>` с вариантами выбора (например, города или цвета).
- При изменении выбранного значения меняйте фон страницы или стиль блока.
- Отображайте выбранное значение на странице.

<details>
<summary><b>Пример решения</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 4</title>
    <style>
        .container {
            max-width: 600px;
            margin: 50px auto;
            padding: 30px;
            background-color: white;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
            transition: all 0.5s;
        }
        .color-preview {
            width: 200px;
            height: 200px;
            margin: 20px auto;
            border: 2px solid #ddd;
            border-radius: 8px;
            transition: background-color 0.5s;
            background-color: #3498db;
        }
        .info {
            text-align: center;
            font-size: 18px;
            margin: 10px 0;
        }
        select {
            padding: 10px 15px;
            font-size: 16px;
            border: 2px solid #ddd;
            border-radius: 4px;
            width: 100%;
            max-width: 300px;
            margin: 10px auto;
            display: block;
            cursor: pointer;
        }
        select:hover {
            border-color: #3498db;
        }
        select:focus {
            outline: none;
            border-color: #2980b9;
        }
        .history {
            margin-top: 20px;
            padding: 15px;
            background-color: #f8f9fa;
            border-radius: 4px;
            max-height: 150px;
            overflow-y: auto;
            font-size: 14px;
        }
        .history-item {
            padding: 3px 0;
            border-bottom: 1px solid #eee;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Задание 4: Выбор цвета</h1>
        
        <label for="colorSelect">Выберите цвет:</label>
        <select id="colorSelect">
            <option value="#3498db">Синий</option>
            <option value="#2ecc71">Зеленый</option>
            <option value="#e74c3c">Красный</option>
            <option value="#f39c12">Оранжевый</option>
            <option value="#9b59b6">Фиолетовый</option>
            <option value="#ecf0f1">Светлый</option>
            <option value="#2c3e50">Темный</option>
        </select>
        
        <div class="color-preview" id="colorPreview"></div>
        <div class="info" id="selectedInfo">Выбран: Синий (#3498db)</div>
        
        <h3>История выборов:</h3>
        <div class="history" id="historyContainer"></div>
    </div>

    <script>
        const select = document.getElementById('colorSelect');
        const preview = document.getElementById('colorPreview');
        const info = document.getElementById('selectedInfo');
        const historyContainer = document.getElementById('historyContainer');
        
        let history = [];
        let colorNames = {
            '#3498db': 'Синий',
            '#2ecc71': 'Зеленый',
            '#e74c3c': 'Красный',
            '#f39c12': 'Оранжевый',
            '#9b59b6': 'Фиолетовый',
            '#ecf0f1': 'Светлый',
            '#2c3e50': 'Темный'
        };

        function updateUI(color, name) {
            preview.style.backgroundColor = color;
            info.textContent = `Выбран: ${name} (${color})`;
            document.querySelector('.container').style.backgroundColor = 
                color === '#ecf0f1' ? '#2c3e50' : 
                color === '#2c3e50' ? '#ecf0f1' : 
                '#ffffff';
            document.querySelector('.container').style.color = 
                color === '#ecf0f1' || color === '#2c3e50' ? '#ffffff' : '#2c3e50';
        }

        function addHistoryEntry(color, name) {
            const timestamp = new Date().toLocaleTimeString();
            history.push({ color, name, timestamp });
            
            if (history.length > 20) {
                history.shift();
            }
            
            historyContainer.innerHTML = '';
            history.slice().reverse().forEach(entry => {
                const div = document.createElement('div');
                div.className = 'history-item';
                div.innerHTML = `
                    <span style="display:inline-block;width:20px;height:20px;
                                background-color:${entry.color};
                                border-radius:4px;vertical-align:middle;margin-right:10px;"></span>
                    ${entry.name} (${entry.color}) - ${entry.timestamp}
                `;
                historyContainer.appendChild(div);
            });
        }

        select.addEventListener('change', (event) => {
            const color = event.target.value;
            const name = colorNames[color] || 'Неизвестный цвет';
            updateUI(color, name);
            addHistoryEntry(color, name);
        });

        // Инициализация
        updateUI('#3498db', 'Синий');
        addHistoryEntry('#3498db', 'Синий');
    </script>
</body>
</html>
```
</details>

# Задание 5. Обработка onfocus и onblur на текстовых полях

**Описание:** Создайте текстовые поля и обрабатывайте события получения и потери фокуса.

**Требования:**

- Создайте несколько текстовых полей (`<input type="text">` и `<textarea>`).
- При получении фокуса подсвечивайте поле (изменяйте рамку или фон).
- При потере фокуса проверяйте содержимое поля.
- Добавьте валидацию (например, минимальная длина).

<details>
<summary><b>Пример решения</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 5</title>
    <style>
        .form-group {
            margin-bottom: 20px;
        }
        .form-group label {
            display: block;
            font-weight: bold;
            margin-bottom: 5px;
            color: #2c3e50;
        }
        .form-group input,
        .form-group textarea {
            width: 100%;
            padding: 10px;
            border: 2px solid #ddd;
            border-radius: 4px;
            font-size: 16px;
            transition: all 0.3s;
            box-sizing: border-box;
        }
        .form-group input:focus,
        .form-group textarea:focus {
            outline: none;
            border-color: #3498db;
            box-shadow: 0 0 8px rgba(52, 152, 219, 0.3);
        }
        .form-group input.focused,
        .form-group textarea.focused {
            background-color: #f0f8ff;
            border-color: #2ecc71;
        }
        .form-group input.error,
        .form-group textarea.error {
            border-color: #e74c3c;
            background-color: #fff5f5;
        }
        .form-group input.valid,
        .form-group textarea.valid {
            border-color: #2ecc71;
            background-color: #f0fff4;
        }
        .form-group .hint {
            font-size: 12px;
            color: #999;
            margin-top: 5px;
            display: block;
        }
        .form-group .error-message {
            color: #e74c3c;
            font-size: 12px;
            display: none;
            margin-top: 5px;
        }
        .form-group .error-message.show {
            display: block;
        }
        #focusLog {
            margin-top: 20px;
            padding: 15px;
            background-color: #f8f9fa;
            border-radius: 4px;
            max-height: 150px;
            overflow-y: auto;
            font-family: monospace;
            font-size: 14px;
        }
        #focusLog div {
            padding: 3px 0;
            border-bottom: 1px solid #eee;
        }
        .submit-btn {
            padding: 12px 30px;
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 4px;
            font-size: 16px;
            cursor: pointer;
        }
        .submit-btn:hover {
            background-color: #2980b9;
        }
    </style>
</head>
<body>
    <h1>Задание 5: Фокус и валидация</h1>
    
    <form id="myForm">
        <div class="form-group">
            <label for="username">Имя пользователя (минимум 3 символа):</label>
            <input type="text" id="username" name="username" placeholder="Введите имя">
            <span class="hint">Минимум 3 символа, только буквы</span>
            <span class="error-message" id="usernameError">Имя должно содержать минимум 3 буквы</span>
        </div>
        
        <div class="form-group">
            <label for="email">Email:</label>
            <input type="text" id="email" name="email" placeholder="example@mail.ru">
            <span class="hint">Введите корректный email</span>
            <span class="error-message" id="emailError">Введите корректный email</span>
        </div>
        
        <div class="form-group">
            <label for="comment">Комментарий:</label>
            <textarea id="comment" name="comment" rows="4" placeholder="Введите ваш комментарий..."></textarea>
            <span class="hint">Минимум 10 символов</span>
            <span class="error-message" id="commentError">Комментарий должен быть минимум 10 символов</span>
        </div>
        
        <button type="submit" class="submit-btn">Отправить</button>
    </form>
    
    <h3>Лог фокуса:</h3>
    <div id="focusLog"></div>

    <script>
        const username = document.getElementById('username');
        const email = document.getElementById('email');
        const comment = document.getElementById('comment');
        const logContainer = document.getElementById('focusLog');
        const form = document.getElementById('myForm');
        
        const errors = {
            username: document.getElementById('usernameError'),
            email: document.getElementById('emailError'),
            comment: document.getElementById('commentError')
        };

        let focusLog = [];

        function addLogEntry(message) {
            const timestamp = new Date().toLocaleTimeString();
            focusLog.push(`[${timestamp}] ${message}`);
            
            if (focusLog.length > 50) {
                focusLog.shift();
            }
            
            logContainer.innerHTML = '';
            focusLog.slice().reverse().forEach(entry => {
                const div = document.createElement('div');
                div.textContent = entry;
                logContainer.appendChild(div);
            });
        }

        function validateField(field, validator, errorElement) {
            if (validator(field.value)) {
                field.classList.remove('error');
                field.classList.add('valid');
                errorElement.classList.remove('show');
                return true;
            } else {
                field.classList.remove('valid');
                field.classList.add('error');
                errorElement.classList.add('show');
                return false;
            }
        }

        // Валидаторы
        const validators = {
            username: (value) => /^[а-яА-Яa-zA-Z]{3,}$/.test(value),
            email: (value) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value),
            comment: (value) => value.trim().length >= 10
        };

        // Обработка фокуса
        function handleFocus(event) {
            const field = event.target;
            field.classList.add('focused');
            addLogEntry(`Фокус на ${field.name || field.id}`);
        }

        // Обработка потери фокуса
        function handleBlur(event) {
            const field = event.target;
            field.classList.remove('focused');
            
            const validator = validators[field.id];
            const errorElement = errors[field.id];
            
            if (validator && field.value.trim() !== '') {
                validateField(field, validator, errorElement);
                const isValid = validator(field.value);
                addLogEntry(`Потеря фокуса с ${field.name || field.id}: ${isValid ? '✓ OK' : '✗ Ошибка'}`);
            } else {
                addLogEntry(`Потеря фокуса с ${field.name || field.id}`);
            }
        }

        // Добавление обработчиков
        [username, email, comment].forEach(field => {
            field.addEventListener('focus', handleFocus);
            field.addEventListener('blur', handleBlur);
        });

        // Валидация при вводе
        username.addEventListener('input', () => {
            if (username.value.trim() !== '') {
                validateField(username, validators.username, errors.username);
            }
        });
        
        email.addEventListener('input', () => {
            if (email.value.trim() !== '') {
                validateField(email, validators.email, errors.email);
            }
        });
        
        comment.addEventListener('input', () => {
            if (comment.value.trim() !== '') {
                validateField(comment, validators.comment, errors.comment);
            }
        });

        // Отправка формы
        form.addEventListener('submit', (event) => {
            event.preventDefault();
            
            const isUsernameValid = validateField(username, validators.username, errors.username);
            const isEmailValid = validateField(email, validators.email, errors.email);
            const isCommentValid = validateField(comment, validators.comment, errors.comment);
            
            if (isUsernameValid && isEmailValid && isCommentValid) {
                addLogEntry('✅ Форма успешно отправлена!');
                alert('Форма отправлена!');
            } else {
                addLogEntry('❌ Ошибка валидации формы');
                alert('Пожалуйста, исправьте ошибки в форме.');
            }
        });
    </script>
</body>
</html>
```
</details>


