# Методические рекомендации по выполнению лабораторной работы
## КИМ-1.2: «Разработка Backend-сервиса для ML-моделей на FastAPI»

---

# Оглавление

| Раздел | Описание |
|--------|----------|
| [**1. Вводная часть**](#1-вводная-часть) | Тема, цель, формат проведения, время выполнения, необходимые ресурсы |
| [**2. Подготовка к выполнению**](#2-подготовка-к-выполнению) | Теоретический минимум, настройка рабочего окружения |
| [**3. Алгоритм выполнения**](#3-алгоритм-выполнения) | Пошаговые инструкции по реализации всех этапов работы |
| [**4. Оформление результатов**](#4-оформление-результатов) | Требования к формату сдачи, пример README.md |
| [**5. Критерии оценки**](#5-критерии-оценки) | Распределение баллов по этапам (макс. 30), пороговые значения |
| [**6. Самопроверка**](#6-самопроверка) | Чек-лист перед сдачей, типичные ошибки и способы их избежать |
| [**7. Источники**](#7-источники) | Официальная документация и полезные ссылки |

---

## 1. Вводная часть

**Тема:** Разработка Backend-сервиса для ML-моделей на FastAPI  

**Цель:** Разработать бэкенд-сервис на FastAPI, позволяющий работать с ML-моделями: загрузка данных, обучение, валидация, прогнозирование и экспорт результатов.

### Формат проведения
- **Тип работы:** Лабораторная работа
- **Формат:** Индивидуальное выполнение
- **Сдача:** Исходный код в Git, README, скриншоты Swagger UI, отчет с ответами на контрольные вопросы

### Время выполнения
- **Общее время:** 6 академических часов (3-4 недели)
- **Распределение по этапам:**
  - Этап 1 (настройка инфраструктуры) — 1 час
  - Этап 2 (работа с данными) — 1.5 часа
  - Этап 3 (модели и обучение) — 1.5 часа
  - Этап 4 (валидация и графики) — 1 час
  - Этап 5 (прогнозирование и экспорт) — 1 час
  - Этап 6 (обработка ошибок, логирование) — 0.5 часа

### Необходимые ресурсы

#### Программное обеспечение

| Инструмент | Версия | Назначение |
|------------|--------|------------|
| Python | 3.10+ | Основной язык программирования |
| Git | 2.30+ | Контроль версий |
| Docker | 20.10+ | Контейнеризация |
| Docker Compose | 2.0+ | Оркестрация контейнеров |
| VS Code / PyCharm | Последняя | IDE для разработки |
| Postman / Insomnia | Последняя | Тестирование API |

#### Основные библиотеки

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

#### Оборудование
- **Минимальные требования:** Intel Core i3 / 4 GB RAM / 2 GB свободного места
- **Рекомендуемые:** Intel Core i5 / 8 GB RAM / 5 GB свободного места

---

## 2. Подготовка к выполнению

### Теоретический минимум

#### FastAPI основы

1. **Создание приложения:**
   ```python
   from fastapi import FastAPI
   app = FastAPI(title="My API", version="1.0.0")
   ```

2. **Маршруты (роуты):**
   ```python
   @app.get("/items/{item_id}")
   async def get_item(item_id: int):
       return {"item_id": item_id}
   ```

3. **Pydantic-схемы для валидации:**
   ```python
   from pydantic import BaseModel
   
   class ItemCreate(BaseModel):
       name: str
       price: float
   ```

4. **Dependency Injection:**
   ```python
   from fastapi import Depends
   
   def get_db():
       return db_session
   
   @app.get("/items")
   async def get_items(db = Depends(get_db)):
       return db.query(Item).all()
   ```

#### Архитектура приложения

**Трехуровневая архитектура:**
```
Routers (маршруты) → Services (бизнес-логика) → Data Layer (данные)
```

**Структура проекта:**
```
backend/
├── app/
│   ├── api/          # Роутеры (эндпоинты)
│   ├── core/         # Конфигурация, исключения
│   ├── services/     # Бизнес-логика
│   ├── schemas/      # Pydantic-схемы
│   └── utils/        # Вспомогательные функции
├── requirements.txt
├── Dockerfile
└── .env.example
```

#### Scikit-learn модели

| Модель | Особенности | Гиперпараметры |
|--------|-------------|----------------|
| LinearRegression | OLS, без регуляризации | – |
| Ridge | L2-регуляризация | alpha |
| Lasso | L1-регуляризация (обнуляет веса) | alpha |
| ElasticNet | L1 + L2 регуляризация | alpha, l1_ratio |
| SGDRegressor | Стохастический градиентный спуск | learning_rate, max_iter, alpha |

#### Метрики регрессии

| Метрика | Формула | Интерпретация |
|---------|---------|---------------|
| R² | 1 - (SS_res / SS_tot) | Доля объясненной дисперсии (0-1) |
| MSE | (1/n) * Σ(y - ŷ)² | Средняя квадратичная ошибка |
| RMSE | √MSE | MSE в тех же единицах, что и y |
| MAE | (1/n) * Σ|y - ŷ| | Средняя абсолютная ошибка |

### Настройка рабочего окружения

#### Шаг 1. Создание виртуального окружения

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Linux/Mac
python3 -m venv venv
source venv/bin/activate
```

#### Шаг 2. Установка зависимостей

```bash
pip install -r requirements.txt
```

#### Шаг 3. Проверка установленных версий

```bash
python --version
pip list
docker --version
docker-compose --version
```

#### Шаг 4. Настройка .env файла

```bash
cp .env.example .env
# Отредактируйте .env под свои параметры
```

---

## 3. Алгоритм выполнения

### Общая стратегия

1. **Выполняйте этапы последовательно** — каждый следующий этап опирается на предыдущий
2. **Тестируйте каждый эндпоинт** после реализации (через Swagger UI или Postman)
3. **Сохраняйте промежуточные результаты** (commit в Git)
4. **Не бойтесь спрашивать** — преподаватель здесь, чтобы помогать

---

### Этап 1. Настройка инфраструктуры и базовая архитектура

**Цель:** Создать каркас проекта с правильной архитектурой.

#### Шаг 1.1. Создайте структуру проекта

```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py                    # Точка входа
│   ├── api/
│   │   ├── __init__.py
│   │   ├── routes.py              # Главный роутер
│   │   ├── data.py                # Эндпоинты для данных
│   │   ├── models.py              # Эндпоинты для моделей
│   │   ├── validation.py          # Эндпоинты для валидации
│   │   ├── predict.py             # Эндпоинты для прогнозирования
│   │   └── export.py              # Эндпоинты для экспорта
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py              # Настройки (Pydantic Settings)
│   │   ├── exceptions.py          # Глобальные обработчики ошибок
│   │   └── state.py               # Глобальное состояние приложения
│   ├── services/
│   │   ├── __init__.py
│   │   ├── data_service.py        # Работа с данными
│   │   ├── model_service.py       # Работа с моделями
│   │   ├── validation_service.py  # Валидация
│   │   ├── predict_service.py     # Прогнозирование
│   │   └── export_service.py      # Экспорт
│   ├── schemas/
│   │   ├── __init__.py
│   │   ├── data_schemas.py        # Схемы для данных
│   │   ├── model_schemas.py       # Схемы для моделей
│   │   ├── validation_schemas.py  # Схемы для валидации
│   │   └── export_schemas.py      # Схемы для экспорта
│   └── utils/
│       ├── __init__.py
│       ├── data_processor.py      # Обработка данных
│       ├── visualizer.py          # Визуализация
│       └── validators.py          # Валидаторы
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
└── .env.example
```

#### Шаг 1.2. Реализуйте базовое приложение (main.py)

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

@app.get("/health")
async def health():
    return {"status": "healthy"}
```

#### Шаг 1.3. Настройте переменные окружения (config.py)

```python
from pydantic_settings import BaseSettings
from typing import List

class Settings(BaseSettings):
    MAX_FILE_SIZE: int = 10 * 1024 * 1024
    DATA_DIR: str = "./data"
    LOGS_DIR: str = "./logs"
    EXPORTS_DIR: str = "./exports"
    ALLOWED_ORIGINS: List[str] = ["http://localhost:5173"]
    
    class Config:
        env_file = ".env"

settings = Settings()
```

#### Шаг 1.4. Настройте глобальное состояние (state.py)

```python
import pandas as pd
from typing import Optional

class AppState:
    def __init__(self):
        self.df: Optional[pd.DataFrame] = None
        self.X: Optional[pd.DataFrame] = None
        self.y: Optional[pd.Series] = None
        self.target_column: Optional[str] = None
        self.model = None
        self.predictions: Optional[pd.Series] = None

app_state = AppState()
```

#### Шаг 1.5. Настройте Docker

**Dockerfile:**
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
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

#### Шаг 1.6. Проверьте результат

- [ ] Запустите `uvicorn app.main:app --reload`
- [ ] Откройте `http://localhost:8000/docs` (должен быть Swagger UI)
- [ ] Убедитесь, что эндпоинт `/` возвращает `{"message": "ML Models API is running"}`
- [ ] Убедитесь, что эндпоинт `/health` возвращает `{"status": "healthy"}`

---

### Этап 2. Работа с данными (Data Layer)

**Цель:** Реализовать API для загрузки, предпросмотра и обработки данных.

#### Шаг 2.1. Реализуйте Pydantic-схемы (data_schemas.py)

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
    mean: Optional[float] = None
    std: Optional[float] = None
    min: Optional[float] = None
    max: Optional[float] = None

class ProcessDataRequest(BaseModel):
    target_column: str
    standardize: bool = False
```

#### Шаг 2.2. Реализуйте DataService (data_service.py)

**Ключевые методы:**
- `load_csv(file)` — загрузка CSV с автоопределением разделителя
- `get_preview(n_rows=5)` — предпросмотр данных
- `get_stats()` — статистика по колонкам
- `set_target(column_name, standardize=False)` — выбор целевой переменной

**Важные моменты:**
- Определяйте разделитель перебором (`,`, `;`, `\t`)
- Обрабатывайте ошибки через `HTTPException`
- Используйте `app_state` для хранения данных

#### Шаг 2.3. Реализуйте роутер (data.py)

```python
from fastapi import APIRouter, UploadFile, File, HTTPException
from app.services.data_service import DataService
from app.schemas.data_schemas import *

router = APIRouter(prefix="/data", tags=["Data"])
data_service = DataService()

@router.post("/upload", response_model=FileUploadResponse)
async def upload_csv(file: UploadFile = File(...)):
    return await data_service.load_csv(file)

@router.get("/preview", response_model=DataPreviewResponse)
async def preview_data(n_rows: int = 5):
    return data_service.get_preview(n_rows)

@router.post("/process", response_model=ProcessDataResponse)
async def process_data(request: ProcessDataRequest):
    return data_service.set_target(request.target_column, request.standardize)
```

#### Шаг 2.4. Проверьте результат

- [ ] `POST /api/data/upload` — загрузка CSV
- [ ] `GET /api/data/preview` — предпросмотр
- [ ] `POST /api/data/process` — выбор целевой переменной

---

### Этап 3. Модели и обучение (Model Layer)

**Цель:** Реализовать API для работы с моделями.

#### Шаг 3.1. Реализуйте Pydantic-схемы (model_schemas.py)

```python
from pydantic import BaseModel
from typing import Dict, List, Optional, Any

class HyperparameterInfo(BaseModel):
    name: str
    type: str
    default: Any
    range: Optional[List[float]] = None

class ModelInfoResponse(BaseModel):
    id: str
    name: str
    description: str
    hyperparameters: List[HyperparameterInfo]

class TrainRequest(BaseModel):
    model_id: str
    hyperparameters: Optional[Dict[str, Any]] = {}

class MetricsResponse(BaseModel):
    r2: float
    mse: float
    rmse: float
    mae: float
```

#### Шаг 3.2. Реализуйте ModelService (model_service.py)

**Ключевые методы:**
- `get_models()` — список доступных моделей
- `get_model_info(model_id)` — информация о модели
- `train(model_id, hyperparameters)` — обучение модели
- `get_metrics()` — получение метрик
- `get_coefficients()` — получение весов

**Структура моделей:**
```python
MODELS = {
    "linear_regression": {
        "name": "Linear Regression (OLS)",
        "class": LinearRegression,
        "hyperparameters": []
    },
    "ridge": {
        "name": "Ridge Regression",
        "class": Ridge,
        "hyperparameters": [
            {"name": "alpha", "type": "float", "default": 1.0}
        ]
    }
    # ... остальные модели
}
```

#### Шаг 3.3. Реализуйте роутер (models.py)

```python
@router.get("/models", response_model=ModelListResponse)
async def list_models():
    return model_service.get_models()

@router.post("/train")
async def train_model(request: TrainRequest):
    return model_service.train(request.model_id, request.hyperparameters)

@router.get("/train/metrics", response_model=MetricsResponse)
async def get_metrics():
    return model_service.get_metrics()
```

#### Шаг 3.4. Проверьте результат

- [ ] `GET /api/models` — список моделей
- [ ] `POST /api/train` — обучение модели
- [ ] `GET /api/train/metrics` — метрики

---

### Этап 4. Валидация и графики (Validation Layer)

**Цель:** Реализовать hold-out валидацию с визуализацией.

#### Шаг 4.1. Реализуйте Pydantic-схемы (validation_schemas.py)

```python
class ValidationRequest(BaseModel):
    test_size: float = 0.2
    random_state: int = 42

class ValidationMetricsResponse(BaseModel):
    train: Dict[str, float]
    test: Dict[str, float]
```

#### Шаг 4.2. Реализуйте ValidationService (validation_service.py)

**Ключевые методы:**
- `run_validation(test_size, random_state)` — выполнение валидации
- `generate_plots()` — генерация графиков

**Графики:**
1. Actual vs Predicted (scatter)
2. Residuals distribution (histogram)
3. Metrics Comparison (bar chart)

#### Шаг 4.3. Реализуйте роутер (validation.py)

```python
@router.post("/validation")
async def run_validation(request: ValidationRequest):
    return validation_service.run_validation(request.test_size, request.random_state)

@router.get("/validation/plots")
async def get_plots():
    return validation_service.generate_plots()
```

#### Шаг 4.4. Проверьте результат

- [ ] `POST /api/validation` — запуск валидации
- [ ] `GET /api/validation/plots` — получение графиков

---

### Этап 5. Прогнозирование и экспорт (Prediction & Export)

**Цель:** Реализовать прогнозирование и экспорт результатов.

#### Шаг 5.1. Реализуйте ExportService (export_service.py)

**Ключевые методы:**
- `export_plot(plot_name, format)` — экспорт графика
- `export_data(data_type)` — экспорт данных в CSV
- `export_all()` — экспорт всех результатов в ZIP

#### Шаг 5.2. Реализуйте роутеры

**predict.py:**
```python
@router.post("/predict")
async def predict(file: UploadFile):
    return predict_service.predict(file)

@router.get("/predict/results")
async def get_predictions():
    return predict_service.get_results()
```

**export.py:**
```python
@router.get("/export/plot/{plot_name}")
async def export_plot(plot_name: str, format: str = "png"):
    return export_service.export_plot(plot_name, format)

@router.get("/export/data/{data_type}")
async def export_data(data_type: str):
    return export_service.export_data(data_type)

@router.get("/export/all")
async def export_all():
    return export_service.export_all()
```

#### Шаг 5.3. Проверьте результат

- [ ] `POST /api/predict` — прогнозирование
- [ ] `GET /api/export/plot/{plot_name}` — экспорт графика
- [ ] `GET /api/export/data/{data_type}` — экспорт данных

---

### Этап 6. Обработка ошибок, логирование и документация

**Цель:** Реализовать глобальную обработку исключений, логирование и документацию.

#### Шаг 6.1. Настройте глобальные обработчики ошибок (exceptions.py)

```python
@app.exception_handler(Exception)
async def global_exception_handler(request: Request, exc: Exception):
    logger.error(f"Unhandled exception: {exc}")
    return JSONResponse(
        status_code=500,
        content={"detail": "Internal server error", "error": str(exc)}
    )
```

#### Шаг 6.2. Настройте логирование (logger.py)

```python
import logging
from logging.handlers import RotatingFileHandler

def setup_logging():
    logger = logging.getLogger()
    logger.setLevel(logging.INFO)
    
    # Консольный handler
    console_handler = logging.StreamHandler()
    console_handler.setFormatter(logging.Formatter('%(asctime)s - %(message)s'))
    logger.addHandler(console_handler)
    
    # Файловый handler с ротацией
    file_handler = RotatingFileHandler(
        "logs/app.log", maxBytes=10485760, backupCount=5
    )
    file_handler.setFormatter(logging.Formatter('%(asctime)s - %(levelname)s - %(message)s'))
    logger.addHandler(file_handler)
```

#### Шаг 6.3. Добавьте документацию в Swagger

```python
@router.post(
    "/upload",
    response_model=FileUploadResponse,
    summary="Upload CSV file",
    description="Upload a CSV file for ML analysis. Supported separators: comma, semicolon, tab."
)
async def upload_csv(file: UploadFile = File(...)):
    """Загружает CSV файл и возвращает информацию о данных."""
    return await data_service.load_csv(file)
```

#### Шаг 6.4. Проверьте результат

- [ ] Эндпоинт `/health` работает
- [ ] Ошибки обрабатываются корректно
- [ ] Логи пишутся в файл
- [ ] Документация Swagger полная

---

## 4. Оформление результатов

### Требования к формату

**Что должно быть сдано:**

1. **Исходный код в репозитории Git**
   - Чистая структура проекта
   - README.md с инструкцией по запуску
   - Все файлы в правильных директориях

2. **README.md должен содержать:**
   - Название и описание проекта
   - Инструкцию по установке и запуску
   - Примеры запросов и ответов для каждого эндпоинта
   - Скриншоты Swagger UI (`/docs`)

3. **Скриншоты Swagger UI:**
   - Открытые вкладки с эндпоинтами
   - Примеры выполнения запросов

4. **Отчет с ответами на контрольные вопросы:**
   - Все 35 вопросов (базовые, средней сложности, продвинутые)
   - Развернутые ответы с пояснениями

### Пример README.md

```markdown
# ML Models API

## Описание
Backend-сервис для обучения и валидации ML-моделей на FastAPI.

## Установка и запуск

### Локальный запуск
```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

### Docker запуск
```bash
docker-compose up -d
```

## API Эндпоинты

### Загрузка данных
`POST /api/data/upload` — загрузка CSV

**Пример запроса:**
```bash
curl -X POST -F "file=@data.csv" http://localhost:8000/api/data/upload
```

**Пример ответа:**
```json
{
  "filename": "data.csv",
  "rows": 1000,
  "columns": 5,
  "column_names": ["feature1", "feature2", "target"]
}
```

## Документация
Swagger UI: http://localhost:8000/docs

---

## 5. Критерии оценки

| Балл | Критерий |
|------|----------|
| 21-30 | Все этапы выполнены полностью, код соответствует архитектуре, все эндпоинты работают корректно |
| 16-20 | Выполнены этапы 1-4, есть небольшие недочеты в работе этапа 5 |
| 11-15 | Выполнены этапы 1-3, есть базовая работа с данными и обучением |
| 6-10 | Выполнены этапы 1-2, реализована загрузка и предпросмотр данных |
| 0-5 | Работа не выполнена или не запускается |

### Пороговые значения

- **Отлично:** 26-30 баллов (все этапы полностью выполнены)
- **Хорошо:** 21-25 баллов (выполнены все этапы, есть небольшие недочеты)
- **Удовлетворительно:** 16-20 баллов (выполнены основные этапы)
- **Неудовлетворительно:** 0-15 баллов (работа не выполнена)

---

## 6. Самопроверка

### Чек-лист перед сдачей

**Общие требования:**
- [ ] Код лежит в репозитории Git
- [ ] Все коммиты с осмысленными сообщениями
- [ ] Приложение запускается без ошибок
- [ ] Swagger UI доступен по `/docs`

**Этап 1 (Инфраструктура):**
- [ ] Структура проекта соответствует требованию
- [ ] FastAPI приложение настроено (CORS, роутеры)
- [ ] Переменные окружения настроены через Pydantic Settings
- [ ] Dockerfile и docker-compose.yml созданы

**Этап 2 (Данные):**
- [ ] Эндпоинт `/api/data/upload` работает
- [ ] Эндпоинт `/api/data/preview` работает
- [ ] Эндпоинт `/api/data/process` работает
- [ ] Автоопределение разделителя CSV работает

**Этап 3 (Модели):**
- [ ] Эндпоинт `/api/models` возвращает список моделей
- [ ] Эндпоинт `/api/train` обучает модель
- [ ] Эндпоинт `/api/train/metrics` возвращает метрики
- [ ] Все 5 моделей реализованы

**Этап 4 (Валидация):**
- [ ] Эндпоинт `/api/validation` выполняет hold-out валидацию
- [ ] Генерируются 3 графика
- [ ] Графики сохраняются в директорию

**Этап 5 (Прогнозирование и экспорт):**
- [ ] Эндпоинт `/api/predict` работает
- [ ] Эндпоинты экспорта работают
- [ ] ZIP-архив создается корректно

**Этап 6 (Ошибки и логирование):**
- [ ] Глобальный обработчик ошибок настроен
- [ ] Логирование работает
- [ ] Документация Swagger полная
- [ ] Эндпоинт `/health` работает

---

### Типичные ошибки и способы их избежать

| Типичная ошибка | Почему неправильно | Как избежать |
|-----------------|---------------------|--------------|
| Отсутствие __init__.py | Python не видит модули | Создайте __init__.py во всех папках |
| Неправильные пути импорта | Ошибка ModuleNotFoundError | Используйте абсолютные импорты |
| Нет обработки ошибок | Падение приложения | Используйте try/except и HTTPException |
| Нет проверки данных | Ошибки при обучении | Проверяйте наличие данных перед обучением |
| Графики не сохраняются | Ошибка FileNotFoundError | Создавайте директории через os.makedirs |
| Нет CORS | Ошибки при запросах с фронтенда | Настройте CORS middleware |
| Неправильные типы в Pydantic | Ошибки валидации | Проверяйте соответствие типов |
| Нет health-check | Непонятно, работает ли сервис | Добавьте эндпоинт /health |

---

## 7. Источники

1. **FastAPI официальная документация**
   - Сайт: https://fastapi.tiangolo.com
   - Особенно: Tutorial - User Guide

2. **Scikit-learn документация**
   - Сайт: https://scikit-learn.org
   - Особенно: Linear Models, Model Evaluation

3. **Pandas документация**
   - Сайт: https://pandas.pydata.org
   - Особенно: DataFrames, CSV I/O

4. **Matplotlib документация**
   - Сайт: https://matplotlib.org
   - Особенно: Pyplot tutorial

5. **Docker документация**
   - Сайт: https://docs.docker.com
   - Особенно: Dockerfile reference

6. **Pydantic документация**
   - Сайт: https://docs.pydantic.dev
   - Особенно: Models, Settings

---

[к оглавлению](#оглавление)