# ВОРКШОП: Визуализация предиктивной аналитики на Frontend
## ECharts vs Chart.js: Предсказание vs Факт

**Продолжительность:** 4 часа (с двумя кофе-брейками по 10 мин)  
**Уровень:** Middle  
**Технологии:** Vue.js 3, Chart.js 4, ECharts 5, TensorFlow.js / ML.js  
**Формат:** Командная работа (2 команды: Chart.js и ECharts)

---

## 📚 ЧАСТЬ 1. ТЕОРЕТИЧЕСКИЙ МИНИМУМ (30 мин)

### 1.1. Настройка Vue.js проекта

```bash
# Создаем проект с Vite (быстрее, чем Vue CLI)
npm create vue@latest viz-workshop
# Выбираем: Vue 3, TypeScript - No, JSX - No, Router - Yes (пригодится)

cd viz-workshop
npm install
```

### 1.2. Подключение библиотек

#### Вариант A: Chart.js (команда "Оптимисты")

Установка:

```bash
npm install chart.js vue-chartjs
```
```javascript
// main.js - глобальная регистрация
import { createApp } from 'vue'
import App from './App.vue'
import VueChartJs from 'vue-chartjs'

const app = createApp(App)
app.use(VueChartJs)
app.mount('#app')
```

Использование в компоненте:

```vue
<template>
  <Line :data="chartData" :options="chartOptions" />
</template>

<script setup>
import { Line } from 'vue-chartjs'
import { Chart as ChartJS, CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend } from 'chart.js'

ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend)

const chartData = {
  labels: ['Янв', 'Фев', 'Мар'],
  datasets: [{ label: 'Продажи', data: [65, 59, 80] }]
}
</script>
```
#### Вариант B: ECharts (команда "Реалисты")

```
bash
npm install echarts
```

```
vue
<template>
  <div ref="chartRef" style="width: 100%; height: 400px;"></div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'

const chartRef = ref(null)

onMounted(() => {
  const chart = echarts.init(chartRef.value)
  chart.setOption({
    xAxis: { data: ['Янв', 'Фев', 'Мар'] },
    yAxis: {},
    series: [{ type: 'line', data: [65, 59, 80] }]
  })
})
</script>
```
### 1.3. Деплой и запуск

```bash
npm run dev       # Локальный сервер на localhost:5173
npm run build     # Сборка для продакшна в папку /dist
```

### 1.4. Теоретическая база

#### Типы визуализаций для сравнения предсказаний vs фактов

| Тип графика | Когда использовать | Лучшая библиотека | Пример использования |
|-------------|-------------------|-------------------|---------------------|
| **Линейный** | Временные ряды, тренды, прогнозирование | ECharts (лучше анимация и сглаживание) | Прогноз продаж по месяцам |
| **Столбчатый** | Сравнение категорий, дискретные данные | Chart.js (проще настройка) | Сравнение плана и факта по отделам |
| **Точечный (Scatter)** | Корреляции, кластеры, выбросы | ECharts (масштабируемость до 100к+ точек) | Предсказанная цена vs фактическая |
| **Свечной (Candlestick)** | Финансовые данные, биржевые графики | ECharts (родная поддержка) | Анализ волатильности акций |
| **Тепловая карта** | Матрицы ошибок, корреляционные матрицы | ECharts (градиенты и интерактивность) | Confusion Matrix для классификации |
| **Ящик с усами (Box Plot)** | Распределение ошибок, выбросы | ECharts (статистические расширения) | Анализ распределения ошибок моделей |
| **Пузырьковая** | Трехмерные данные (x, y, размер) | ECharts (лучшая кастомизация) | Доход, траты, возраст клиентов |

#### Метрики качества прогнозов

- **MAE (Mean Absolute Error)** - средняя абсолютная ошибка
- **RMSE (Root Mean Square Error)** - среднеквадратичная ошибка
- **MAPE (Mean Absolute Percentage Error)** - ошибка в процентах

## Часть 2. Подготовка данных (20 мин)

### 2.1. Генерация синтетических данных (раздаточный материал)

#### Файл `attachments/sales_forecast.csv`

```csv
date,actual,sales_forecast,ml_forecast,weather,holiday
2024-01-01,120,115,118,25,0
2024-01-02,95,110,105,22,0
2024-01-03,145,130,140,28,1
...
2024-12-31,180,170,185,15,1
```

#### Файл `attachments/housing.json`

## 2.2. Загрузка данных в Vue

