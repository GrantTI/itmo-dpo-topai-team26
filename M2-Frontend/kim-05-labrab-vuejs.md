# Лабораторная работа 2.4. Разработка интерфейса для выбора ML модели
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

1.3. Настройка Vue.js 3
frontend/package.json:

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
```
vue
<template>
  <div>
    <h1>Выбор ML модели</h1>
    
    <!-- Загрузка данных -->
    <div>
      <h2>Загрузка данных</h2>
      <input type="file" @change="uploadFile" accept=".csv" />
      <div v-if="fileInfo">
        <p>Файл: {{ fileInfo.filename }} ({{ fileInfo.rows }} строк)</p>
      </div>
    </div>

    <!-- Выбор модели -->
    <div>
      <h2>Модель</h2>
      <select v-model="selectedModelId" @change="onModelChange">
        <option value="">Выберите модель</option>
        <option v-for="(model, id) in models" :key="id" :value="id">
          {{ model.name }}
        </option>
      </select>
    </div>

    <!-- Параметры модели -->
    <div v-if="selectedModel">
      <h2>Параметры</h2>
      <div v-for="param in selectedModel.params" :key="param.name">
        <label>
          {{ param.name }}
          <!-- Boolean (флаг) -->
          <input v-if="param.type === 'boolean'" 
                 type="checkbox" 
                 v-model="params[param.name]" />
          <!-- Select -->
          <select v-else-if="param.type === 'select'" 
                  v-model="params[param.name]">
            <option v-for="opt in param.options" :key="opt" :value="opt">
              {{ opt }}
            </option>
          </select>
          <!-- Number -->
          <input v-else-if="param.type === 'number'" 
                 type="number" 
                 v-model="params[param.name]" />
        </label>
      </div>
    </div>

    <!-- Кнопка обучения -->
    <button @click="trainModel" :disabled="!selectedModelId || !fileInfo">
      Обучить
    </button>

    <!-- Результаты -->
    <div v-if="metrics">
      <h2>Результаты</h2>
      <p>Модель: {{ metrics.model_name }}</p>
      <p v-if="metrics.accuracy">Точность: {{ metrics.accuracy }}</p>
      <p v-if="metrics.precision">Precision: {{ metrics.precision }}</p>
      <p v-if="metrics.recall">Recall: {{ metrics.recall }}</p>
      <p v-if="metrics.f1_score">F1: {{ metrics.f1_score }}</p>
      <p>Обработано: {{ metrics.processed_samples }} записей</p>
      
      <!-- Коэффициенты -->
      <div v-if="metrics.coefficients">
        <h3>Коэффициенты</h3>
        <div v-for="(coef, i) in metrics.coefficients" :key="i">
          β{{ i }}: {{ coef.toFixed(3) }}
        </div>
      </div>
      
      <!-- Важность признаков -->
      <div v-if="metrics.feature_importance">
        <h3>Важность признаков</h3>
        <div v-for="(value, key) in metrics.feature_importance" :key="key">
          {{ key }}: {{ (value * 100).toFixed(1) }}%
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import api from './api'

const models = ref({})
const selectedModelId = ref('')
const selectedModel = ref(null)
const params = ref({})
const fileInfo = ref(null)
const metrics = ref(null)

const loadModels = async () => {
  const response = await api.getModels()
  models.value = response.models
}

const onModelChange = () => {
  selectedModel.value = models.value[selectedModelId.value]
  // Инициализация параметров значениями по умолчанию
  params.value = {}
  selectedModel.value.params.forEach(p => {
    params.value[p.name] = p.default
  })
  metrics.value = null
}

const uploadFile = async (event) => {
  const file = event.target.files[0]
  if (!file) return
  const response = await api.uploadData(file)
  fileInfo.value = response
}

const trainModel = async () => {
  const response = await api.trainModel({
    model_id: selectedModelId.value,
    params: params.value
  })
  metrics.value = response
}

onMounted(loadModels)
</script>
```


2.2. API клиент
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
Этап 3. Реализация бэкенда с ML моделями (1.5 часа)
<details> <summary><b>Код: backend/app/services/ml_service.py</b></summary>
python
from sklearn.linear_model import LinearRegression, LogisticRegression
from sklearn.ensemble import RandomForestClassifier
from sklearn.svm import SVC
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score
import pandas as pd
import time

