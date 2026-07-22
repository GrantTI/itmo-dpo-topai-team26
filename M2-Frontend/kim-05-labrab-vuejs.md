# Лабораторная работа. Разработка интерфейса для выбора ML модели
Продолжительность: 6 академических часов

## Цель работы
Освоить разработку интерактивного веб-интерфейса для выбора и настройки моделей машинного обучения с использованием Vue.js 3 и FastAPI. Научиться создавать динамические формы, управлять состоянием приложения и визуализировать результаты работы моделей.

## Теоретическая часть
Архитектура приложения

### Frontend (Vue.js 3)
- **Выбор модели** – выпадающий список доступных ML-моделей
- **Динамические поля** – автоматическая генерация полей ввода под гиперпараметры выбранной модели
- **Флаги и чекбоксы** – управление дополнительными опциями (нормализация, валидация и т.д.)
- **Отображение результатов** – визуализация метрик (accuracy, precision, recall) и коэффициентов модели

### Backend (FastAPI)
- **REST API** – эндпоинты для CRUD операций с моделями
- **Обработка данных** – загрузка датасетов, предобработка, разделение на выборки
- **Выполнение ML** – обучение, предсказание, оценка качества
- **Возврат результатов** – структурированный ответ с метриками и коэффициентами

### Взаимодействие
- **HTTP** – основные запросы (получение списка моделей, отправка параметров, получение результатов)

## Ключевые концепции Vue.js 3
| Концепция | Назначение |
|-----------|------------|
| `v-model` | Двусторонняя привязка данных между элементом ввода и состоянием компонента |
| `v-for` | Динамическое создание элементов списка на основе итерации по массиву или объекту |
| `v-if` / `v-else` | Условный рендеринг элементов в зависимости от истинности выражения |
| `ref` / `reactive` | Создание реактивных данных (примитивы — `ref`, объекты — `reactive`) |
| `watch` | Отслеживание изменений реактивных данных и выполнение побочных эффектов |
| `onMounted` | Выполнение кода после монтирования компонента в DOM (хук жизненного цикла) |


## Ход работы

### Этап 1. Настройка проекта (30 минут)
**1.1. Создание структуры проекта**
```
    bash
mkdir ml-interface-lab
cd ml-interface-lab
mkdir -p backend/app backend/data
mkdir -p frontend/src/components frontend/src/api
</details>
```

**1.2. Настройка FastAPI бэкенда**

backend/requirements.txt:
```
text
fastapi==0.104.1
uvicorn[standard]==0.24.0
python-multipart==0.0.6
pandas==2.1.3
numpy==1.26.2
scikit-learn==1.3.2
pydantic==2.5.0
```

```
python
from fastapi import FastAPI, File, UploadFile
from fastapi.middleware.cors import CORSMiddleware
from typing import Dict, Any, List, Optional
import pandas as pd
import numpy as np
from pydantic import BaseModel

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

class ModelConfig(BaseModel):
    model_id: str
    params: Dict[str, Any]

class ModelMetrics(BaseModel):
    model_name: str
    accuracy: Optional[float] = None
    precision: Optional[float] = None
    recall: Optional[float] = None
    f1_score: Optional[float] = None
    coefficients: Optional[List[float]] = None
    feature_importance: Optional[Dict[str, float]] = None
    processed_samples: int = 0
```
</details>

### Доступные модели

```
AVAILABLE_MODELS = {
    "linear_regression": {
        "name": "Линейная регрессия",
        "params": [
            {"name": "fit_intercept", "type": "boolean", "default": True},
            {"name": "alpha", "type": "number", "default": 1.0}
        ]
    },
    "random_forest": {
        "name": "Случайный лес",
        "params": [
            {"name": "n_estimators", "type": "number", "default": 100},
            {"name": "max_depth", "type": "number", "default": 10}
        ]
    },
    "svm": {
        "name": "SVM",
        "params": [
            {"name": "C", "type": "number", "default": 1.0},
            {"name": "kernel", "type": "select", "default": "rbf", "options": ["linear", "rbf", "poly"]}
        ]
    }
}
```

# Backend (FastAPI) — пример кода

<details> <summary>Описание эндпоинтов</summary>
    
```python
@app.get("/api/models")
async def get_models():
    return {"models": AVAILABLE_MODELS}

@app.post("/api/train")
async def train_model(config: ModelConfig):
    # Симуляция обучения
    import time
    time.sleep(1)
    
    metrics = {
        "linear_regression": {"accuracy": 0.85, "coefficients": [0.5, -0.3, 0.8]},
        "random_forest": {"accuracy": 0.92, "feature_importance": {"f1": 0.4, "f2": 0.3, "f3": 0.3}},
        "svm": {"accuracy": 0.88, "coefficients": [0.2, -0.1, 0.5]}
    }
    
    result = metrics.get(config.model_id, {})
    result["model_name"] = AVAILABLE_MODELS[config.model_id]["name"]
    result["processed_samples"] = 1000
    return result

@app.post("/api/upload-data")
async def upload_data(file: UploadFile = File(...)):
    import aiofiles
    file_path = f"data/{file.filename}"
    
    async with aiofiles.open(file_path, 'wb') as out_file:
        content = await file.read()
        await out_file.write(content)
    
    df = pd.read_csv(file_path)
    return {
        "filename": file.filename,
        "rows": len(df),
        "columns": len(df.columns),
        "column_names": df.columns.tolist()
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```
</details>