```javascript
// composables/useDataLoader.js
import { ref } from 'vue'
import Papa from 'papaparse' // npm install papaparse

export function useDataLoader() {
  const data = ref([])
  const loading = ref(false)
  const error = ref(null)

  const loadCSV = async (filePath) => {
    loading.value = true
    try {
      const response = await fetch(filePath)
      const csvText = await response.text()
      const result = Papa.parse(csvText, { header: true, dynamicTyping: true })
      data.value = result.data
    } catch (err) {
      error.value = err.message
    } finally {
      loading.value = false
    }
  }

  const loadJSON = async (filePath) => {
    loading.value = true
    try {
      const response = await fetch(filePath)
      data.value = await response.json()
    } catch (err) {
      error.value = err.message
    } finally {
      loading.value = false
    }
  }

  return { data, loading, error, loadCSV, loadJSON }
}
```

## Часть 3. ML Модели (30 мин)

### 3.1. Линейная регрессия (легкая) — TensorFlow.js

```javascript
// ml/linearRegression.js
import * as tf from '@tensorflow/tfjs'

export async function trainLinearRegression(features, labels) {
  // features: [[x1, x2, ...], ...], labels: [y1, y2, ...]
  
  const xs = tf.tensor2d(features)
  const ys = tf.tensor1d(labels)
  
  const model = tf.sequential()
  model.add(tf.layers.dense({ units: 1, inputShape: [features[0].length] }))
  
  model.compile({ optimizer: 'sgd', loss: 'meanSquaredError' })
  
  await model.fit(xs, ys, { epochs: 100, callbacks: { onEpochEnd: console.log } })
  
  return model
}

// Предсказание
export function predict(model, newFeatures) {
  const input = tf.tensor2d([newFeatures])
  const output = model.predict(input)
  return output.dataSync()[0]
}
```

## 3.2. Кластеризация K-Means (средняя) — простая реализация

```javascript
// ml/kmeans.js
export class KMeans {
  constructor(k = 3, maxIter = 100) {
    this.k = k
    this.maxIter = maxIter
    this.centroids = []
  }

  fit(data) {
    // Инициализация центроидов (случайные точки из данных)
    const shuffled = [...data].sort(() => Math.random() - 0.5)
    this.centroids = shuffled.slice(0, this.k)

    for (let iter = 0; iter < this.maxIter; iter++) {
      // Назначаем кластеры
      const clusters = Array.from({ length: this.k }, () => [])
      data.forEach(point => {
        const distances = this.centroids.map(c => this.distance(point, c))
        const clusterIdx = distances.indexOf(Math.min(...distances))
        clusters[clusterIdx].push(point)
      })

      // Обновляем центроиды
      const newCentroids = clusters.map(cluster => {
        if (cluster.length === 0) return this.centroids[0]
        return cluster.reduce((acc, point) => 
          acc.map((val, i) => val + point[i] / cluster.length), 
          Array(data[0].length).fill(0)
        )
      })

      // Проверка сходимости
      if (this.centroids.every((c, i) => this.distance(c, newCentroids[i]) < 0.001)) {
        break
      }
      this.centroids = newCentroids
    }
  }

  predict(point) {
    const distances = this.centroids.map(c => this.distance(point, c))
    return distances.indexOf(Math.min(...distances))
  }

  distance(a, b) {
    return Math.sqrt(a.reduce((sum, val, i) => sum + (val - b[i]) ** 2, 0))
  }
}
```

## 3.3. SVM (средняя) — используем библиотеку libsvm-js

Установка:

```bash
npm install libsvm-js
```

Код модели:

```
javascript
// ml/svm.js
import SVM from 'libsvm-js'

export function trainSVM(trainingData, labels) {
  const svm = new SVM({
    type: SVM.SVM_TYPES.C_SVC,
    kernel: SVM.KERNEL_TYPES.RBF,
    gamma: 0.5,
    C: 1
  })
  
  svm.train(trainingData, labels)
  return svm
}
```

## Часть 4. Задания (3 часа)

### Задание 1: Разогрев — "Мой первый график" (20 мин)

#### Команда Chart.js

```vue
<template>
  <div class="chart-container">
    <h3>Продажи по месяцам</h3>
    <Bar :data="salesData" :options="options" />
  </div>
</template>

<script setup>
import { Bar } from 'vue-chartjs'
import { ref, onMounted } from 'vue'
import { useDataLoader } from '@/composables/useDataLoader'

const { data, loadCSV } = useDataLoader()

const salesData = ref({
  labels: [],
  datasets: [
    { label: 'Факт', data: [], backgroundColor: '#42A5F5' },
    { label: 'Прогноз', data: [], backgroundColor: '#FFA726' }
  ]
})

onMounted(async () => {
  await loadCSV('/data/sales_forecast.csv')
  
  const months = data.value.map(d => d.date)
  salesData.value.labels = months.slice(0, 12)
  salesData.value.datasets[0].data = data.value.map(d => d.actual).slice(0, 12)
  salesData.value.datasets[1].data = data.value.map(d => d.sales_forecast).slice(0, 12)
})
</script>

```

