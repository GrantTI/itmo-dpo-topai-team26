# КИМ-1.2
## Лабораторная работа на тему "Разработка Backend-сервиса для ML-моделей на FastAPI"
---

### Оглавление
| № | Раздел | Описание | Дескриптор
|---|--------|----------|----|
| | **[Введение](#введение)** | Цель работы, объем, формат сдачи | |
| **Этап 1** | **[Настройка инфраструктуры и базовая архитектура](#этап-1-настройка-инфраструктуры-и-базовая-архитектура)** | Создание каркаса проекта, настройка FastAPI, Docker-контейнеризация | Студент организует код API-приложения по трехуровневой архитектуре: Маршруты-Контроллеры-Сервисы |
| **Этап 2** | **[Работа с данными (Data Layer)](#этап-2-работа-с-данными-data-layer)** | Загрузка CSV, предпросмотр, статистика, выбор целевой переменной |Студент реализует API для ML-пайплайна|
| **Этап 3** | **[Модели и обучение (Model Layer)](#этап-3-модели-и-обучение-model-layer)** | Список моделей, гиперпараметры, обучение, метрики, веса модели |Студент реализует API для ML-пайплайна|
| **Этап 4** | **[Валидация и графики (Validation Layer)](#этап-4-валидация-и-графики-validation-layer)** | Hold-out валидация, метрики train/test, визуализация |Студент реализует API для ML-пайплайна|
| **Этап 5** | **[Прогнозирование и экспорт (Prediction & Export Layer)](#этап-5-прогнозирование-и-экспорт-prediction--export-layer)** | Прогнозирование на новых данных, экспорт графиков и CSV |Студент реализует API для ML-пайплайна|
| **Этап 6** | **[Обработка ошибок, логирование и документация](#этап-6-обработка-ошибок-логирование-и-документация)** | Глобальный exception handler, логирование, Swagger/OpenAPI |• Студент документирует все эндпоинты API с использованием OpenAPI<br>• Студент реализует централизованную обработку ошибок|
| | **[Контрольные вопросы](#контрольные-вопросы)** | Базовые, средней сложности и продвинутые вопросы |
| | **[Критерии оценки](#критерии-оценки)** | Шкала оценивания |
| | **[Техническая база](#техническая-база)** | Необходимые инструменты, фреймворки, библиотеки и оборудование |

---

# Введение

**Цель лабораторной работы:** разработать бэкенд-сервис на FastAPI, позволяющий работать с ML-моделями: загрузка данных, обучение, валидация, прогнозирование и экспорт результатов.

**Макисмальная оценка за лабораторную работу:** `30 баллов` (см. **[Критерии оценки](#критерии-оценки)**).

**Формат сдачи:** 
- Исходный код в репозитории Git
- README.md с инструкцией по запуску и примерами запросов
- Скриншоты Swagger UI (/docs)
- Отчет с ответами на контрольные вопросы

---

# Этап 1: Настройка инфраструктуры и базовая архитектура

## Задание

Создать каркас проекта с правильной архитектурой, настроить окружение для разработки и контейнеризацию.

### Требования

1. **Структура проекта** должна соответствовать схеме:

```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── routes.py
│   │   ├── data.py
│   │   ├── models.py
│   │   ├── validation.py
│   │   ├── predict.py
│   │   └── export.py
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py
│   │   └── exceptions.py
│   ├── services/
│   │   ├── __init__.py
│   │   ├── data_service.py
│   │   ├── model_service.py
│   │   ├── validation_service.py
│   │   ├── predict_service.py
│   │   └── export_service.py
│   ├── schemas/
│   │   ├── __init__.py
│   │   ├── data_schemas.py
│   │   ├── model_schemas.py
│   │   ├── validation_schemas.py
│   │   └── export_schemas.py
│   └── utils/
│       ├── __init__.py
│       ├── data_processor.py
│       ├── visualizer.py
│       └── validators.py
├── requirements.txt
├── Dockerfile
└── .env.example
```

2. **Настройка FastAPI приложения:**
   - Создать экземпляр FastAPI с метаданными (title, description, version)
   - Настроить CORS (разрешить запросы с localhost:5173 для разработки)
   - Подключить все роутеры с префиксом `/api`

3. **Глобальное состояние:**
   - Создать модуль `app/core/state.py` с классами для хранения:
     - Загруженного DataFrame
     - Выбранной целевой переменной
     - Обученной модели
     - Результатов валидации

4. **Настройка переменных окружения через Pydantic Settings:**
   - Максимальный размер файла (10 MB)
   - Пути к директориям (data, logs, exports)
   - Настройки CORS

5. **Docker-контейнеризация:**
   - Создать Dockerfile для бэкенда
   - Создать docker-compose.yml (только для бэкенда)
   - Настроить монтирование томов для data, logs, exports

### Пример

**app/main.py:**
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.api.routes import router
from app.core.config import settings

app = FastAPI(
    title="ML Models API",
    description="Backend for training and validating ML models",
    version="1.0.0"
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.ALLOWED_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(router, prefix="/api")

@app.get("/")
async def root():
    return {"message": "ML Models API is running"}
```

**app/core/config.py:**
```python
from pydantic_settings import BaseSettings
from typing import List

class Settings(BaseSettings):
    MAX_FILE_SIZE: int = 10 * 1024 * 1024  # 10 MB
    DATA_DIR: str = "./data"
    LOGS_DIR: str = "./logs"
    EXPORTS_DIR: str = "./exports"
    ALLOWED_ORIGINS: List[str] = ["http://localhost:5173", "http://localhost:3000"]
    
    class Config:
        env_file = ".env"

settings = Settings()
```

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: ml-backend
    ports:
      - "8000:8000"
    environment:
      - ENVIRONMENT=development
    volumes:
      - ./backend:/app
      - ./data:/app/data
      - ./logs:/app/logs
      - ./exports:/app/exports
    restart: unless-stopped
```

### Полезные источники

1. [FastAPI Official Documentation](https://fastapi.tiangolo.com/)
2. [Pydantic Settings](https://docs.pydantic.dev/latest/concepts/pydantic_settings/)
3. [Docker Documentation](https://docs.docker.com/)
4. [FastAPI CORS Middleware](https://fastapi.tiangolo.com/tutorial/cors/)

---

# Этап 2: Работа с данными (Data Layer)

## Задание

Реализовать API для загрузки, предпросмотра и обработки данных.

### Требования

1. **Эндпоинты для данных:**
   - `POST /api/data/upload` — загрузка CSV файла
   - `GET /api/data/preview` — предпросмотр первых строк
   - `GET /api/data/stats` — статистика по числовым колонкам
   - `POST /api/data/process` — выбор целевой переменной и стандартизация

2. **Валидация данных:**
   - Проверка размера файла (≤ 10 MB)
   - Проверка формата (только CSV)
   - Автоматическое определение разделителя (запятая, точка с запятой, табуляция)
   - Проверка на наличие числовых колонок
   - Обработка пропусков (заполнение средним или удаление)

3. **Pydantic-схемы:**
   - `FileUploadResponse` — информация о загруженном файле
   - `DataPreviewResponse` — первые строки данных
   - `DataStatsResponse` — статистика по колонкам
   - `ProcessDataRequest` — выбор целевой переменной и параметры
   - `ProcessDataResponse` — результат обработки

4. **DataService:**
   - Метод `load_csv(file)` — загрузка и парсинг CSV
   - Метод `get_preview(n_rows=5)` — получение первых строк
   - Метод `get_stats()` — расчет статистики
   - Метод `set_target(column_name, standardize=False)` — выбор целевой переменной
   - Метод `get_data()` — получение DataFrame

### Пример

**app/schemas/data_schemas.py:**
```python
from pydantic import BaseModel
from typing import Dict, Any, List, Optional

class FileUploadResponse(BaseModel):
    filename: str
    rows: int
    columns: int
    column_names: List[str]
    dtypes: Dict[str, str]
    size_mb: float

class DataPreviewResponse(BaseModel):
    preview: List[Dict[str, Any]]
    rows: int
    columns: int

class DataStatsResponse(BaseModel):
    column: str
    count: int
    mean: Optional[float]
    std: Optional[float]
    min: Optional[float]
    max: Optional[float]
    median: Optional[float]

class ProcessDataRequest(BaseModel):
    target_column: str
    standardize: bool = False

class ProcessDataResponse(BaseModel):
    target_column: str
    features: List[str]
    X_shape: List[int]
    y_shape: List[int]
    standardized: bool
```

**app/services/data_service.py:**
```python
import pandas as pd
import numpy as np
from typing import Dict, Any, List, Optional, Tuple
from fastapi import UploadFile, HTTPException
from app.core.state import app_state

class DataService:
    def __init__(self):
        self.df: Optional[pd.DataFrame] = None
        self.target_column: Optional[str] = None
        self.X: Optional[pd.DataFrame] = None
        self.y: Optional[pd.Series] = None
        self.scaler = None
    
    async def load_csv(self, file: UploadFile) -> Dict[str, Any]:
        """Загрузка CSV с автоматическим определением разделителя"""
        if not file.filename.endswith('.csv'):
            raise HTTPException(400, "Only CSV files are supported")
        
        content = await file.read()
        # Определение разделителя
        separators = [',', ';', '\t']
        for sep in separators:
            try:
                df = pd.read_csv(pd.io.StringIO(content.decode('utf-8')), sep=sep)
                break
            except:
                continue
        else:
            raise HTTPException(400, "Could not determine CSV separator")
        
        self.df = df
        app_state.df = df
        
        return {
            "filename": file.filename,
            "rows": len(df),
            "columns": len(df.columns),
            "column_names": df.columns.tolist(),
            "dtypes": df.dtypes.astype(str).to_dict(),
            "size_mb": len(content) / (1024 * 1024)
        }
    
    def get_preview(self, n_rows: int = 5) -> Dict[str, Any]:
        if self.df is None:
            raise HTTPException(400, "No data loaded")
        return {
            "preview": self.df.head(n_rows).replace({np.nan: None}).to_dict(orient='records'),
            "rows": len(self.df),
            "columns": len(self.df.columns)
        }
    
    def set_target(self, target_column: str, standardize: bool = False):
        if self.df is None:
            raise HTTPException(400, "No data loaded")
        
        if target_column not in self.df.columns:
            raise HTTPException(400, f"Column '{target_column}' not found")
        
        self.target_column = target_column
        self.X = self.df.drop(columns=[target_column])
        self.y = self.df[target_column]
        
        # Стандартизация
        if standardize:
            from sklearn.preprocessing import StandardScaler
            self.scaler = StandardScaler()
            self.X = pd.DataFrame(
                self.scaler.fit_transform(self.X),
                columns=self.X.columns
            )
        
        app_state.X = self.X
        app_state.y = self.y
        app_state.target_column = target_column
        
        return {
            "target_column": target_column,
            "features": self.X.columns.tolist(),
            "X_shape": [len(self.X), len(self.X.columns)],
            "y_shape": [len(self.y)],
            "standardized": standardize
        }
```

### Полезные источники

1. [Pandas Documentation](https://pandas.pydata.org/docs/)
2. [FastAPI File Upload](https://fastapi.tiangolo.com/tutorial/request-files/)
3. [Scikit-learn Preprocessing](https://scikit-learn.org/stable/modules/preprocessing.html)

---

# Этап 3: Модели и обучение (Model Layer)

## Задание

Реализовать API для работы с моделями: список, гиперпараметры, обучение и метрики.

### Требования

1. **Эндпоинты для моделей:**
   - `GET /api/models` — список доступных моделей
   - `GET /api/models/{model_id}` — информация о конкретной модели
   - `POST /api/train` — обучение модели
   - `GET /api/train/metrics` — получение метрик
   - `GET /api/train/coefficients` — получение весов модели

2. **Реализовать 5 моделей согласно ТЗ:**
   - `LinearRegression` — без гиперпараметров
   - `Ridge` — параметр alpha
   - `Lasso` — параметр alpha
   - `ElasticNet` — параметры alpha и l1_ratio
   - `SGDRegressor` — параметры learning_rate, max_iter, alpha

3. **Pydantic-схемы:**
   - `ModelInfoResponse` — информация о модели
   - `ModelListResponse` — список моделей
   - `TrainRequest` — запрос на обучение
   - `MetricsResponse` — метрики качества
   - `CoefficientsResponse` — веса модели

4. **ModelService:**
   - Метод `get_models()` — получение списка моделей
   - Метод `get_model_info(model_id)` — информация о модели
   - Метод `train(model_id, hyperparameters)` — обучение модели
   - Метод `get_metrics()` — расчет метрик
   - Метод `get_coefficients()` — получение весов

### Пример

**app/schemas/model_schemas.py:**
```python
from pydantic import BaseModel
from typing import Dict, List, Optional, Any

class HyperparameterInfo(BaseModel):
    name: str
    type: str
    default: Any
    range: Optional[List[float]] = None
    description: Optional[str] = None

class ModelInfoResponse(BaseModel):
    id: str
    name: str
    description: str
    hyperparameters: List[HyperparameterInfo]

class ModelListResponse(BaseModel):
    models: List[ModelInfoResponse]

class TrainRequest(BaseModel):
    model_id: str
    hyperparameters: Optional[Dict[str, Any]] = {}

class MetricsResponse(BaseModel):
    r2: float
    mse: float
    rmse: float
    mae: float

class CoefficientsResponse(BaseModel):
    coefficients: Dict[str, float]
    intercept: float
```

**app/services/model_service.py:**
```python
from sklearn.linear_model import LinearRegression, Ridge, Lasso, ElasticNet, SGDRegressor
from sklearn.metrics import r2_score, mean_squared_error, mean_absolute_error
import numpy as np
from typing import Dict, Any, Optional
from fastapi import HTTPException
from app.core.state import app_state

class ModelService:
    MODELS = {
        "linear_regression": {
            "name": "Linear Regression (OLS)",
            "description": "Ordinary Least Squares linear regression",
            "class": LinearRegression,
            "hyperparameters": []
        },
        "ridge": {
            "name": "Ridge Regression",
            "description": "Linear regression with L2 regularization",
            "class": Ridge,
            "hyperparameters": [
                {"name": "alpha", "type": "float", "default": 1.0, "range": [0.01, 100.0]}
            ]
        },
        "lasso": {
            "name": "Lasso Regression",
            "description": "Linear regression with L1 regularization",
            "class": Lasso,
            "hyperparameters": [
                {"name": "alpha", "type": "float", "default": 1.0, "range": [0.01, 100.0]}
            ]
        },
        "elasticnet": {
            "name": "ElasticNet",
            "description": "Linear regression with L1 and L2 regularization",
            "class": ElasticNet,
            "hyperparameters": [
                {"name": "alpha", "type": "float", "default": 1.0, "range": [0.01, 100.0]},
                {"name": "l1_ratio", "type": "float", "default": 0.5, "range": [0.0, 1.0]}
            ]
        },
        "sgd_regressor": {
            "name": "SGD Regressor",
            "description": "Stochastic Gradient Descent Regressor",
            "class": SGDRegressor,
            "hyperparameters": [
                {"name": "learning_rate", "type": "str", "default": "constant", "range": ["constant", "optimal"]},
                {"name": "max_iter", "type": "int", "default": 1000, "range": [100, 5000]},
                {"name": "alpha", "type": "float", "default": 0.0001, "range": [0.00001, 0.1]}
            ]
        }
    }
    
    def __init__(self):
        self.model = None
        self.model_id = None
    
    def get_models(self) -> Dict[str, Any]:
        models_info = []
        for model_id, info in self.MODELS.items():
            models_info.append({
                "id": model_id,
                "name": info["name"],
                "description": info["description"],
                "hyperparameters": info["hyperparameters"]
            })
        return {"models": models_info}
    
    def train(self, model_id: str, hyperparameters: Dict[str, Any] = None):
        if app_state.X is None or app_state.y is None:
            raise HTTPException(400, "Data not loaded or target not selected")
        
        if model_id not in self.MODELS:
            raise HTTPException(404, f"Model '{model_id}' not found")
        
        model_info = self.MODELS[model_id]
        hyperparameters = hyperparameters or {}
        
        # Сборка параметров с подстановкой значений по умолчанию
        params = {}
        for hp in model_info["hyperparameters"]:
            params[hp["name"]] = hyperparameters.get(hp["name"], hp["default"])
        
        # Инициализация и обучение модели
        model_class = model_info["class"]
        self.model = model_class(**params)
        self.model.fit(app_state.X, app_state.y)
        self.model_id = model_id
        
        app_state.model = self.model
        
        return {
            "status": "trained",
            "model_id": model_id,
            "hyperparameters_used": params
        }
    
    def get_metrics(self) -> Dict[str, float]:
        if self.model is None:
            raise HTTPException(400, "Model not trained")
        
        y_pred = self.model.predict(app_state.X)
        return {
            "r2": r2_score(app_state.y, y_pred),
            "mse": mean_squared_error(app_state.y, y_pred),
            "rmse": np.sqrt(mean_squared_error(app_state.y, y_pred)),
            "mae": mean_absolute_error(app_state.y, y_pred)
        }
    
    def get_coefficients(self) -> Dict[str, Any]:
        if self.model is None:
            raise HTTPException(400, "Model not trained")
        
        # Обработка разных моделей
        if hasattr(self.model, 'coef_'):
            coefs = self.model.coef_
        else:
            coefs = [0] * len(app_state.X.columns)
        
        # Приведение к списку для случая с одним признаком
        if coefs.ndim == 1:
            coefs = coefs
        elif coefs.ndim == 0:
            coefs = [coefs]
        
        coef_dict = dict(zip(app_state.X.columns, coefs))
        return {
            "coefficients": coef_dict,
            "intercept": self.model.intercept_
        }
```

### Полезные источники

1. [Scikit-learn Linear Models](https://scikit-learn.org/stable/modules/linear_model.html)
2. [Scikit-learn Metrics](https://scikit-learn.org/stable/modules/model_evaluation.html)

---

# Этап 4: Валидация и графики (Validation Layer)

## Задание

Реализовать hold-out валидацию с визуализацией результатов.

### Требования

1. **Эндпоинты для валидации:**
   - `POST /api/validation` — запуск hold-out валидации
   - `GET /api/validation/metrics` — получение метрик train/test
   - `GET /api/validation/plots` — получение графиков

2. **Hold-out валидация:**
   - Разделение на train/test с параметрами test_size и random_state
   - Обучение модели на train
   - Предсказание на test
   - Расчет метрик отдельно для train и test

3. **Графики (matplotlib):**
   - График 1: Actual vs Predicted (для тестовой выборки)
   - График 2: Residuals (гистограмма остатков)
   - График 3: Сравнение метрик train/test (bar chart)

4. **Pydantic-схемы:**
   - `ValidationRequest` — параметры валидации
   - `ValidationMetricsResponse` — метрики train/test
   - `ValidationPlotsResponse` — ссылки на графики

### Пример

**app/schemas/validation_schemas.py:**
```python
from pydantic import BaseModel
from typing import Dict, List, Optional

class ValidationRequest(BaseModel):
    test_size: float = 0.2
    random_state: int = 42

class ValidationMetricsResponse(BaseModel):
    train: Dict[str, float]
    test: Dict[str, float]
    test_size: float

class ValidationPlotsResponse(BaseModel):
    plots: List[Dict[str, str]]
```

**app/services/validation_service.py:**
```python
from sklearn.model_selection import train_test_split
from sklearn.metrics import r2_score, mean_squared_error, mean_absolute_error
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import os
from typing import Dict, Any
from fastapi import HTTPException
from app.core.config import settings
from app.core.state import app_state

class ValidationService:
    def __init__(self):
        self.X_train = None
        self.X_test = None
        self.y_train = None
        self.y_test = None
        self.y_pred_train = None
        self.y_pred_test = None
    
    def run_validation(self, test_size: float = 0.2, random_state: int = 42):
        if app_state.X is None or app_state.y is None:
            raise HTTPException(400, "Data not loaded or target not selected")
        
        if app_state.model is None:
            raise HTTPException(400, "Model not trained")
        
        # Разделение данных
        self.X_train, self.X_test, self.y_train, self.y_test = train_test_split(
            app_state.X, app_state.y, test_size=test_size, random_state=random_state
        )
        
        # Обучение на train
        model_class = app_state.model.__class__
        self.model_train = model_class(**app_state.model.get_params())
        self.model_train.fit(self.X_train, self.y_train)
        
        # Предсказания
        self.y_pred_train = self.model_train.predict(self.X_train)
        self.y_pred_test = self.model_train.predict(self.X_test)
        
        return {
            "train": self._calculate_metrics(self.y_train, self.y_pred_train),
            "test": self._calculate_metrics(self.y_test, self.y_pred_test),
            "test_size": test_size
        }
    
    def _calculate_metrics(self, y_true, y_pred):
        return {
            "r2": r2_score(y_true, y_pred),
            "mse": mean_squared_error(y_true, y_pred),
            "rmse": np.sqrt(mean_squared_error(y_true, y_pred)),
            "mae": mean_absolute_error(y_true, y_pred)
        }
    
    def generate_plots(self) -> Dict[str, Any]:
        if self.y_pred_test is None:
            raise HTTPException(400, "Validation not performed")
        
        os.makedirs(settings.EXPORTS_DIR, exist_ok=True)
        plots = []
        
        # Plot 1: Actual vs Predicted
        fig, ax = plt.subplots(figsize=(10, 6))
        ax.scatter(self.y_test, self.y_pred_test, alpha=0.5)
        ax.plot([self.y_test.min(), self.y_test.max()], [self.y_test.min(), self.y_test.max()], 'r--', lw=2)
        ax.set_xlabel('Actual Values')
        ax.set_ylabel('Predicted Values')
        ax.set_title('Actual vs Predicted (Test Set)')
        path1 = os.path.join(settings.EXPORTS_DIR, 'actual_vs_predicted.png')
        plt.savefig(path1, dpi=100, bbox_inches='tight')
        plt.close()
        plots.append({"name": "actual_vs_predicted", "url": f"/static/plots/actual_vs_predicted.png"})
        
        # Plot 2: Residuals
        fig, ax = plt.subplots(figsize=(10, 6))
        residuals = self.y_test - self.y_pred_test
        sns.histplot(residuals, kde=True, ax=ax)
        ax.axvline(x=0, color='r', linestyle='--')
        ax.set_xlabel('Residuals')
        ax.set_ylabel('Frequency')
        ax.set_title('Residuals Distribution (Test Set)')
        path2 = os.path.join(settings.EXPORTS_DIR, 'residuals.png')
        plt.savefig(path2, dpi=100, bbox_inches='tight')
        plt.close()
        plots.append({"name": "residuals", "url": f"/static/plots/residuals.png"})
        
        # Plot 3: Metrics Comparison
        metrics_train = self._calculate_metrics(self.y_train, self.y_pred_train)
        metrics_test = self._calculate_metrics(self.y_test, self.y_pred_test)
        
        fig, ax = plt.subplots(figsize=(12, 6))
        x = np.arange(len(metrics_train))
        width = 0.35
        ax.bar(x - width/2, list(metrics_train.values()), width, label='Train')
        ax.bar(x + width/2, list(metrics_test.values()), width, label='Test')
        ax.set_xlabel('Metrics')
        ax.set_ylabel('Score')
        ax.set_title('Metrics Comparison: Train vs Test')
        ax.set_xticks(x)
        ax.set_xticklabels(metrics_train.keys())
        ax.legend()
        path3 = os.path.join(settings.EXPORTS_DIR, 'metrics_comparison.png')
        plt.savefig(path3, dpi=100, bbox_inches='tight')
        plt.close()
        plots.append({"name": "metrics_comparison", "url": f"/static/plots/metrics_comparison.png"})
        
        return {"plots": plots}
```

### Полезные источники

1. [Train/Test Split](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html)
2. [Matplotlib Tutorial](https://matplotlib.org/stable/tutorials/index.html)
3. [Seaborn Tutorial](https://seaborn.pydata.org/tutorial.html)

---

# Этап 5: Прогнозирование и экспорт (Prediction & Export Layer)

## Задание

Реализовать прогнозирование на новых данных и экспорт результатов.

### Требования

1. **Эндпоинты для прогнозирования:**
   - `POST /api/predict` — загрузка данных для прогноза
   - `GET /api/predict/results` — получение результатов прогноза
   - `POST /api/predict/compare` — сравнение прогнозов с фактическими (если есть целевая переменная)

2. **Эндпоинты для экспорта:**
   - `GET /api/export/plot/{plot_id}` — экспорт графика (PNG, SVG, PDF)
   - `GET /api/export/data/{data_type}` — экспорт данных в CSV
   - `POST /api/export/all` — экспорт всех результатов в ZIP-архив

3. **Валидация при прогнозировании:**
   - Проверка структуры файла (те же колонки-признаки)
   - Обработка пропусков
   - Применение стандартизации (если использовалась)

4. **Pydantic-схемы:**
   - `PredictRequest` — запрос на прогнозирование
   - `PredictResponse` — результаты прогноза
   - `ExportFormat` — форматы экспорта

### Пример

**app/services/export_service.py:**
```python
import os
import zipfile
import pandas as pd
import io
from typing import Dict, Any
from fastapi import HTTPException, Response
from app.core.config import settings
from app.core.state import app_state

class ExportService:
    def export_plot(self, plot_name: str, format: str = "PNG") -> bytes:
        plot_path = os.path.join(settings.EXPORTS_DIR, f"{plot_name}.png")
        if not os.path.exists(plot_path):
            raise HTTPException(404, f"Plot '{plot_name}' not found")
        
        with open(plot_path, 'rb') as f:
            content = f.read()
        
        return content
    
    def export_data(self, data_type: str) -> Dict[str, Any]:
        if data_type == "coefficients":
            if app_state.model is None:
                raise HTTPException(400, "Model not trained")
            coef_dict = dict(zip(app_state.X.columns, app_state.model.coef_))
            df = pd.DataFrame({
                'feature': coef_dict.keys(),
                'coefficient': coef_dict.values()
            })
        elif data_type == "validation_metrics":
            # Получение метрик из состояния
            if not hasattr(app_state, 'validation_metrics'):
                raise HTTPException(400, "Validation not performed")
            df = pd.DataFrame(app_state.validation_metrics)
        elif data_type == "predictions":
            if app_state.predictions is None:
                raise HTTPException(400, "Predictions not available")
            df = pd.DataFrame(app_state.predictions, columns=['prediction'])
        else:
            raise HTTPException(400, f"Unknown data type: {data_type}")
        
        csv_content = df.to_csv(index=False)
        return {
            "data": csv_content,
            "filename": f"{data_type}.csv"
        }
    
    def export_all(self) -> bytes:
        zip_buffer = io.BytesIO()
        with zipfile.ZipFile(zip_buffer, 'w', zipfile.ZIP_DEFLATED) as zip_file:
            # Добавление графиков
            plot_dir = settings.EXPORTS_DIR
            for plot_file in os.listdir(plot_dir):
                if plot_file.endswith('.png'):
                    file_path = os.path.join(plot_dir, plot_file)
                    zip_file.write(file_path, f"plots/{plot_file}")
            
            # Добавление CSV
            for data_type in ["coefficients", "validation_metrics", "predictions"]:
                try:
                    result = self.export_data(data_type)
                    zip_file.writestr(f"data/{data_type}.csv", result["data"])
                except:
                    continue
        
        zip_buffer.seek(0)
        return zip_buffer.getvalue()
```

### Полезные источники

1. [FastAPI File Responses](https://fastapi.tiangolo.com/advanced/custom-response/)
2. [Python ZIP Module](https://docs.python.org/3/library/zipfile.html)
3. [Matplotlib Export Formats](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.savefig.html)

---

# Этап 6: Обработка ошибок, логирование и документация

## Задание

Реализовать глобальную обработку исключений, логирование и документацию API.

### Требования

1. **Глобальный обработчик ошибок:**
   - Перехват всех исключений
   - Возврат понятных сообщений со статус-кодами
   - Логирование ошибок

2. **Логирование:**
   - Настройка логирования с ротацией
   - Логирование всех запросов
   - Логирование ошибок с traceback
   - Сохранение логов в директорию `logs/`

3. **Документация API:**
   - Все эндпоинты должны иметь теги
   - Докстринги для всех функций
   - Примеры запросов и ответов в Swagger
   - Описание моделей данных

4. **Health-check:**
   - `GET /health` — проверка состояния сервиса

### Пример

**app/core/exceptions.py:**
```python
from fastapi import FastAPI, Request, status
from fastapi.responses import JSONResponse
from fastapi.exceptions import RequestValidationError
import logging
import traceback

logger = logging.getLogger(__name__)

def setup_exception_handlers(app: FastAPI):
    @app.exception_handler(Exception)
    async def global_exception_handler(request: Request, exc: Exception):
        logger.error(f"Unhandled exception: {exc}\n{traceback.format_exc()}")
        return JSONResponse(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            content={
                "detail": "Internal server error",
                "error": str(exc)
            }
        )
    
    @app.exception_handler(RequestValidationError)
    async def validation_exception_handler(request: Request, exc: RequestValidationError):
        logger.warning(f"Validation error: {exc}")
        return JSONResponse(
            status_code=status.HTTP_422_UNPROCESSABLE_ENTITY,
            content={
                "detail": "Validation error",
                "errors": exc.errors()
            }
        )
```

**app/utils/logger.py:**
```python
import logging
import sys
from logging.handlers import RotatingFileHandler
from app.core.config import settings

def setup_logging():
    # Настройка логгера
    logger = logging.getLogger()
    logger.setLevel(logging.INFO)
    
    # Формат логов
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    )
    
    # Консольный обработчик
    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setFormatter(formatter)
    logger.addHandler(console_handler)
    
    # Файловый обработчик с ротацией
    file_handler = RotatingFileHandler(
        f"{settings.LOGS_DIR}/app.log",
        maxBytes=10485760,  # 10 MB
        backupCount=5
    )
    file_handler.setFormatter(formatter)
    logger.addHandler(file_handler)
```

### Полезные источники

1. [FastAPI Error Handling](https://fastapi.tiangolo.com/tutorial/handling-errors/)
2. [Python Logging](https://docs.python.org/3/library/logging.html)
3. [FastAPI OpenAPI Documentation](https://fastapi.tiangolo.com/tutorial/metadata/)

---

# Контрольные вопросы

## Базовые вопросы

1. **Что такое FastAPI и для чего он используется?**
   - Ответ: Современный веб-фреймворк для создания API на Python с поддержкой асинхронности, автоматической документации и валидации данных через Pydantic.

2. **Как в FastAPI организовать маршруты (routing)?**
   - Ответ: Через декораторы `@app.get()`, `@app.post()` и т.д., а также через `APIRouter` для модульной организации.

3. **Для чего нужны Pydantic-схемы?**
   - Ответ: Для валидации данных, автоматической генерации документации и типизации.

4. **Что такое dependency injection в FastAPI?**
   - Ответ: Механизм внедрения зависимостей через параметры функций с использованием `Depends()`.

5. **Как обрабатываются ошибки в FastAPI?**
   - Ответ: Через `HTTPException` и глобальные обработчики исключений.

6. **Какие методы HTTP используются в REST API?**
   - Ответ: GET, POST, PUT, DELETE, PATCH.

7. **Что такое Swagger/OpenAPI и зачем он нужен?**
   - Ответ: Спецификация для описания API, позволяющая автоматически генерировать интерактивную документацию.

8. **Как загрузить файл через FastAPI?**
   - Ответ: Использовать `UploadFile` в параметрах эндпоинта.

9. **Какие метрики регрессии вы знаете?**
   - Ответ: R², MSE, RMSE, MAE, MAPE.

10. **Что такое hold-out валидация?**
    - Ответ: Разделение данных на обучающую и тестовую выборки для оценки качества модели.

---

## Вопросы средней сложности

1. **Опишите архитектуру приложения с разделением на слои (routers, controllers, services). В чем преимущества такого подхода?**
   - Ответ: Разделение ответственности: роутеры для маршрутизации, контроллеры для обработки запросов, сервисы для бизнес-логики. Это обеспечивает переиспользуемость, тестируемость и поддерживаемость кода.

2. **Как в FastAPI реализовать валидацию загружаемых файлов (размер, формат)?**
   - Ответ: Использовать проверки размера через `len(await file.read())`, проверку расширения через `file.filename.endswith('.csv')` и обработку ошибок через `HTTPException`.

3. **Чем отличаются модели Ridge, Lasso и ElasticNet?**
   - Ответ: Ridge — L2-регуляризация, Lasso — L1-регуляризация (может обнулять веса), ElasticNet — комбинация L1 и L2.

4. **Как автоматически определить разделитель в CSV-файле?**
   - Ответ: Перебором возможных разделителей (`,`, `;`, `\t`) с попыткой чтения через `pd.read_csv()`.

5. **В чем разница между обучением на полном датасете и валидацией с разбиением?**
   - Ответ: Обучение на полном датасете дает максимальное качество, но не позволяет оценить обобщающую способность модели. Валидация с разбиением позволяет оценить качество на невидимых данных.

6. **Как в scikit-learn получить веса модели и интерпретировать их?**
   - Ответ: Через атрибуты `model.coef_` и `model.intercept_`. Веса показывают влияние каждого признака на целевую переменную.

7. **Какие HTTP статус-коды вы используете в своем API и в каких случаях?**
   - Ответ: 200 (OK), 201 (Created), 400 (Bad Request), 404 (Not Found), 422 (Validation Error), 500 (Internal Server Error).

8. **Что такое CORS и зачем его настраивать?**
   - Ответ: Cross-Origin Resource Sharing — политика безопасности браузеров. Настройка необходима для разрешения запросов из фронтенда.

9. **Как реализовать логирование с ротацией в Python?**
   - Ответ: Использовать `RotatingFileHandler` из модуля logging.

10. **Как в FastAPI вернуть файл для скачивания?**
    - Ответ: Использовать `FileResponse` или `Response` с `media_type`.

---

## Вопросы продвинутого уровня

1. **Как реализовать асинхронное обучение модели в FastAPI с сохранением статуса задачи?**
   - Ответ: Использовать `BackgroundTasks` для запуска обучения в фоне и сохранять статус в глобальном состоянии или Redis. Реализовать эндпоинт для проверки статуса (`/train/status/{job_id}`).

2. **Какие стратегии кэширования результатов обучения можно применить?**
   - Ответ: Кэширование на основе хэша данных и гиперпараметров. Использование `functools.lru_cache` или внешнего кэша (Redis).

3. **Как реализовать поддержку категориальных признаков (One-Hot Encoding)?**
   - Ответ: Использовать `pd.get_dummies()` или `ColumnTransformer` с `OneHotEncoder` из scikit-learn.

4. **Какие существуют подходы к оптимизации гиперпараметров (Grid Search, Random Search, Bayesian Optimization)?**
   - Ответ: Grid Search — полный перебор, Random Search — случайный поиск, Bayesian Optimization — последовательная оптимизация с вероятностной моделью.

5. **Как реализовать обработку больших файлов (более 100 MB) без загрузки в память?**
   - Ответ: Использовать чанковую обработку через `pd.read_csv(chunksize=...)`, потоковую загрузку через `StreamingResponse`, использование баз данных для хранения.

6. **В чем отличие между глобальным состоянием и использованием Redis для хранения данных между запросами?**
   - Ответ: Глобальное состояние — простое решение для одного процесса/потока, но не масштабируется. Redis обеспечивает общее состояние между несколькими экземплярами приложения.

7. **Как реализовать многопользовательскую работу в вашем API?**
   - Ответ: Использовать JWT аутентификацию, создавать отдельные сессии для каждого пользователя с хранением данных в Redis или PostgreSQL.

8. **Какие паттерны проектирования используются в вашем приложении?**
   - Ответ: Dependency Injection (зависимости), Singleton (состояние приложения), Factory (создание моделей), Strategy (разные модели).

9. **Как протестировать ваш API (юнит-тесты, интеграционные тесты)?**
   - Ответ: Использовать `pytest` и `httpx` для тестирования эндпоинтов. Мокать зависимости и базы данных. Использовать `TestClient` из FastAPI.

10. **Как реализовать сравнение нескольких моделей на одном графике?**
    - Ответ: Обучить несколько моделей, сохранить их метрики и визуализировать на одном графике (bar chart с группировкой).

11. **Что такое SHAP и LIME и как они могут быть использованы для интерпретации линейных моделей?**
    - Ответ: SHAP и LIME — методы интерпретации моделей. Для линейных моделей веса уже являются интерпретацией, но эти методы могут показать вклад каждого признака для конкретного предсказания.

12. **Как организовать экспорт в PDF с включением всех графиков и метрик?**
    - Ответ: Использовать библиотеки `reportlab` или `matplotlib.backends.backend_pdf`.

13. **Как реализовать Health Check с проверкой зависимостей (база данных, Redis)?**
    - Ответ: Эндпоинт `/health`, который проверяет подключение к базе данных и другим сервисам, возвращая статус 200 при успехе и 503 при ошибке.

14. **Какие вы знаете способы мониторинга производительности FastAPI приложения?**
    - Ответ: Prometheus + Grafana, Sentry для ошибок, логирование времени выполнения запросов, профилирование.

15. **Как реализовать Rate Limiting для защиты API от перегрузки?**
    - Ответ: Использовать библиотеки `slowapi` или реализовать свой middleware на основе Redis для подсчета запросов от одного IP.

---

# Критерии оценки

| Балл | Критерий |
|------|----------|
| 21-30 | Все этапы выполнены полностью, код соответствует архитектуре, все эндпоинты работают корректно |
| 16-20 | Выполнены этапы 1-4, есть небольшие недочеты в работе этапа 5 |
| 11-15 | Выполнены этапы 1-3, есть базовая работа с данными и обучением |
| 6-10 | Выполнены этапы 1-2, реализована загрузка и предпросмотр данных |
| 0-5 | Работа не выполнена или не запускается |

---

# Техническая база

---

## 1. Программное обеспечение

### 1.1. Базовое ПО

| Инструмент | Версия | Назначение |
|------------|--------|------------|
| **Python** | 3.10+ | Основной язык программирования |
| **Git** | 2.30+ | Контроль версий |
| **Docker** | 20.10+ | Контейнеризация приложения |
| **Docker Compose** | 2.0+ | Оркестрация контейнеров |
| **VS Code / PyCharm** | Последняя | IDE для разработки |
| **Postman / Insomnia** | Последняя | Тестирование API-эндпоинтов |

### 1.2. Backend-фреймворки и библиотеки

#### Основной фреймворк
| Название | Команда установки | Назначение |
|----------|-------------------|------------|
| **FastAPI** | `pip install fastapi` | Создание REST API |
| **Uvicorn** | `pip install uvicorn` | ASGI-сервер для запуска приложения |

#### Работа с данными
| Название | Команда установки | Назначение |
|----------|-------------------|------------|
| **Pandas** | `pip install pandas` | Загрузка, обработка и анализ данных |
| **NumPy** | `pip install numpy` | Вычисления и работа с массивами |

#### Машинное обучение
| Название | Команда установки | Назначение |
|----------|-------------------|------------|
| **Scikit-learn** | `pip install scikit-learn` | Модели ML и метрики качества |

#### Визуализация
| Название | Команда установки | Назначение |
|----------|-------------------|------------|
| **Matplotlib** | `pip install matplotlib` | Построение графиков и визуализация |
| **Seaborn** | `pip install seaborn` | Улучшенная визуализация данных |

#### Валидация и конфигурация
| Название | Команда установки | Назначение |
|----------|-------------------|------------|
| **Pydantic** | `pip install pydantic` | Валидация данных и схемы API |
| **Pydantic-settings** | `pip install pydantic-settings` | Управление настройками через .env |
| **Python-dotenv** | `pip install python-dotenv` | Загрузка переменных окружения |

#### Дополнительные утилиты
| Название | Команда установки | Назначение |
|----------|-------------------|------------|
| **HTTPX** | `pip install httpx` | HTTP-клиент для тестирования |
| **Pytest** | `pip install pytest` | Написание и запуск тестов |
| **Python-multipart** | `pip install python-multipart` | Обработка multipart/form-data (загрузка файлов) |

---

### 1.3. Полный requirements.txt

```txt
fastapi==0.104.1
uvicorn[standard]==0.24.0
pandas==2.1.3
numpy==1.26.2
scikit-learn==1.3.2
matplotlib==3.8.2
seaborn==0.13.0
pydantic==2.5.0
pydantic-settings==2.1.0
python-dotenv==1.0.0
python-multipart==0.0.6
httpx==0.25.2
pytest==7.4.3
```

---

## 2. Оборудование

### 2.1. Минимальные требования

| Компонент | Требование |
|-----------|------------|
| **Процессор** | Intel Core i3 / AMD Ryzen 3 или выше |
| **Оперативная память** | 4 GB RAM (рекомендуется 8 GB) |
| **Свободное место на диске** | 2 GB |
| **ОС** | Windows 10/11, macOS 12+, Linux (Ubuntu 20.04+) |

### 2.2. Рекомендуемые требования

| Компонент | Требование |
|-----------|------------|
| **Процессор** | Intel Core i5 / AMD Ryzen 5 или выше |
| **Оперативная память** | 8 GB RAM |
| **Свободное место на диске** | 5 GB (для хранения данных и контейнеров) |
| **ОС** | Linux (Ubuntu 22.04) или macOS 14+ |

---

## 3. Онлайн-ресурсы и инструменты

| Ресурс | Назначение |
|--------|------------|
|[Чек-лист для соответствия принципам разработки](https://docs.google.com/spreadsheets/d/1aXfNz79cC0Gttqn24R6ifkErAqzQHtYl/edit?usp=sharing&ouid=108102062564768975378&rtpof=true&sd=true)|Чек-лист команды разработки для тимлида/техлида/старшего разработчика, которые благодаря утвержденному списку правил не пропустят изменения противоречащие принципам разработки. в чистом виде его применять не стоит! Это слишком подробный список, поэтому предварительно сократите его, иначе процесс отправки на ревью и процесс самого ревью сильно затянется. [Подробнее о соответствии чек-листа принципам разработки.](https://habr.com/ru/companies/simbirsoft/articles/808389/) |
| **[Replit](https://replit.com)** | Онлайн-IDE для быстрого прототипирования |
| **[GitHub Codespaces](https://github.com/features/codespaces)** | Облачная среда разработки |
| **[Google Colab](https://colab.research.google.com)** | Выполнение Jupyter-ноутбуков с GPU |
| **[Swagger Editor](https://editor.swagger.io)** | Редактор OpenAPI-спецификаций |
| **[JSON Validator](https://jsonformatter.org)** | Проверка JSON-ответов |

---

## 4. Дополнительные инструменты (опционально)

| Инструмент | Назначение |
|------------|------------|
| **Jupyter Notebook** | Эксперименты и прототипирование |
| **Redis** | Кэширование и хранение состояния |
| **PostgreSQL / SQLite** | Хранение истории экспериментов |
| **Prometheus + Grafana** | Мониторинг производительности |
| **Sentry** | Отслеживание ошибок в продакшене |

---

## 5. Команды для быстрой установки

### Установка всех зависимостей:

```bash
# Создание виртуального окружения
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# Установка зависимостей
pip install -r requirements.txt
```

### Проверка установленных версий:

```bash
python --version
pip list
docker --version
docker-compose --version
```

---

[к оглавлению](#оглавление)