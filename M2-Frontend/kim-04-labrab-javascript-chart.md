# Лабораторная работа

## Тема: Javascript и визуализация данных

**Цель работы:** Освоить навыки работы с DOM-элементами, обработки событий, загрузки и визуализации данных с использованием JavaScript.

**Продолжительность:** 6 академических часов

**Необходимые инструменты:** Текстовый редактор (VS Code), браузер (Google Chrome), Python с FastAPI (для заданий 8-10).

---

## Подготовка окружения

<details>
<summary><b>Настройки окружения</b></summary>

### Структура проекта

lab-javascript/
├── index.html
├── style.css
├── script.js
├── data/
│   ├── sample.txt
│   └── sample.csv
├── server.py
└── README.md

Настройка FastAPI сервера (для заданий 8-10)
server.py:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from fastapi.responses import FileResponse
import os
```

app = FastAPI()

# Разрешаем CORS для разработки

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

Запуск сервера:

```
bash
pip install fastapi uvicorn
uvicorn server:app --reload
```