```
json
{
  "name": "ml-interface",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite"
  },
  "dependencies": {
    "vue": "^3.3.8",
    "axios": "^1.6.2"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^4.5.0",
    "vite": "^5.0.0"
  }
}
```

frontend/vite.config.js:

```
javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:8000',
        changeOrigin: true
      }
    }
  }
})
```

frontend/index.html:

```
html
<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <title>ML Model Selection</title>
</head>
<body>
  <div id="app"></div>
  <script type="module" src="/src/main.js"></script>
</body>
</html>
```

## Этап 2. Разработка Vue.js компонентов (2 часа)

### 2.1. Основной компонент

```
javascript
import { createApp } from 'vue'
import App from './App.vue'
createApp(App).mount('#app')
```

Напишите код на `Vue.js 3` для создания интерфейса выбора ML-модели: вы загружаете CSV-файл, выбираете модель из списка и настраиваете её гиперпараметры (флаги, выпадающие списки, числа) через динамические формы.

При нажатии кнопки "Обучить" код отправляет выбранную модель и параметры на сервер через API, получает метрики (точность, коэффициенты, важность признаков) и отображает их в интерфейсе.

Вся логика построена на реактивных переменных (`ref`), условном рендеринге (`v-if`/`v-for`) и хуке onMounted для автоматической загрузки списка моделей при старте приложения.



### 2.2. API клиент
<details> <summary><b>Код: frontend/src/api/index.js</b></summary>
javascript
import axios from 'axios'

const api = {
  async getModels() {
    const response = await axios.get('/api/models')
    return response.data
  },

  async trainModel(config) {
    const response = await axios.post('/api/train', config)
    return response.data
  },

  async uploadData(file) {
    const formData = new FormData()
    formData.append('file', file)
    const response = await axios.post('/api/upload-data', formData)
    return response.data
  }
}

export default api
</details>

## Этап 3. Реализация бэкенда с ML моделями (1.5 часа)
Создайте класс `MLService` с методом `load_data()`, который читает CSV, разделяет признаки и целевую переменную, преобразует категории через `pd.get_dummies()`, масштабирует данные и делит их на `train`/`test` выборки.

Разработайте отдельные методы для каждой модели (`train_random_forest`, `train_linear_regression`, `train_svm`), которые принимают словарь с гиперпараметрами, создают экземпляр модели из `scikit-learn`, обучают её на подготовленных данных и вычисляют метрики.

Используйте в методах единый паттерн: засекайте время через `time.time()`, выполняйте `fit()` и `predict()`, рассчитывайте метрики (accuracy, precision, recall, f1_score, r2_score, коэффициенты, важность признаков) и возвращайте результат в виде словаря.

Подключите класс к API в `main.py`: в эндпоинте загрузки создавайте экземпляр сервиса и вызывайте `load_data()`, в эндпоинте обучения выбирайте нужный метод по model_id и передавайте параметры из запроса.

Добавьте обработку ошибок: проверяйте наличие загруженных данных, существование модели и оборачивайте обучение в `try/except`, а также установите зависимости `scikit-learn`, pandas и numpy для работы сервиса.


## Этап 4. Запуск и тестирование (30 минут)
### 4.1. Запуск бэкенда
```
bash
cd backend
pip install -r requirements.txt
python -m app.main
```
### 4.2. Запуск фронтенда
```
bash
cd frontend
npm install
npm run dev
```
### 4.3. Тестовые данные
<details> <summary><b>Код: test_data.csv</b></summary>

```
csv
feature1,feature2,feature3,target
1.2,3.4,5.6,0
2.3,4.5,6.7,1
3.4,5.6,7.8,0
4.5,6.7,8.9,1
5.6,7.8,9.0,0
6.7,8.9,1.2,1
7.8,9.0,2.3,0
8.9,1.2,3.4,1
9.0,2.3,4.5,0
1.1,3.3,5.5,1
```
</details>

# Критерии оценки лабораторной работы

| Балл | Критерий |
|:----:|----------|
| 2 | Настройка проекта — создана структура директорий (backend, frontend), настроены зависимости в requirements.txt и package.json, настроен Vite с прокси для API |
| 2 | Загрузка данных — реализована загрузка файлов через интерфейс, данные передаются на сервер, отображается информация о загруженном файле |
| 2 | Выбор модели — список моделей загружается с сервера и отображается в выпадающем списке, при выборе модели отображаются доступные параметры для настройки |
| 2 | Динамические формы — реализована автоматическая генерация полей ввода в зависимости от типа параметра (логический, числовой, строковый с выбором значений) |
| 2 | Обучение модели — реализован бэкенд с классом для работы с моделями, методы обучения на загруженных данных с вычислением метрик качества |
| 2 | Отображение метрик — реализовано отображение числовых метрик производительности модели и параметров модели (коэффициенты или важность признаков) |
| 1 | Обработка ошибок — реализована обработка ошибок на бэкенде и фронтенде при некорректных действиях пользователя |
| 1 | API взаимодействие — настроено корректное взаимодействие между фронтендом и бэкендом через REST API, данные передаются в структурированном формате |
| 1 | Качество кода — код структурирован, разделен на компоненты и сервисы, присутствуют комментарии, соблюдены соглашения по именованию |
| **15** | **ИТОГО** |

