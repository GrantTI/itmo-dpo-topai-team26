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