Команда ECharts:

```
vue
<template>
  <div ref="chartRef" style="height: 400px;"></div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'
import { useDataLoader } from '@/composables/useDataLoader'

const chartRef = ref(null)
const { data, loadCSV } = useDataLoader()

onMounted(async () => {
  await loadCSV('/data/sales_forecast.csv')
  
  const chart = echarts.init(chartRef.value)
  const months = data.value.map(d => d.date).slice(0, 12)
  
  chart.setOption({
    tooltip: { trigger: 'axis' },
    legend: { data: ['Факт', 'Прогноз'] },
    xAxis: { data: months },
    yAxis: {},
    series: [
      { name: 'Факт', type: 'bar', data: data.value.map(d => d.actual).slice(0, 12) },
      { name: 'Прогноз', type: 'bar', data: data.value.map(d => d.sales_forecast).slice(0, 12) }
    ]
  })
})
</script>
```

## Задание 2: Линейная регрессия + прогноз (40 мин)

### Цель

Обучить модель на исторических данных и визуализировать предсказания.

### Шаги

1. Загрузить `housing.json`
2. Обучить линейную регрессию на 80% данных
3. Сделать предсказания на 20% тестовых данных
4. Построить scatter plot: actual vs prediction

### Решение (общее для обеих команд)

```vue
<template>
  <div>
    <ScatterPlot :data="scatterData" />
    <div class="metrics">
      <p>MAE: {{ mae.toFixed(2) }}</p>
      <p>RMSE: {{ rmse.toFixed(2) }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useDataLoader } from '@/composables/useDataLoader'
import { trainLinearRegression, predict } from '@/ml/linearRegression'

const { data, loadJSON } = useDataLoader()
const scatterData = ref({ datasets: [] })
const mae = ref(0)
const rmse = ref(0)

onMounted(async () => {
  await loadJSON('/data/housing.json')
  
  // Подготовка данных
  const features = data.value.map(d => [d.sqft, d.bedrooms])
  const labels = data.value.map(d => d.actual_price)
  
  // Разделение на train/test (80/20)
  const splitIdx = Math.floor(features.length * 0.8)
  const trainFeatures = features.slice(0, splitIdx)
  const trainLabels = labels.slice(0, splitIdx)
  const testFeatures = features.slice(splitIdx)
  const testActual = labels.slice(splitIdx)
  
  // Обучение
  const model = await trainLinearRegression(trainFeatures, trainLabels)
  
  // Предсказания
  const predictions = testFeatures.map(f => predict(model, f))
  
  // Визуализация
  scatterData.value = {
    datasets: [{
      label: 'Actual vs Predicted',
      data: testActual.map((actual, i) => ({ x: actual, y: predictions[i] })),
      backgroundColor: 'rgba(75, 192, 192, 0.6)'
    }]
  }
  
  // Расчет метрик
  mae.value = testActual.reduce((sum, val, i) => sum + Math.abs(val - predictions[i]), 0) / testActual.length
  rmse.value = Math.sqrt(testActual.reduce((sum, val, i) => sum + (val - predictions[i]) ** 2, 0) / testActual.length
})
</script>
```

## ЗАДАНИЕ 3: Кластеризация клиентов (45 мин)

### Цель

Сегментировать клиентов по 2 признакам и показать кластеры на графике.

### Данные

`attachments/customers.csv` 

### Задачи

- Реализовать K-Means кластеризацию (3 кластера)
- Визуализировать точки с цветом кластера
- Отобразить центроиды специальным маркером
- Добавить интерактивность: hover показывает информацию о клиенте

### Ключевой код для ECharts (продвинутый вариант)

```javascript
// В компоненте ECharts
const option = {
  tooltip: {
    trigger: 'item',
    formatter: (params) => {
      const data = params.data
      return `Доход: ${data.value[0]}<br>Траты: ${data.value[1]}<br>Кластер: ${data.cluster}`
    }
  },
  xAxis: { name: 'Доход' },
  yAxis: { name: 'Траты' },
  series: [{
    type: 'scatter',
    data: clusteredData.map(d => ({
      value: [d.income, d.spending],
      cluster: d.cluster,
      itemStyle: { color: colors[d.cluster] }
    })),
    symbolSize: (data) => data.cluster === -1 ? 20 : 10, // центроиды больше
    symbol: (data) => data.cluster === -1 ? 'diamond' : 'circle'
  }]
}
```

## ЗАДАНИЕ 4: Сравнение 3 моделей (45 мин)

### Цель

