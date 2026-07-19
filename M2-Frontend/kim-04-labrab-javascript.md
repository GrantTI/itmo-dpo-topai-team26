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

# Задание 6. Изменение типа поля input (text ↔ password)

**Описание:** Создайте поле для ввода пароля с возможностью переключения видимости.

**Требования:**

- Создайте поле `<input type="password">`.
- Добавьте кнопку/чекбокс для переключения типа поля на text и обратно.
- Реализуйте индикатор сложности пароля.
- Добавьте визуальную обратную связь при переключении.

<details>
<summary><b>Пример решения</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 6</title>
    <style>
        .password-container {
            max-width: 400px;
            margin: 50px auto;
            padding: 30px;
            background-color: #f8f9fa;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }
        .password-wrapper {
            position: relative;
            display: flex;
            align-items: center;
        }
        .password-wrapper input {
            flex: 1;
            padding: 12px 45px 12px 15px;
            border: 2px solid #ddd;
            border-radius: 4px;
            font-size: 16px;
            transition: all 0.3s;
        }
        .password-wrapper input:focus {
            outline: none;
            border-color: #3498db;
            box-shadow: 0 0 8px rgba(52, 152, 219, 0.3);
        }
        .toggle-btn {
            position: absolute;
            right: 10px;
            background: none;
            border: none;
            cursor: pointer;
            font-size: 20px;
            padding: 5px;
        }
        .toggle-btn:hover {
            transform: scale(1.1);
        }
        .toggle-btn:active {
            transform: scale(0.9);
        }
        .strength-meter {
            margin-top: 15px;
            height: 8px;
            background-color: #ddd;
            border-radius: 4px;
            overflow: hidden;
            transition: all 0.3s;
        }
        .strength-meter .fill {
            height: 100%;
            width: 0%;
            transition: all 0.5s;
            border-radius: 4px;
        }
        .strength-text {
            margin-top: 5px;
            font-size: 14px;
            color: #666;
            text-align: center;
        }
        .requirements {
            margin-top: 15px;
            padding: 10px;
            background-color: #fff;
            border-radius: 4px;
            font-size: 14px;
        }
        .requirements .req {
            padding: 3px 0;
            color: #999;
        }
        .requirements .req.done {
            color: #2ecc71;
        }
        .requirements .req.fail {
            color: #e74c3c;
        }
        .status-icon {
            margin-right: 8px;
        }
    </style>
</head>
<body>
    <div class="password-container">
        <h1>Задание 6: Переключение пароля</h1>
        
        <div class="password-wrapper">
            <input type="password" id="passwordInput" placeholder="Введите пароль">
            <button class="toggle-btn" id="toggleBtn">👁️</button>
        </div>
        
        <div class="strength-meter">
            <div class="fill" id="strengthFill"></div>
        </div>
        <div class="strength-text" id="strengthText">Введите пароль</div>
        
        <div class="requirements">
            <div class="req" id="reqLength">
                <span class="status-icon">❌</span> Минимум 8 символов
            </div>
            <div class="req" id="reqLower">
                <span class="status-icon">❌</span> Строчная буква
            </div>
            <div class="req" id="reqUpper">
                <span class="status-icon">❌</span> Заглавная буква
            </div>
            <div class="req" id="reqDigit">
                <span class="status-icon">❌</span> Цифра
            </div>
            <div class="req" id="reqSpecial">
                <span class="status-icon">❌</span> Специальный символ
            </div>
        </div>
    </div>

    <script>
        const passwordInput = document.getElementById('passwordInput');
        const toggleBtn = document.getElementById('toggleBtn');
        const strengthFill = document.getElementById('strengthFill');
        const strengthText = document.getElementById('strengthText');
        
        const reqs = {
            length: document.getElementById('reqLength'),
            lower: document.getElementById('reqLower'),
            upper: document.getElementById('reqUpper'),
            digit: document.getElementById('reqDigit'),
            special: document.getElementById('reqSpecial')
        };

        let isPasswordVisible = false;

        // Переключение видимости
        toggleBtn.addEventListener('click', () => {
            isPasswordVisible = !isPasswordVisible;
            passwordInput.type = isPasswordVisible ? 'text' : 'password';
            toggleBtn.textContent = isPasswordVisible ? '🙈' : '👁️';
            
            // Анимация
            passwordInput.style.transition = 'all 0.3s';
            passwordInput.style.transform = 'scale(1.02)';
            setTimeout(() => {
                passwordInput.style.transform = 'scale(1)';
            }, 200);
        });

        // Проверка сложности пароля
        function checkPasswordStrength(password) {
            const checks = {
                length: password.length >= 8,
                lower: /[a-zа-я]/.test(password),
                upper: /[A-ZА-Я]/.test(password),
                digit: /\d/.test(password),
                special: /[!@#$%^&*()_+\-=\[\]{};:'",.<>/?]/.test(password)
            };

            // Обновление требований
            for (const [key, value] of Object.entries(checks)) {
                const req = reqs[key];
                if (req) {
                    const icon = req.querySelector('.status-icon');
                    if (value) {
                        req.classList.add('done');
                        req.classList.remove('fail');
                        icon.textContent = '✅';
                    } else {
                        req.classList.remove('done');
                        req.classList.add('fail');
                        icon.textContent = '❌';
                    }
                }
            }

            // Расчет силы пароля
            let score = 0;
            for (const [key, value] of Object.entries(checks)) {
                if (value) score++;
            }

            const strengthLevels = [
                { min: 0, label: 'Очень слабый', color: '#e74c3c', width: '20%' },
                { min: 1, label: 'Слабый', color: '#e67e22', width: '40%' },
                { min: 2, label: 'Средний', color: '#f39c12', width: '60%' },
                { min: 3, label: 'Хороший', color: '#2ecc71', width: '80%' },
                { min: 4, label: 'Сильный', color: '#2ecc71', width: '100%' },
                { min: 5, label: 'Очень сильный', color: '#27ae60', width: '100%' }
            ];

            let strength = strengthLevels[0];
            for (const level of strengthLevels) {
                if (score >= level.min && score >= strength.min) {
                    strength = level;
                }
            }

            // Если пароль пустой
            if (password.length === 0) {
                strength = { label: 'Введите пароль', color: '#ddd', width: '0%' };
            }

            return { score, strength, checks };
        }

        // Обновление индикатора
        function updateStrength(password) {
            const result = checkPasswordStrength(password);
            const { strength } = result;

            strengthFill.style.width = strength.width;
            strengthFill.style.backgroundColor = strength.color;
            strengthText.textContent = strength.label;
            strengthText.style.color = strength.color;
        }

        passwordInput.addEventListener('input', (event) => {
            updateStrength(event.target.value);
        });

        // Инициализация
        updateStrength('');
    </script>
</body>
</html>
```
</details>

# Задание 7. Загрузка данных из текстового файла

**Описание:** Реализуйте загрузку и отображение данных из текстового файла.

**Требования:**

- Создайте форму для загрузки файла (`<input type="file">`).
- Реализуйте чтение файла с помощью FileReader.
- Отобразите содержимое файла на странице.
- Добавьте подсчет строк, слов и символов.

<details>
<summary><b>Пример решения</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 7</title>
    <style>
        .upload-container {
            max-width: 800px;
            margin: 50px auto;
            padding: 30px;
            background-color: #f8f9fa;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }
        .drop-zone {
            border: 2px dashed #3498db;
            border-radius: 8px;
            padding: 40px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s;
            margin-bottom: 20px;
        }
        .drop-zone:hover {
            background-color: #f0f8ff;
            border-color: #2980b9;
        }
        .drop-zone.dragover {
            background-color: #e3f2fd;
            border-color: #2ecc71;
        }
        .file-input {
            display: none;
        }
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 15px;
            margin: 20px 0;
        }
        .stat-card {
            padding: 15px;
            background-color: white;
            border-radius: 8px;
            text-align: center;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .stat-card .number {
            font-size: 28px;
            font-weight: bold;
            color: #3498db;
        }
        .stat-card .label {
            font-size: 14px;
            color: #666;
            margin-top: 5px;
        }
        #contentDisplay {
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            max-height: 400px;
            overflow-y: auto;
            font-family: monospace;
            white-space: pre-wrap;
            word-wrap: break-word;
            border: 1px solid #ddd;
            display: none;
        }
        .loading {
            text-align: center;
            padding: 20px;
            color: #666;
        }
        .clear-btn {
            padding: 8px 20px;
            background-color: #e74c3c;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            margin-top: 10px;
        }
        .clear-btn:hover {
            background-color: #c0392b;
        }
    </style>
</head>
<body>
    <div class="upload-container">
        <h1>Задание 7: Загрузка текстового файла</h1>
        
        <div class="drop-zone" id="dropZone">
            <p>Перетащите файл сюда или кликните для выбора</p>
            <p style="font-size: 14px; color: #999;">Поддерживаются: .txt, .log, .md</p>
        </div>
        
        <input type="file" class="file-input" id="fileInput" accept=".txt,.log,.md">
        
        <div class="stats" id="statsContainer" style="display: none;">
            <div class="stat-card">
                <div class="number" id="lineCount">0</div>
                <div class="label">Строк</div>
            </div>
            <div class="stat-card">
                <div class="number" id="wordCount">0</div>
                <div class="label">Слов</div>
            </div>
            <div class="stat-card">
                <div class="number" id="charCount">0</div>
                <div class="label">Символов</div>
            </div>
            <div class="stat-card">
                <div class="number" id="fileSize">0</div>
                <div class="label">Размер (КБ)</div>
            </div>
        </div>
        
        <div id="contentDisplay"></div>
        
        <button class="clear-btn" id="clearBtn" style="display: none;">Очистить</button>
    </div>

    <script>
        const dropZone = document.getElementById('dropZone');
        const fileInput = document.getElementById('fileInput');
        const contentDisplay = document.getElementById('contentDisplay');
        const statsContainer = document.getElementById('statsContainer');
        const clearBtn = document.getElementById('clearBtn');
        
        const lineCount = document.getElementById('lineCount');
        const wordCount = document.getElementById('wordCount');
        const charCount = document.getElementById('charCount');
        const fileSize = document.getElementById('fileSize');

        function readFileContent(file) {
            const reader = new FileReader();
            
            reader.onload = (event) => {
                const content = event.target.result;
                displayContent(content, file.size);
            };
            
            reader.onerror = (event) => {
                alert('Ошибка чтения файла: ' + event.target.error);
            };
            
            reader.readAsText(file, 'UTF-8');
        }

        function displayContent(content, size) {
            // Отображение содержимого
            contentDisplay.textContent = content;
            contentDisplay.style.display = 'block';
            
            // Статистика
            const lines = content.split('\n').filter(line => line.trim() !== '');
            const words = content.match(/\S+/g) || [];
            const chars = content.replace(/\s/g, '');
            
            lineCount.textContent = lines.length;
            wordCount.textContent = words.length;
            charCount.textContent = chars.length;
            fileSize.textContent = (size / 1024).toFixed(2);
            
            statsContainer.style.display = 'grid';
            clearBtn.style.display = 'inline-block';
            
            // Анимация появления
            contentDisplay.style.opacity = '0';
            setTimeout(() => {
                contentDisplay.style.transition = 'opacity 0.5s';
                contentDisplay.style.opacity = '1';
            }, 10);
        }

        function clearContent() {
            contentDisplay.style.display = 'none';
            contentDisplay.textContent = '';
            statsContainer.style.display = 'none';
            clearBtn.style.display = 'none';
            fileInput.value = '';
        }

        // Клик по drop-zone для выбора файла
        dropZone.addEventListener('click', () => {
            fileInput.click();
        });

        // Обработка выбора файла
        fileInput.addEventListener('change', (event) => {
            const file = event.target.files[0];
            if (file) {
                readFileContent(file);
            }
        });

        // Drag and Drop
        dropZone.addEventListener('dragover', (event) => {
            event.preventDefault();
            dropZone.classList.add('dragover');
        });

        dropZone.addEventListener('dragleave', (event) => {
            event.preventDefault();
            dropZone.classList.remove('dragover');
        });

        dropZone.addEventListener('drop', (event) => {
            event.preventDefault();
            dropZone.classList.remove('dragover');
            
            const file = event.dataTransfer.files[0];
            if (file) {
                readFileContent(file);
            }
        });

        // Очистка
        clearBtn.addEventListener('click', clearContent);

        // Загрузка примера через FastAPI (задание 8)
        async function loadExampleData() {
            try {
                const response = await fetch('http://localhost:8000/api/data/txt');
                const data = await response.json();
                if (data.content) {
                    // Создаем Blob для отображения
                    const blob = new Blob([data.content], { type: 'text/plain' });
                    const file = new File([blob], 'sample.txt', { type: 'text/plain' });
                    readFileContent(file);
                }
            } catch (error) {
                console.log('Сервер не запущен, используйте локальные файлы');
            }
        }
    </script>
</body>
</html>
```
</details>

# Задание 8. Загрузка данных из CSV с сервера

**Описание:** Загрузите данные из CSV файла через FastAPI и отобразите их в виде таблицы.

**Требования:**

- Используйте FastAPI для получения данных (готовый сервер из подготовительной части).
- Загрузите данные через `fetch`.
- Отобразите данные в виде HTML-таблицы с заголовками.
- Добавьте поиск и фильтрацию данных.

<details>
<summary><b>Пример решения</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 8</title>
    <style>
        .container {
            max-width: 1200px;
            margin: 50px auto;
            padding: 30px;
            background-color: #f8f9fa;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }
        .controls {
            display: flex;
            gap: 15px;
            margin: 20px 0;
            flex-wrap: wrap;
        }
        .controls input {
            flex: 1;
            min-width: 200px;
            padding: 10px;
            border: 2px solid #ddd;
            border-radius: 4px;
            font-size: 16px;
        }
        .controls input:focus {
            outline: none;
            border-color: #3498db;
        }
        .controls select {
            padding: 10px;
            border: 2px solid #ddd;
            border-radius: 4px;
            font-size: 16px;
            cursor: pointer;
        }
        .table-wrapper {
            overflow-x: auto;
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 14px;
        }
        table thead {
            background-color: #3498db;
            color: white;
        }
        table th {
            padding: 12px 15px;
            text-align: left;
            cursor: pointer;
            user-select: none;
            position: sticky;
            top: 0;
            z-index: 10;
        }
        table th:hover {
            background-color: #2980b9;
        }
        table td {
            padding: 10px 15px;
            border-bottom: 1px solid #eee;
        }
        table tbody tr:hover {
            background-color: #f0f8ff;
        }
        table tbody tr:nth-child(even) {
            background-color: #fafafa;
        }
        .stats-bar {
            display: flex;
            justify-content: space-between;
            padding: 10px 0;
            color: #666;
            font-size: 14px;
        }
        .loading {
            text-align: center;
            padding: 40px;
            color: #666;
        }
        .error {
            text-align: center;
            padding: 40px;
            color: #e74c3c;
            background-color: #fff5f5;
            border-radius: 8px;
        }
        .highlight {
            background-color: #fff3cd !important;
        }
        .score-badge {
            display: inline-block;
            padding: 2px 10px;
            border-radius: 12px;
            font-size: 12px;
            font-weight: bold;
        }
        .score-high {
            background-color: #d4edda;
            color: #155724;
        }
        .score-medium {
            background-color: #fff3cd;
            color: #856404;
        }
        .score-low {
            background-color: #f8d7da;
            color: #721c24;
        }
        #tableContainer {
            min-height: 200px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Задание 8: Загрузка CSV данных</h1>
        
        <div class="controls">
            <input type="text" id="searchInput" placeholder="Поиск по всем полям...">
            <select id="filterColumn">
                <option value="">Все столбцы</option>
            </select>
            <button id="refreshBtn">🔄 Обновить</button>
            <button id="resetBtn">✖️ Сбросить</button>
        </div>
        
        <div class="stats-bar">
            <span id="recordCount">Записей: 0</span>
            <span id="avgScore">Средний балл: -</span>
        </div>
        
        <div class="table-wrapper" id="tableContainer">
            <div class="loading">Загрузка данных...</div>
        </div>
    </div>

    <script>
        const API_URL = 'http://localhost:8000/api/data/csv';
        let originalData = [];
        let filteredData = [];
        let currentSort = { column: null, direction: 'asc' };

        const searchInput = document.getElementById('searchInput');
        const filterColumn = document.getElementById('filterColumn');
        const refreshBtn = document.getElementById('refreshBtn');
        const resetBtn = document.getElementById('resetBtn');
        const tableContainer = document.getElementById('tableContainer');
        const recordCount = document.getElementById('recordCount');
        const avgScore = document.getElementById('avgScore');

        // Загрузка данных
        async function loadData() {
            try {
                tableContainer.innerHTML = '<div class="loading">Загрузка данных...</div>';
                
                const response = await fetch(API_URL);
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                
                const result = await response.json();
                if (result.error) {
                    throw new Error(result.error);
                }
                
                originalData = result.data || [];
                filteredData = [...originalData];
                
                updateFilterOptions();
                applyFiltersAndRender();
                
            } catch (error) {
                console.error('Ошибка загрузки:', error);
                tableContainer.innerHTML = `
                    <div class="error">
                        ❌ Ошибка загрузки данных: ${error.message}<br>
                        <small>Убедитесь, что сервер FastAPI запущен на порту 8000</small>
                    </div>
                `;
            }
        }

        // Обновление опций фильтра
        function updateFilterOptions() {
            if (originalData.length === 0) return;
            
            const columns = Object.keys(originalData[0]);
            filterColumn.innerHTML = '<option value="">Все столбцы</option>';
            
            columns.forEach(col => {
                const option = document.createElement('option');
                option.value = col;
                option.textContent = col.charAt(0).toUpperCase() + col.slice(1);
                filterColumn.appendChild(option);
            });
        }

        // Сортировка
        function sortData(data, column, direction) {
            return [...data].sort((a, b) => {
                let valA = a[column] || '';
                let valB = b[column] || '';
                
                // Определение типа для корректной сортировки
                if (!isNaN(valA) && !isNaN(valB)) {
                    valA = parseFloat(valA);
                    valB = parseFloat(valB);
                } else {
                    valA = valA.toString().toLowerCase();
                    valB = valB.toString().toLowerCase();
                }
                
                if (valA < valB) return direction === 'asc' ? -1 : 1;
                if (valA > valB) return direction === 'asc' ? 1 : -1;
                return 0;
            });
        }

        // Применение фильтров и сортировки
        function applyFiltersAndRender() {
            let data = [...originalData];
            
            // Поиск
            const searchTerm = searchInput.value.toLowerCase().trim();
            if (searchTerm) {
                const column = filterColumn.value;
                data = data.filter(row => {
                    if (column) {
                        const value = String(row[column] || '').toLowerCase();
                        return value.includes(searchTerm);
                    } else {
                        return Object.values(row).some(val => 
                            String(val).toLowerCase().includes(searchTerm)
                        );
                    }
                });
            }
            
            // Сортировка
            if (currentSort.column) {
                data = sortData(data, currentSort.column, currentSort.direction);
            }
            
            filteredData = data;
            renderTable(data);
            updateStats(data);
        }

        // Отображение таблицы
        function renderTable(data) {
            if (data.length === 0) {
                tableContainer.innerHTML = `
                    <div style="text-align:center;padding:40px;color:#666;">
                        <p>📭 Нет данных для отображения</p>
                        <small>Попробуйте изменить параметры поиска</small>
                    </div>
                `;
                return;
            }

            const columns = Object.keys(data[0]);
            
            let html = '<table><thead><tr>';
            columns.forEach(col => {
                const isSorted = currentSort.column === col;
                const direction = isSorted ? currentSort.direction : 'asc';
                const arrow = isSorted ? (direction === 'asc' ? '↑' : '↓') : '↕';
                html += `<th onclick="sortBy('${col}')">${col} ${arrow}</th>`;
            });
            html += '</tr></thead><tbody>';
            
            data.forEach(row => {
                html += '<tr>';
                columns.forEach(col => {
                    let value = row[col] || '';
                    
                    // Форматирование для баллов
                    if (col === 'score' && !isNaN(value)) {
                        const score = parseFloat(value);
                        let badgeClass = 'score-medium';
                        if (score >= 4.5) badgeClass = 'score-high';
                        else if (score < 3.5) badgeClass = 'score-low';
                        html += `<td><span class="score-badge ${badgeClass}">${value}</span></td>`;
                    } else {
                        html += `<td>${value}</td>`;
                    }
                });
                html += '</tr>';
            });
            
            html += '</tbody></table>';
            tableContainer.innerHTML = html;
        }

        // Обновление статистики
        function updateStats(data) {
            recordCount.textContent = `Записей: ${data.length}`;
            
            if (data.length > 0 && 'score' in data[0]) {
                const scores = data.map(row => parseFloat(row.score)).filter(s => !isNaN(s));
                if (scores.length > 0) {
                    const avg = scores.reduce((a, b) => a + b, 0) / scores.length;
                    avgScore.textContent = `Средний балл: ${avg.toFixed(2)}`;
                } else {
                    avgScore.textContent = 'Средний балл: -';
                }
            } else {
                avgScore.textContent = 'Средний балл: -';
            }
        }

        // Сортировка по столбцу (глобальная функция для onclick)
        window.sortBy = function(column) {
            if (currentSort.column === column) {
                currentSort.direction = currentSort.direction === 'asc' ? 'desc' : 'asc';
            } else {
                currentSort.column = column;
                currentSort.direction = 'asc';
            }
            applyFiltersAndRender();
        };

        // Обработчики событий
        searchInput.addEventListener('input', applyFiltersAndRender);
        filterColumn.addEventListener('change', applyFiltersAndRender);
        
        refreshBtn.addEventListener('click', loadData);
        resetBtn.addEventListener('click', () => {
            searchInput.value = '';
            filterColumn.value = '';
            currentSort = { column: null, direction: 'asc' };
            applyFiltersAndRender();
        });

        // Загрузка данных при открытии страницы
        loadData();
    </script>
</body>
</html>
```
</details>

# Задание 9. Визуализация данных с помощью Canvas

**Описание:** Создайте простую визуализацию загруженных данных с использованием Canvas.

**Требования:**

- Загрузите данные (можно использовать данные из CSV).
- Создайте столбчатую диаграмму на Canvas.
- Добавьте подписи осей и значений.
- Реализуйте интерактивность (подсветка столбцов при наведении).

<details>
<summary><b>Пример решения</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 9</title>
    <style>
        .container {
            max-width: 1000px;
            margin: 50px auto;
            padding: 30px;
            background-color: #f8f9fa;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }
        canvas {
            display: block;
            margin: 20px auto;
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
            cursor: pointer;
        }
        .controls {
            display: flex;
            gap: 15px;
            justify-content: center;
            margin: 20px 0;
            flex-wrap: wrap;
        }
        .controls button {
            padding: 10px 20px;
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.3s;
        }
        .controls button:hover {
            background-color: #2980b9;
            transform: scale(1.05);
        }
        .controls button:active {
            transform: scale(0.95);
        }
        .controls button.active {
            background-color: #2ecc71;
        }
        .legend {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin: 15px 0;
            flex-wrap: wrap;
        }
        .legend-item {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 14px;
        }
        .legend-color {
            width: 20px;
            height: 20px;
            border-radius: 4px;
        }
        #tooltip {
            position: fixed;
            background-color: rgba(0,0,0,0.8);
            color: white;
            padding: 8px 12px;
            border-radius: 4px;
            font-size: 14px;
            pointer-events: none;
            display: none;
            z-index: 1000;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Задание 9: Визуализация данных</h1>
        
        <div class="controls">
            <button id="chartBar" class="active">📊 Столбчатая</button>
            <button id="chartLine">📈 Линейная</button>
            <button id="chartArea">📉 Области</button>
            <button id="refreshDataBtn">🔄 Обновить</button>
        </div>
        
        <div class="legend" id="legend"></div>
        
        <canvas id="chartCanvas" width="900" height="400"></canvas>
        
        <div id="tooltip"></div>
    </div>

    <script>
        const canvas = document.getElementById('chartCanvas');
        const ctx = canvas.getContext('2d');
        const tooltip = document.getElementById('tooltip');
        
        let chartData = [];
        let chartType = 'bar';
        
        const colors = [
            '#3498db', '#2ecc71', '#e74c3c', '#f39c12', '#9b59b6',
            '#1abc9c', '#e67e22', '#2c3e50', '#16a085', '#c0392b'
        ];

        // Генерация данных (можно заменить на реальные из CSV)
        function generateData() {
            const names = ['Анна', 'Борис', 'Виктория', 'Глеб', 'Дарья', 
                          'Евгений', 'Жанна', 'Иван', 'Кирилл', 'Лариса'];
            const values = names.map(() => Math.floor(Math.random() * 40) + 60);
            return names.map((name, index) => ({
                label: name,
                value: values[index]
            }));
        }

        // Рисование диаграммы
        function drawChart(data, type) {
            const width = canvas.width;
            const height = canvas.height;
            const padding = { top: 40, right: 40, bottom: 60, left: 60 };
            const chartWidth = width - padding.left - padding.right;
            const chartHeight = height - padding.top - padding.bottom;
            
            ctx.clearRect(0, 0, width, height);
            
            if (data.length === 0) {
                ctx.fillStyle = '#999';
                ctx.font = '18px Arial';
                ctx.textAlign = 'center';
                ctx.fillText('Нет данных для отображения', width / 2, height / 2);
                return;
            }

            // Определение максимального значения
            const maxValue = Math.max(...data.map(d => d.value), 10);
            const barWidth = chartWidth / data.length * 0.7;
            const gap = chartWidth / data.length;

            // Отрисовка сетки
            ctx.strokeStyle = '#eee';
            ctx.lineWidth = 1;
            ctx.beginPath();
            for (let i = 0; i <= 4; i++) {
                const y = padding.top + chartHeight - (i / 4) * chartHeight;
                ctx.moveTo(padding.left, y);
                ctx.lineTo(width - padding.right, y);
                
                ctx.fillStyle = '#999';
                ctx.font = '12px Arial';
                ctx.textAlign = 'right';
                ctx.fillText(Math.round((i / 4) * maxValue), padding.left - 10, y + 4);
            }
            ctx.stroke();

            // Отрисовка данных
            data.forEach((item, index) => {
                const x = padding.left + index * gap + (gap - barWidth) / 2;
                const barHeight = (item.value / maxValue) * chartHeight;
                const y = padding.top + chartHeight - barHeight;
                
                let color = colors[index % colors.length];
                
                // Изменение цвета для разных типов
                if (type === 'bar') {
                    // Столбчатая диаграмма
                    const gradient = ctx.createLinearGradient(x, y, x, y + barHeight);
                    gradient.addColorStop(0, color);
                    gradient.addColorStop(1, lightenColor(color, 40));
                    ctx.fillStyle = gradient;
                    
                    ctx.shadowColor = 'rgba(0,0,0,0.1)';
                    ctx.shadowBlur = 5;
                    ctx.fillRect(x, y, barWidth, barHeight);
                    ctx.shadowBlur = 0;
                    
                    // Рамка
                    ctx.strokeStyle = color;
                    ctx.lineWidth = 2;
                    ctx.strokeRect(x, y, barWidth, barHeight);
                    
                    // Значение сверху
                    ctx.fillStyle = '#333';
                    ctx.font = '12px Arial';
                    ctx.textAlign = 'center';
                    ctx.fillText(item.value, x + barWidth / 2, y - 5);
                    
                } else if (type === 'line' || type === 'area') {
                    // Линейная или диаграмма областей
                    const cx = padding.left + index * gap + gap / 2;
                    const cy = y;
                    
                    if (index === 0) {
                        ctx.beginPath();
                        ctx.moveTo(cx, cy);
                    } else {
                        const prevX = padding.left + (index - 1) * gap + gap / 2;
                        const prevY = padding.top + chartHeight - (data[index - 1].value / maxValue) * chartHeight;
                        
                        if (type === 'area') {
                            ctx.lineTo(cx, cy);
                            ctx.lineTo(cx, padding.top + chartHeight);
                            ctx.lineTo(prevX, padding.top + chartHeight);
                            ctx.closePath();
                            ctx.fillStyle = color + '40';
                            ctx.fill();
                            ctx.beginPath();
                            ctx.moveTo(prevX, prevY);
                            ctx.lineTo(cx, cy);
                        } else {
                            ctx.lineTo(cx, cy);
                        }
                    }
                    
                    ctx.strokeStyle = color;
                    ctx.lineWidth = 3;
                    ctx.stroke();
                    
                    // Точки
                    ctx.beginPath();
                    ctx.arc(cx, cy, 6, 0, Math.PI * 2);
                    ctx.fillStyle = color;
                    ctx.fill();
                    ctx.strokeStyle = 'white';
                    ctx.lineWidth = 2;
                    ctx.stroke();
                    
                    // Значение
                    ctx.fillStyle = '#333';
                    ctx.font = '12px Arial';
                    ctx.textAlign = 'center';
                    ctx.fillText(item.value, cx, cy - 12);
                }
                
                // Подписи оси X
                ctx.fillStyle = '#666';
                ctx.font = '12px Arial';
                ctx.textAlign = 'center';
                const labelX = padding.left + index * gap + gap / 2;
                ctx.fillText(item.label, labelX, padding.top + chartHeight + 20);
            });
        }

        // Вспомогательная функция для осветления цвета
        function lightenColor(hex, percent) {
            const num = parseInt(hex.replace('#', ''), 16);
            const amt = Math.round(2.55 * percent);
            const R = Math.min(255, (num >> 16) + amt);
            const G = Math.min(255, ((num >> 8) & 0x00FF) + amt);
            const B = Math.min(255, (num & 0x0000FF) + amt);
            return `rgb(${R}, ${G}, ${B})`;
        }

        // Обновление легенды
        function updateLegend(data) {
            const legendContainer = document.getElementById('legend');
            legendContainer.innerHTML = '';
            
            data.forEach((item, index) => {
                const div = document.createElement('div');
                div.className = 'legend-item';
                div.innerHTML = `
                    <span class="legend-color" style="background-color:${colors[index % colors.length]}"></span>
                    <span>${item.label}: ${item.value}</span>
                `;
                legendContainer.appendChild(div);
            });
        }

        // Обработка движения мыши на canvas
        canvas.addEventListener('mousemove', (event) => {
            const rect = canvas.getBoundingClientRect();
            const mouseX = event.clientX - rect.left;
            const mouseY = event.clientY - rect.top;
            
            // Масштабирование координат
            const scaleX = canvas.width / rect.width;
            const scaleY = canvas.height / rect.height;
            const canvasX = mouseX * scaleX;
            const canvasY = mouseY * scaleY;
            
            // Поиск ближайшего столбца
            const padding = { top: 40, right: 40, bottom: 60, left: 60 };
            const chartWidth = canvas.width - padding.left - padding.right;
            const gap = chartWidth / chartData.length;
            
            let found = false;
            chartData.forEach((item, index) => {
                const x = padding.left + index * gap + gap / 2;
                const y = padding.top + (1 - item.value / Math.max(...chartData.map(d => d.value), 10)) * 
                          (canvas.height - padding.top - padding.bottom);
                
                if (Math.abs(canvasX - x) < gap / 2 && Math.abs(canvasY - y) < 50) {
                    tooltip.style.display = 'block';
                    tooltip.style.left = (event.clientX + 10) + 'px';
                    tooltip.style.top = (event.clientY - 10) + 'px';
                    tooltip.innerHTML = `<strong>${item.label}</strong><br>Значение: ${item.value}`;
                    canvas.style.cursor = 'pointer';
                    found = true;
                }
            });
            
            if (!found) {
                tooltip.style.display = 'none';
                canvas.style.cursor = 'default';
            }
        });

        canvas.addEventListener('mouseleave', () => {
            tooltip.style.display = 'none';
        });

        // Обновление диаграммы
        function updateChart() {
            chartData = generateData();
            drawChart(chartData, chartType);
            updateLegend(chartData);
        }

        // Обработчики кнопок
        document.getElementById('chartBar').addEventListener('click', () => {
            chartType = 'bar';
            document.querySelectorAll('.controls button').forEach(btn => btn.classList.remove('active'));
            document.getElementById('chartBar').classList.add('active');
            drawChart(chartData, chartType);
        });
        
        document.getElementById('chartLine').addEventListener('click', () => {
            chartType = 'line';
            document.querySelectorAll('.controls button').forEach(btn => btn.classList.remove('active'));
            document.getElementById('chartLine').classList.add('active');
            drawChart(chartData, chartType);
        });
        
        document.getElementById('chartArea').addEventListener('click', () => {
            chartType = 'area';
            document.querySelectorAll('.controls button').forEach(btn => btn.classList.remove('active'));
            document.getElementById('chartArea').classList.add('active');
            drawChart(chartData, chartType);
        });
        
        document.getElementById('refreshDataBtn').addEventListener('click', updateChart);

        // Инициализация
        updateChart();

        // Обработка ресайза
        window.addEventListener('resize', () => {
            drawChart(chartData, chartType);
        });
    </script>
</body>
</html>
```
</details>


# Задание 10. Комплексное приложение: загрузка, фильтрация и визуализация

**Описание:** Создайте полноценное приложение, объединяющее все предыдущие навыки.

**Требования:**

- Загрузка данных из CSV через FastAPI.
- Отображение данных в виде таблицы.
- Поиск и фильтрация данных.
- Визуализация данных в виде графика (выбор типа графика).
- Экспорт отфильтрованных данных в CSV (скачивание).

<details>
<summary><b>Пример решения (ключевые компоненты)</b></summary>

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Задание 10: Комплексное приложение</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f2f5;
            padding: 20px;
        }
        .app {
            max-width: 1400px;
            margin: 0 auto;
        }
        header {
            background-color: #2c3e50;
            color: white;
            padding: 20px;
            border-radius: 12px;
            margin-bottom: 20px;
        }
        header h1 { margin-bottom: 5px; }
        .grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
        }
        .card {
            background-color: white;
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }
        .card h2 {
            color: #2c3e50;
            margin-bottom: 15px;
            font-size: 18px;
            border-bottom: 2px solid #e9ecef;
            padding-bottom: 10px;
        }
        .controls {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
            flex-wrap: wrap;
        }
        .controls input, .controls select {
            padding: 8px 12px;
            border: 2px solid #ddd;
            border-radius: 4px;
            font-size: 14px;
            flex: 1;
            min-width: 150px;
        }
        .controls input:focus, .controls select:focus {
            outline: none;
            border-color: #3498db;
        }
        .controls button {
            padding: 8px 20px;
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.3s;
            font-size: 14px;
        }
        .controls button:hover { background-color: #2980b9; }
        .controls button.secondary {
            background-color: #95a5a6;
        }
        .controls button.secondary:hover { background-color: #7f8c8d; }
        .controls button.danger {
            background-color: #e74c3c;
        }
        .controls button.danger:hover { background-color: #c0392b; }
        
        .table-wrapper {
            max-height: 400px;
            overflow: auto;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 14px;
        }
        table th {
            background-color: #3498db;
            color: white;
            padding: 8px 12px;
            text-align: left;
            position: sticky;
            top: 0;
            z-index: 10;
            cursor: pointer;
        }
        table th:hover { background-color: #2980b9; }
        table td {
            padding: 6px 12px;
            border-bottom: 1px solid #eee;
        }
        table tbody tr:hover { background-color: #f0f8ff; }
        table tbody tr:nth-child(even) { background-color: #fafafa; }
        
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
            gap: 10px;
            margin-bottom: 15px;
        }
        .stat-item {
            background-color: #f8f9fa;
            padding: 10px;
            border-radius: 8px;
            text-align: center;
        }
        .stat-item .value {
            font-size: 24px;
            font-weight: bold;
            color: #2c3e50;
        }
        .stat-item .label {
            font-size: 12px;
            color: #999;
        }
        
        .chart-container {
            height: 300px;
            background-color: #f8f9fa;
            border-radius: 8px;
            position: relative;
        }
        .chart-container canvas {
            width: 100% !important;
            height: 100% !important;
        }
        
        .download-btn {
            margin-top: 15px;
            padding: 10px 20px;
            background-color: #2ecc71;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.3s;
        }
        .download-btn:hover {
            background-color: #27ae60;
        }
        
        .full-width {
            grid-column: 1 / -1;
        }
        
        .alert {
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 15px;
        }
        .alert-info {
            background-color: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }
        .alert-warning {
            background-color: #fff3cd;
            color: #856404;
            border: 1px solid #ffeeba;
        }
        .alert-danger {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
        
        @media (max-width: 900px) {
            .grid {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="app">
        <header>
            <h1>📊 Комплексное приложение</h1>
            <p>Загрузка, фильтрация и визуализация данных</p>
        </header>
        
        <div id="alertContainer"></div>
        
        <div class="grid">
            <!-- Таблица -->
            <div class="card">
                <h2>📋 Данные</h2>
                <div class="controls">
                    <input type="text" id="searchInput" placeholder="Поиск...">
                    <select id="filterColumn">
                        <option value="">Все столбцы</option>
                    </select>
                    <button id="refreshBtn">🔄</button>
                </div>
                <div class="stats" id="statsContainer"></div>
                <div class="table-wrapper" id="tableContainer">
                    <div style="text-align:center;padding:40px;color:#999;">
                        Загрузка данных...
                    </div>
                </div>
                <button class="download-btn" id="downloadBtn">⬇️ Скачать CSV</button>
            </div>
            
            <!-- График -->
            <div class="card">
                <h2>📈 Визуализация</h2>
                <div class="controls">
                    <select id="chartTypeSelect">
                        <option value="bar">Столбчатая</option>
                        <option value="line">Линейная</option>
                        <option value="pie">Круговая</option>
                        <option value="area">Области</option>
                    </select>
                    <select id="chartColumnSelect">
                        <option value="score">Баллы</option>
                        <option value="age">Возраст</option>
                    </select>
                </div>
                <div class="chart-container">
                    <canvas id="chartCanvas"></canvas>
                </div>
            </div>
        </div>
    </div>

    <script>
        const API_URL = 'http://localhost:8000/api/data/csv';
        let allData = [];
        let filteredData = [];
        let currentSort = { column: null, direction: 'asc' };
        
        // DOM элементы
        const searchInput = document.getElementById('searchInput');
        const filterColumn = document.getElementById('filterColumn');
        const refreshBtn = document.getElementById('refreshBtn');
        const downloadBtn = document.getElementById('downloadBtn');
        const tableContainer = document.getElementById('tableContainer');
        const statsContainer = document.getElementById('statsContainer');
        const alertContainer = document.getElementById('alertContainer');
        const canvas = document.getElementById('chartCanvas');
        const chartTypeSelect = document.getElementById('chartTypeSelect');
        const chartColumnSelect = document.getElementById('chartColumnSelect');
        const ctx = canvas.getContext('2d');

        // Показ уведомления
        function showAlert(message, type = 'info') {
            alertContainer.innerHTML = `<div class="alert alert-${type}">${message}</div>`;
            setTimeout(() => {
                alertContainer.innerHTML = '';
            }, 5000);
        }

        // Загрузка данных
        async function loadData() {
            try {
                showAlert('Загрузка данных...', 'info');
                const response = await fetch(API_URL);
                if (!response.ok) throw new Error(`HTTP ${response.status}`);
                
                const result = await response.json();
                if (result.error) throw new Error(result.error);
                
                allData = result.data || [];
                filteredData = [...allData];
                
                updateFilterOptions();
                applyFilters();
                showAlert(`Загружено ${allData.length} записей`, 'info');
                
            } catch (error) {
                showAlert(`Ошибка: ${error.message}. Проверьте сервер.`, 'danger');
                console.error(error);
            }
        }

        // Обновление опций фильтра
        function updateFilterOptions() {
            if (allData.length === 0) return;
            const columns = Object.keys(allData[0]);
            filterColumn.innerHTML = '<option value="">Все столбцы</option>';
            columns.forEach(col => {
                const opt = document.createElement('option');
                opt.value = col;
                opt.textContent = col.charAt(0).toUpperCase() + col.slice(1);
                filterColumn.appendChild(opt);
            });
        }

        // Применение фильтров
        function applyFilters() {
            let data = [...allData];
            
            const search = searchInput.value.toLowerCase().trim();
            if (search) {
                const col = filterColumn.value;
                data = data.filter(row => {
                    if (col) {
                        return String(row[col] || '').toLowerCase().includes(search);
                    }
                    return Object.values(row).some(v => 
                        String(v).toLowerCase().includes(search)
                    );
                });
            }
            
            if (currentSort.column) {
                data.sort((a, b) => {
                    let va = a[currentSort.column] || '';
                    let vb = b[currentSort.column] || '';
                    if (!isNaN(va) && !isNaN(vb)) {
                        va = parseFloat(va);
                        vb = parseFloat(vb);
                    } else {
                        va = va.toString().toLowerCase();
                        vb = vb.toString().toLowerCase();
                    }
                    return currentSort.direction === 'asc' 
                        ? va > vb ? 1 : va < vb ? -1 : 0
                        : va < vb ? 1 : va > vb ? -1 : 0;
                });
            }
            
            filteredData = data;
            renderTable(data);
            updateStats(data);
            drawChart(data);
        }

        // Рендер таблицы
        function renderTable(data) {
            if (data.length === 0) {
                tableContainer.innerHTML = '<div style="text-align:center;padding:40px;color:#999;">Нет данных</div>';
                return;
            }
            
            const cols = Object.keys(data[0]);
            let html = '<table><thead><tr>';
            cols.forEach(col => {
                const isSorted = currentSort.column === col;
                const arrow = isSorted ? (currentSort.direction === 'asc' ? '↑' : '↓') : '↕';
                html += `<th onclick="sortBy('${col}')">${col} ${arrow}</th>`;
            });
            html += '</tr></thead><tbody>';
            
            data.forEach(row => {
                html += '<tr>';
                cols.forEach(col => {
                    let val = row[col] || '';
                    if (col === 'score' && !isNaN(val)) {
                        const score = parseFloat(val);
                        let cls = 'score-medium';
                        if (score >= 4.5) cls = 'score-high';
                        else if (score < 3.5) cls = 'score-low';
                        html += `<td><span class="score-badge ${cls}">${val}</span></td>`;
                    } else {
                        html += `<td>${val}</td>`;
                    }
                });
                html += '</tr>';
            });
            
            html += '</tbody></table>';
            tableContainer.innerHTML = html;
        }

        // Обновление статистики
        function updateStats(data) {
            if (data.length === 0) {
                statsContainer.innerHTML = `<div class="stat-item"><div class="value">0</div><div class="label">Записей</div></div>`;
                return;
            }
            
            const total = data.length;
            const avgScore = data.reduce((s, r) => s + parseFloat(r.score || 0), 0) / total;
            const avgAge = data.reduce((s, r) => s + parseFloat(r.age || 0), 0) / total;
            
            statsContainer.innerHTML = `
                <div class="stat-item"><div class="value">${total}</div><div class="label">Записей</div></div>
                <div class="stat-item"><div class="value">${avgScore.toFixed(2)}</div><div class="label">Средний балл</div></div>
                <div class="stat-item"><div class="value">${avgAge.toFixed(1)}</div><div class="label">Средний возраст</div></div>
            `;
        }

        // Сортировка
        window.sortBy = function(column) {
            if (currentSort.column === column) {
                currentSort.direction = currentSort.direction === 'asc' ? 'desc' : 'asc';
            } else {
                currentSort.column = column;
                currentSort.direction = 'asc';
            }
            applyFilters();
        };

        // Рисование графика
        function drawChart(data) {
            const rect = canvas.parentElement.getBoundingClientRect();
            canvas.width = canvas.parentElement.clientWidth || 800;
            canvas.height = 300;
            
            const width = canvas.width;
            const height = canvas.height;
            const padding = { top: 30, right: 30, bottom: 40, left: 50 };
            const chartW = width - padding.left - padding.right;
            const chartH = height - padding.top - padding.bottom;
            
            ctx.clearRect(0, 0, width, height);
            
            if (data.length === 0) {
                ctx.fillStyle = '#999';
                ctx.font = '18px Arial';
                ctx.textAlign = 'center';
                ctx.fillText('Нет данных', width / 2, height / 2);
                return;
            }
            
            const column = chartColumnSelect.value;
            const values = data.map(r => parseFloat(r[column]) || 0);
            const labels = data.map(r => r.name || '');
            const maxVal = Math.max(...values, 10);
            const minVal = Math.min(...values, 0);
            const range = maxVal - minVal || 1;
            
            const type = chartTypeSelect.value;
            
            if (type === 'pie') {
                // Круговая диаграмма
                const centerX = width / 2;
                const centerY = height / 2;
                const radius = Math.min(width, height) / 2 - 30;
                let startAngle = -Math.PI / 2;
                
                const total = values.reduce((s, v) => s + v, 0);
                if (total === 0) {
                    ctx.fillStyle = '#999';
                    ctx.font = '18px Arial';
                    ctx.textAlign = 'center';
                    ctx.fillText('Нет данных для круговой диаграммы', width / 2, height / 2);
                    return;
                }
                
                const colors = ['#3498db', '#2ecc71', '#e74c3c', '#f39c12', '#9b59b6', '#1abc9c', '#e67e22', '#2c3e50'];
                
                values.forEach((val, i) => {
                    const sliceAngle = (val / total) * Math.PI * 2;
                    const color = colors[i % colors.length];
                    
                    ctx.beginPath();
                    ctx.moveTo(centerX, centerY);
                    ctx.arc(centerX, centerY, radius, startAngle, startAngle + sliceAngle);
                    ctx.closePath();
                    ctx.fillStyle = color;
                    ctx.fill();
                    ctx.strokeStyle = 'white';
                    ctx.lineWidth = 2;
                    ctx.stroke();
                    
                    // Подпись
                    const midAngle = startAngle + sliceAngle / 2;
                    const labelRadius = radius * 0.65;
                    const lx = centerX + Math.cos(midAngle) * labelRadius;
                    const ly = centerY + Math.sin(midAngle) * labelRadius;
                    ctx.fillStyle = 'white';
                    ctx.font = '12px Arial';
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    if (val / total > 0.05) {
                        ctx.fillText(`${Math.round(val / total * 100)}%`, lx, ly);
                    }
                    
                    startAngle += sliceAngle;
                });
                
                // Легенда
                let legendY = 10;
                const legendX = width - 120;
                ctx.textAlign = 'left';
                ctx.textBaseline = 'top';
                labels.forEach((label, i) => {
                    if (i < 10) {
                        const color = colors[i % colors.length];
                        ctx.fillStyle = color;
                        ctx.fillRect(legendX, legendY + i * 20, 15, 15);
                        ctx.fillStyle = '#333';
                        ctx.font = '11px Arial';
                        ctx.fillText(label.substring(0, 12), legendX + 20, legendY + i * 20);
                    }
                });
                
            } else {
                // Столбчатая, линейная, области
                const gap = chartW / Math.max(data.length, 1);
                const barWidth = Math.min(gap * 0.7, 40);
                
                // Сетка
                ctx.strokeStyle = '#eee';
                ctx.lineWidth = 1;
                for (let i = 0; i <= 5; i++) {
                    const y = padding.top + chartH - (i / 5) * chartH;
                    ctx.beginPath();
                    ctx.moveTo(padding.left, y);
                    ctx.lineTo(width - padding.right, y);
                    ctx.stroke();
                    
                    ctx.fillStyle = '#999';
                    ctx.font = '11px Arial';
                    ctx.textAlign = 'right';
                    ctx.fillText((minVal + (i / 5) * range).toFixed(1), padding.left - 5, y + 4);
                }
                
                // Данные
                const isArea = type === 'area';
                const isLine = type === 'line' || isArea;
                
                values.forEach((val, i) => {
                    const x = padding.left + i * gap + gap / 2;
                    const normVal = (val - minVal) / range;
                    const barH = normVal * chartH;
                    const y = padding.top + chartH - barH;
                    
                    const color = ['#3498db', '#2ecc71', '#e74c3c', '#f39c12', '#9b59b6', '#1abc9c', '#e67e22', '#2c3e50'][i % 8];
                    
                    if (isLine) {
                        if (i === 0) {
                            ctx.beginPath();
                            ctx.moveTo(x, y);
                        } else {
                            const prevX = padding.left + (i - 1) * gap + gap / 2;
                            const prevVal = (values[i - 1] - minVal) / range;
                            const prevY = padding.top + chartH - prevVal * chartH;
                            
                            ctx.lineTo(x, y);
                            
                            if (isArea && i === values.length - 1) {
                                ctx.lineTo(x, padding.top + chartH);
                                ctx.lineTo(prevX, padding.top + chartH);
                                ctx.closePath();
                                ctx.fillStyle = color + '30';
                                ctx.fill();
                                ctx.beginPath();
                                ctx.moveTo(prevX, prevY);
                                ctx.lineTo(x, y);
                            }
                        }
                    } else {
                        // Столбцы
                        const grad = ctx.createLinearGradient(x, y, x, y + barH);
                        grad.addColorStop(0, color);
                        grad.addColorStop(1, color + '80');
                        ctx.fillStyle = grad;
                        ctx.shadowColor = 'rgba(0,0,0,0.1)';
                        ctx.shadowBlur = 5;
                        ctx.fillRect(x - barWidth / 2, y, barWidth, barH);
                        ctx.shadowBlur = 0;
                        ctx.strokeStyle = color;
                        ctx.lineWidth = 2;
                        ctx.strokeRect(x - barWidth / 2, y, barWidth, barH);
                    }
                    
                    // Точки для линейной
                    if (isLine) {
                        ctx.strokeStyle = color;
                        ctx.lineWidth = 3;
                        ctx.stroke();
                        
                        ctx.beginPath();
                        ctx.arc(x, y, 5, 0, Math.PI * 2);
                        ctx.fillStyle = color;
                        ctx.fill();
                        ctx.strokeStyle = 'white';
                        ctx.lineWidth = 2;
                        ctx.stroke();
                    }
                    
                    // Подпись оси X
                    ctx.fillStyle = '#666';
                    ctx.font = '11px Arial';
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'top';
                    const label = labels[i] || '';
                    ctx.fillText(label.substring(0, 10), x, padding.top + chartH + 10);
                    
                    // Значение
                    ctx.fillStyle = '#333';
                    ctx.font = '10px Arial';
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'bottom';
                    ctx.fillText(val.toFixed(1), x, y - 5);
                });
            }
        }

        // Экспорт CSV
        function downloadCSV() {
            if (filteredData.length === 0) {
                showAlert('Нет данных для экспорта', 'warning');
                return;
            }
            
            const cols = Object.keys(filteredData[0]);
            let csv = cols.join(',') + '\n';
            filteredData.forEach(row => {
                csv += cols.map(col => `"${row[col] || ''}"`).join(',') + '\n';
            });
            
            const blob = new Blob(['\uFEFF' + csv], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = `data_${new Date().toISOString().slice(0,10)}.csv`;
            link.click();
            URL.revokeObjectURL(link.href);
            showAlert('CSV скачан', 'info');
        }

        // Обработчики
        searchInput.addEventListener('input', applyFilters);
        filterColumn.addEventListener('change', applyFilters);
        refreshBtn.addEventListener('click', loadData);
        downloadBtn.addEventListener('click', downloadCSV);
        chartTypeSelect.addEventListener('change', () => drawChart(filteredData));
        chartColumnSelect.addEventListener('change', () => drawChart(filteredData));

        // Обработка ресайза для canvas
        window.addEventListener('resize', () => {
            if (filteredData.length > 0) drawChart(filteredData);
        });

        // Инициализация
        loadData();
    </script>
</body>
</html>
```
</details>