class MLService:
    def __init__(self):
        self.X_train = None
        self.X_test = None
        self.y_train = None
        self.y_test = None
    
    def load_data(self, file_path):
        df = pd.read_csv(file_path)
        X = df.iloc[:, :-1]
        y = df.iloc[:, -1]
        
        # Преобразование категориальных данных
        X = pd.get_dummies(X)
        
        self.X_train, self.X_test, self.y_train, self.y_test = train_test_split(
            X, y, test_size=0.2, random_state=42
        )
        
        scaler = StandardScaler()
        self.X_train = scaler.fit_transform(self.X_train)
        self.X_test = scaler.transform(self.X_test)
        
        return {"samples": len(df), "features": X.shape[1]}
    
    def train_random_forest(self, params):
        start = time.time()
        model = RandomForestClassifier(
            n_estimators=int(params.get('n_estimators', 100)),
            max_depth=int(params.get('max_depth', 10)),
            random_state=42
        )
        model.fit(self.X_train, self.y_train)
        y_pred = model.predict(self.X_test)
        
        return {
            "accuracy": accuracy_score(self.y_test, y_pred),
            "precision": precision_score(self.y_test, y_pred, average='weighted'),
            "recall": recall_score(self.y_test, y_pred, average='weighted'),
            "f1_score": f1_score(self.y_test, y_pred, average='weighted'),
            "feature_importance": dict(zip(
                [f"f{i}" for i in range(len(model.feature_importances_))],
                model.feature_importances_.tolist()
            )),
            "training_time": time.time() - start
        }
    
    def train_linear_regression(self, params):
        start = time.time()
        model = LinearRegression(
            fit_intercept=params.get('fit_intercept', True)
        )
        model.fit(self.X_train, self.y_train)
        
        return {
            "r2_score": model.score(self.X_test, self.y_test),
            "coefficients": model.coef_.tolist(),
            "training_time": time.time() - start
        }
    
    def train_svm(self, params):
        start = time.time()
        model = SVC(
            C=params.get('C', 1.0),
            kernel=params.get('kernel', 'rbf'),
            random_state=42
        )
        model.fit(self.X_train, self.y_train)
        y_pred = model.predict(self.X_test)
        
        return {
            "accuracy": accuracy_score(self.y_test, y_pred),
            "precision": precision_score(self.y_test, y_pred, average='weighted'),
            "recall": recall_score(self.y_test, y_pred, average='weighted'),
            "f1_score": f1_score(self.y_test, y_pred, average='weighted'),
            "training_time": time.time() - start
        }
</details>
Обновленный backend/app/main.py (добавление реального обучения)
<details> <summary><b>Код: Обновленный main.py</b></summary>
python
from app.services.ml_service import MLService

data_storage = {}

@app.post("/api/upload-data")
async def upload_data(file: UploadFile = File(...)):
    import aiofiles
    import os
    
    os.makedirs("data", exist_ok=True)
    file_path = f"data/{file.filename}"
    
    async with aiofiles.open(file_path, 'wb') as out_file:
        content = await file.read()
        await out_file.write(content)
    
    service = MLService()
    info = service.load_data(file_path)
    data_storage[file.filename] = service
    
    return {
        "filename": file.filename,
        "rows": info["samples"],
        "columns": info["features"]
    }

@app.post("/api/train")
async def train_model(config: ModelConfig):
    if not data_storage:
        raise HTTPException(status_code=400, detail="Данные не загружены")
    
    service = list(data_storage.values())[0]
    
    methods = {
        "linear_regression": service.train_linear_regression,
        "random_forest": service.train_random_forest,
        "svm": service.train_svm
    }
    
    if config.model_id not in methods:
        raise HTTPException(status_code=400, detail="Неизвестная модель")
    
    metrics = methods[config.model_id](config.params)
    metrics["model_name"] = AVAILABLE_MODELS[config.model_id]["name"]
    metrics["processed_samples"] = len(service.X_train) + len(service.X_test)
    return metrics
</details>
Этап 4. Запуск и тестирование (30 минут)
4.1. Запуск бэкенда
bash
cd backend
pip install -r requirements.txt
python -m app.main
4.2. Запуск фронтенда
bash
cd frontend
npm install
npm run dev
4.3. Тестовые данные
<details> <summary><b>Код: test_data.csv</b></summary>
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
</details>
