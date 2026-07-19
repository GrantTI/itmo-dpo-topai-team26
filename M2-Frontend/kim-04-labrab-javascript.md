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

**Пример решения:**

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

# Задание 2. Обработка групп элементов по классам

**Описание:** Создайте несколько элементов с одинаковым классом и выполните с ними групповые операции.

**Требования:**

- Создайте 5 карточек с классом `card`.
- При клике на любую карточку она должна менять цвет.
- Добавьте кнопку "Сбросить", которая возвращает все карточки в исходное состояние.
- Реализуйте подсчет количества кликов по каждой карточке.

**Пример решения:**

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
