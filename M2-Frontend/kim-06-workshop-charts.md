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
export function predictSVM(svm, features) {
  return svm.predict(features)
}
```
