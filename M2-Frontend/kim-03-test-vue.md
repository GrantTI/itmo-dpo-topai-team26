## Вопрос 1. Какая команда используется для создания нового Vue.js 3 проекта с использованием Vite?

A) vue create my-project
B) npm create vite@latest my-project -- --template vue
C) npm init vue@latest my-project
D) vite new my-project --vue

<details> <summary>Показать ответ</summary>
Правильный ответ: B)
</details>

## Вопрос 2. Как правильно настроить прокси в Vite для перенаправления запросов на FastAPI бэкенд?

A) В файле package.json добавить "proxy": "http://localhost:8000"
B) В файле vite.config.js добавить настройку server.proxy
C) В файле main.js настроить axios.defaults.baseURL
D) В файле index.html добавить мета-тег proxy

<details> <summary>Показать ответ</summary>
Правильный ответ: B)
</details>

Настройка прокси выполняется в vite.config.js через объект server.proxy. Пример:

javascript
server: {
  proxy: {
    '/api': {
      target: 'http://localhost:8000',
      changeOrigin: true
    }
  }
}
</details>

## Вопрос 3. Какой хук используется для выполнения запросов к FastAPI после монтирования компонента?

A) created()

B) mounted()

C) onMounted()

D) beforeMount()

<details> <summary>Показать ответ</summary>
Правильный ответ: C)
</details>

onMounted() — это хук Composition API, который выполняется после того, как компонент был смонтирован в DOM. Это идеальное место для выполнения асинхронных запросов к API.

</details>
## Вопрос 4. Как правильно обработать отправку формы в Vue.js 3 с отправкой данных на FastAPI?

A) Использовать v-model и @submit.prevent с fetch

B) Использовать v-bind и @click

C) Использовать v-html и @change

D) Использовать v-text и @input

<details> <summary>Показать ответ</summary>
Правильный ответ: A)

Для обработки формы используется v-model для двусторонней привязки данных полей ввода, а @submit.prevent отменяет стандартную отправку формы и позволяет отправить данные через fetch или axios.

</details>
## Вопрос 5. Какой пакет нужно установить для интеграции axios в проект Vue.js?

A) npm install vue-axios

B) npm install axios

C) npm install @vue/axios

D) npm install vue-resource

<details> <summary>Показать ответ</summary>
Правильный ответ: B)

npm install axios устанавливает axios. Затем его можно импортировать в компонентах: import axios from 'axios'. Это стандартный HTTP-клиент для работы с API.

</details>
## Вопрос 6. Как правильно добавить библиотеку Chart.js в проект Vue.js 3?

A) import Chart from 'chart.js' в компоненте

B) npm install chart.js и import Chart from 'chart.js/auto'

C) Добавить CDN ссылку в index.html

D) Только варианты B и C верны

<details> <summary>Показать ответ</summary>
Правильный ответ: D)

Chart.js можно установить через npm (npm install chart.js) и импортировать в компоненте, или использовать CDN. Для Vue.js 3 также есть обертка vue-chart-3 для более удобной интеграции.

</details>
## Вопрос 7. Что произойдет при выполнении команды npm run dev в Vue.js проекте?

A) Проект соберется для продакшена

B) Запустится сервер разработки с горячей перезагрузкой

C) Будут запущены все тесты

D) Проект будет опубликован на npm

<details> <summary>Показать ответ</summary>
Правильный ответ: B)

npm run dev запускает сервер разработки Vite с горячей перезагрузкой (HMR). При изменении кода страница автоматически обновляется, что ускоряет разработку.

</details>
## Вопрос 8. Как правильно отобразить данные, полученные от FastAPI, в шаблоне Vue.js?

A) {{ data.message }}

B) v-text="data.message"

C) v-html="data.message"

D) Все вышеперечисленные варианты верны

<details> <summary>Показать ответ</summary>
Правильный ответ: D)

Все варианты корректны, но имеют разные применения: {{ }} — интерполяция для текста, v-text — для текстового содержимого, v-html — для HTML-содержимого (нужно быть осторожным с XSS).

</details>
## Вопрос 9. Какой метод используется для обработки ошибок при запросе к FastAPI во Vue.js?

A) .catch() или try/catch

B) .error()

C) .fail()

D) .onError()

<details> <summary>Показать ответ</summary>
Правильный ответ: A)

При использовании fetch ошибки обрабатываются через try/catch, а при использовании axios — через .catch(). Это стандартные способы обработки ошибок в асинхронном JavaScript.

</details>
## Вопрос 10. Как правильно импортировать и использовать стороннюю библиотеку ECharts во Vue.js 3?

A) npm install echarts и import * as echarts from 'echarts'

B) Использовать CDN в index.html

C) Установить vue-echarts для более удобной интеграции

D) Все вышеперечисленные варианты верны

<details> <summary>Показать ответ</summary>
Правильный ответ: D)

ECharts можно использовать несколькими способами: прямая установка через npm, использование CDN или через обертку vue-echarts для более удобной работы с Vue.js 3.

</details>
## Вопрос 11. Какой компонент Vue.js используется для двусторонней привязки данных в полях формы?