Визуализировать предсказания трех моделей на одном графике.

### Модели

- Линейная регрессия
- SVM (с библиотекой)
- Простое скользящее среднее (baseline)

### Визуализация

Линейный график с 4 линиями (Факт, LR, SVM, MA)

```vue
<template>
  <div class="comparison-chart">
    <Line :data="comparisonData" :options="comparisonOptions" />
    <div class="legend-extra">
      <span v-for="model in models" :key="model.name">
        <span :style="{ color: model.color }">●</span> {{ model.name }}: {{ model.mae.toFixed(2) }}
      </span>
    </div>
  </div>
</template>

<script setup>
import { Line } from 'vue-chartjs'
import { ref, onMounted } from 'vue'

const comparisonData = ref({
  labels: [],
  datasets: [
    { label: 'Факт', data: [], borderColor: 'black', borderWidth: 3 },
    { label: 'Linear Regression', data: [], borderColor: 'blue', borderDash: [] },
    { label: 'SVM', data: [], borderColor: 'green', borderDash: [5, 5] },
    { label: 'Moving Average', data: [], borderColor: 'red', borderDash: [2, 5] }
  ]
})

// Функция скользящего среднего
function movingAverage(data, windowSize = 3) {
  return data.map((_, idx) => {
    const start = Math.max(0, idx - windowSize + 1)
    const slice = data.slice(start, idx + 1)
    return slice.reduce((a, b) => a + b, 0) / slice.length
  })
}

onMounted(async () => {
  // Загрузка данных и обучение моделей...
  // Заполнение comparisonData.value.datasets
})
</script>
```


## ЗАДАНИЕ 5: Финальный дашборд (40 мин)

### Цель

Собрать всё в единый дашборд с 3 графиками.

### Структура дашборда
## Структура дашборда

| Строка | Левая колонка | Правая колонка |
|--------|---------------|----------------|
| **Заголовок** | 📊 Прогнозирование продаж | |
| **Панель управления** | [Фильтр по дате] | [Выбор модели] |
| **Ряд 1** | **График 1** (Линейный) | **График 2** (Столбчатый ошибок) |
| **Ряд 2 (полная ширина)** | **График 3** (Scatter: предсказание vs факт) + Метрики качества (MAE, RMSE, MAPE) | |



### Ключевые фичи для реализации

- **Реактивность**: при выборе модели обновляются все графики
- **Экспорт графика в PNG** (кнопка)
- **Автообновление данных** (setInterval для имитации реального времени)

### Решение

```vue
<template>
  <div class="dashboard">
    <header>
      <h1>Прогнозирование продаж</h1>
      <select v-model="selectedModel">
        <option value="lr">Linear Regression</option>
        <option value="svm">SVM</option>
        <option value="ma">Moving Average</option>
      </select>
      <button @click="exportCharts">Экспорт</button>
    </header>
    
    <div class="grid">
      <div class="chart-card">
        <LineChart :data="timeSeriesData" />
      </div>
      <div class="chart-card">
        <BarChart :data="errorData" />
      </div>
    </div>
    
    <div class="chart-card full-width">
      <ScatterChart :data="scatterData" />
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import { useDataLoader } from '@/composables/useDataLoader'
import { trainLinearRegression, predict } from '@/ml/linearRegression'
import { trainSVM, predictSVM } from '@/ml/svm'

const { data, loadCSV } = useDataLoader()
const selectedModel = ref('lr')
const models = ref({})

// Вычисляемые данные для графиков
const timeSeriesData = computed(() => {
  // Возвращает данные в зависимости от выбранной модели
})

const errorData = computed(() => {
  // Возвращает ошибки по месяцам
})

const scatterData = computed(() => {
  // Возвращает предсказание vs факт
})

// Обучение моделей при загрузке
onMounted(async () => {
  await loadCSV('/data/sales_forecast.csv')
  
  // Обучаем все модели
  const features = data.value.map(d => [d.weather, d.holiday])
  const labels = data.value.map(d => d.actual)
  
  models.value.lr = await trainLinearRegression(features, labels)
  // models.value.svm = trainSVM(...)
  // models.value.ma = movingAverage(...)
})

// Переключение модели
watch(selectedModel, (newModel) => {
  // Обновляем все графики
})
</script>
```

## Часть 5. Критерии оценки

| Критерий | Баллы |
|----------|-------|
| Загрузка данных из CSV/JSON | 1 |
| Работающий график (любой) | 2 |
| Интерактивность (tooltip, zoom) | 2 |
| ML модель (обучение + предсказание) | 2 |
| Сравнение 2+ моделей | 2 |
| Финальный дашборд | 1 |
| **ИТОГО** | **10** |