A) v-bind

B) v-model

C) v-on

D) v-for

<details> <summary>Показать ответ</summary>
Правильный ответ: B)

v-model реализует двустороннюю привязку данных. Изменения в поле ввода автоматически обновляют данные компонента, и наоборот.

</details>
## Вопрос 12. Как правильно настроить CORS в FastAPI для работы с Vue.js приложением?

A) Добавить app.add_middleware(CORSMiddleware, allow_origins=["*"])

B) Настроить в vite.config.js

C) Добавить заголовки в HTML

D) CORS не нужен при разработке

<details> <summary>Показать ответ</summary>
Правильный ответ: A)

CORS настраивается на стороне сервера (FastAPI) с помощью CORSMiddleware. Для разработки можно разрешить все источники (allow_origins=["*"]), для продакшена нужно указать конкретные домены.

</details>
## Вопрос 13. Как установить Vue Router в проект Vue.js 3?

A) npm install vue-router

B) npm install @vue/router

C) npm install vue-router@4

D) npm install router-vue

<details> <summary>Показать ответ</summary>
Правильный ответ: C)

Для Vue.js 3 используется vue-router@4. Версия 3 используется для Vue.js 2. Важно устанавливать правильную версию для совместимости.

</details>
## Вопрос 14. Что нужно сделать, чтобы использовать SCSS в Vue.js компонентах?

A) Установить sass и добавить lang="scss" в тег <style>

B) Просто добавить lang="scss"

C) Установить node-sass

D) SCSS поддерживается из коробки

<details> <summary>Показать ответ</summary>
Правильный ответ: A)

Для работы с SCSS нужно установить пакет sass (npm install -D sass) и добавить атрибут lang="scss" в тег <style>. Vite автоматически обработает SCSS файлы.

</details>
## Вопрос 15. Как правильно отправить POST запрос с JSON данными на FastAPI из Vue.js?

A) fetch('/api/users', { method: 'POST', body: JSON.stringify(data) })

B) axios.post('/api/users', data)

C) Использовать $http.post()

D) Только варианты A и B верны

<details> <summary>Показать ответ</summary>
Правильный ответ: D)

И fetch, и axios корректно отправляют POST запросы с JSON данными. fetch требует ручного преобразования данных в JSON-строку, а axios делает это автоматически.

</details>
## Вопрос 16. Что такое Composition API в Vue.js 3?

A) Альтернативный способ написания компонентов с использованием setup()

B) Новый синтаксис для создания компонентов

C) Способ организации кода в компонентах

D) Все вышеперечисленные варианты верны

<details> <summary>Показать ответ</summary>
Правильный ответ: D)

Composition API — это новый способ организации кода в Vue.js 3, который позволяет группировать связанную логику вместе и переиспользовать ее между компонентами через функции.

</details>
## Вопрос 17. Как правильно интегрировать стороннюю библиотеку для визуализации данных (например, Chart.js) в проект Vue.js 3?

A) Установить через npm и создать обертку компонента

B) Использовать готовую библиотеку vue-chart-3

C) Импортировать напрямую в компоненте и инициализировать в onMounted

D) Все вышеперечисленные варианты верны

<details> <summary>Показать ответ</summary>
Правильный ответ: D)

Существует несколько подходов: можно создать свой компонент-обертку, использовать готовые решения типа vue-chart-3 или напрямую инициализировать библиотеку в хуке onMounted(). Выбор зависит от конкретных требований проекта.

</details>
## Вопрос 18. Какой URL будет использоваться при запросе /api/users из Vue.js приложения с прокси к FastAPI на порту 8000?

A) http://localhost:3000/api/users

B) http://localhost:8000/api/users

C) http://localhost:3000/users

D) http://localhost:8000/users

<details> <summary>Показать ответ</summary>
Правильный ответ: A)

Запрос отправляется на http://localhost:3000/api/users (адрес Vue.js сервера), прокси перенаправляет его на http://localhost:8000/api/users (адрес FastAPI сервера). Это позволяет обойти CORS.

</details>
## Вопрос 19. Как обработать загрузку файлов во Vue.js и отправить их на FastAPI?

A) Использовать v-model с типом файл

B) Использовать FormData и fetch или axios

C) Использовать v-file директиву

D) Только варианты A и B верны

<details> <summary>Показать ответ</summary>
Правильный ответ: B)

Для загрузки файлов нужно использовать FormData и отправлять через fetch или axios с заголовком Content-Type: multipart/form-data. v-model не работает с файловыми инпутами напрямую — нужно использовать референсы.

</details>
## Вопрос 20. Какой метод используется для отладки Vue.js приложения в браузере?

A) Vue Devtools расширение

B) console.log() в коде

C) Инструменты разработчика браузера

D) Все вышеперечисленные варианты верны

<details> <summary>Показать ответ</summary>
Правильный ответ: D)

Для эффективной отладки используются все методы: Vue Devtools для отслеживания состояния компонентов, console.log() для вывода данных и стандартные инструменты разработчика браузера для отладки сети и выполнения кода.

</details>
