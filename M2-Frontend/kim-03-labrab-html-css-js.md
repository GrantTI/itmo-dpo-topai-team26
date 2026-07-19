# Лабораторная работа 
## Тема: Визуализация данных. Работа с HTML и CSS

**Цель работы:** Освоить базовые навыки создания веб-страниц с использованием HTML для разметки и CSS для стилизации, включая работу с формами и интерактивными элементами.

**Продолжительность:** 4 академических часа

**Необходимые инструменты:** Текстовый редактор (VS Code, Sublime Text, Notepad++ или любой другой), браузер (Google Chrome, Firefox).

---

## Задание 1. Создание структуры страницы с заголовками

**Описание:** Создайте HTML-страницу с иерархией заголовков от `<h1>` до `<h4>` и абзацами текста.

**Требования:**
1. Используйте теги `<h1>`, `<h2>`, `<h3>`, `<h4>`.
2. Каждый заголовок должен сопровождаться абзацем текста (`<p>`).
3. В тексте используйте теги для выделения: `<strong>` и `<em>`.

**Пример структуры:**

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Моя страница</title>
</head>
<body>
    <h1>Основы веб-технологий</h1>
    <p><strong>Веб-разработка</strong> включает в себя множество технологий...</p>
    
    <h2>HTML</h2>
    <p><em>HTML</em> — это язык разметки гипертекста...</p>
    
    <h3>Структура HTML-документа</h3>
    <p>Документ HTML состоит из <strong>головы</strong> и <strong>тела</strong>...</p>
    
    <h4>Теги заголовков</h4>
    <p>Существует шесть уровней заголовков от h1 до h6...</p>
</body>
</html>

```

## Задание 2. Работа со списками

**Описание:** Создайте маркированный, нумерованный и вложенный списки.

**Требования:**

1. Маркированный список (`<ul>`) с минимум 3 пунктами.
2. Нумерованный список (`<ol>`) с минимум 4 пунктами.
3. Вложенный список (один список внутри другого).

**Пример:**

```html
<h2>Мои любимые технологии</h2>
<ul>
    <li>HTML</li>
    <li>CSS</li>
    <li>JavaScript</li>
</ul>

<h2>Этапы изучения веб-разработки</h2>
<ol>
    <li>Изучение HTML</li>
    <li>Изучение CSS</li>
    <li>Изучение JavaScript</li>
    <li>Изучение фреймворков</li>
</ol>

<h2>Структура HTML-документа</h2>
<ul>
    <li>head
        <ul>
            <li>title</li>
            <li>meta</li>
        </ul>
    </li>
    <li>body
        <ul>
            <li>Заголовки</li>
            <li>Абзацы</li>
            <li>Списки</li>
        </ul>
    </li>
</ul>
```


# Задание 3. Создание гиперссылок

**Описание:** Создайте несколько гиперссылок разного типа.

**Требования:**

- Ссылка на внешний сайт (например, https://developer.mozilla.org).
- Ссылка на внутреннюю страницу (например, page2.html).
- Ссылка на раздел текущей страницы (якорь).
- Ссылка для отправки email (mailto:).
- Все ссылки должны открываться в новой вкладке (атрибут `target="_blank"`).

**Пример:**

```html
<h2>Полезные ссылки</h2>
<ul>
    <li><a href="https://developer.mozilla.org/ru/" target="_blank">MDN Web Docs</a></li>
    <li><a href="page2.html" target="_blank">Страница 2</a></li>
    <li><a href="#section1" target="_blank">Перейти к разделу 1</a></li>
    <li><a href="mailto:example@mail.ru" target="_blank">Написать письмо</a></li>
</ul>

<h2 id="section1">Раздел 1</h2>
<p>Содержание раздела...</p>
```

# Задание 4. Вставка изображений

**Описание:** Добавьте на страницу изображения с разными настройками.

**Требования:**

- Изображение из интернета (по прямой ссылке).
- Изображение из локальной папки (создайте папку `images` и положите в нее картинку).
- Для каждого изображения укажите атрибуты `width`, `height` и `alt`.
- Добавьте изображение, которое является ссылкой (клик на картинку ведет на сайт).

**Пример:**

```html
<h2>Изображения</h2>

<!-- Изображение из интернета -->
<img src="https://via.placeholder.com/300x200" alt="Пример изображения" width="300" height="200">

<!-- Изображение из локальной папки -->
<img src="images/photo.jpg" alt="Мое фото" width="200" height="150">

<!-- Изображение-ссылка -->
<a href="https://example.com" target="_blank">
    <img src="https://via.placeholder.com/150" alt="Кликни на меня" width="150" height="150">
</a>
```

# Задание 5. Создание формы с текстовыми полями

**Описание:** Создайте форму с различными типами текстовых полей.

**Требования:**

- Поле для ввода текста (`<input type="text">`).
- Поле для ввода email (`<input type="email">`).
- Поле для ввода пароля (`<input type="password">`).
- Многострочное текстовое поле (`<textarea>`).
- Все поля должны иметь подписи (`<label>`).
- Добавьте обязательные поля (атрибут `required`).

**Пример:**

```html
<h2>Регистрационная форма</h2>
<form action="#" method="post">
    <div>
        <label for="name">Имя:</label>
        <input type="text" id="name" name="name" placeholder="Введите ваше имя" required>
    </div>
    
    <div>
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" placeholder="example@mail.ru" required>
    </div>
    
    <div>
        <label for="password">Пароль:</label>
        <input type="password" id="password" name="password" placeholder="Минимум 6 символов" required>
    </div>
    
    <div>
        <label for="comment">Комментарий:</label>
        <textarea id="comment" name="comment" rows="4" cols="50" placeholder="Введите ваш комментарий..."></textarea>
    </div>
</form>
```

# Задание 6. Работа с чекбоксами и радиокнопками

**Описание:** Добавьте в форму группы чекбоксов и радиокнопок.

**Требования:**

- Группа чекбоксов (`<input type="checkbox">`) с одинаковым атрибутом `name` для выбора нескольких вариантов.
- Группа радиокнопок (`<input type="radio">`) с одинаковым атрибутом `name` для выбора одного варианта.
- Одна из радиокнопок должна быть выбрана по умолчанию (атрибут `checked`).
- Все элементы должны иметь подписи.

**Пример:**

```html
<h2>Выберите интересующие технологии</h2>
<div>
    <p>Какие технологии вы хотите изучить?</p>
    <label><input type="checkbox" name="tech" value="html"> HTML</label>
    <label><input type="checkbox" name="tech" value="css"> CSS</label>
    <label><input type="checkbox" name="tech" value="js"> JavaScript</label>
    <label><input type="checkbox" name="tech" value="python"> Python</label>
</div>

<div>
    <p>Ваш уровень владения:</p>
    <label><input type="radio" name="level" value="beginner" checked> Начинающий</label>
    <label><input type="radio" name="level" value="middle"> Средний</label>
    <label><input type="radio" name="level" value="advanced"> Продвинутый</label>
</div>
```

# Задание 7. Работа с выпадающими списками

**Описание:** Создайте выпадающий список (`<select>`) и список с множественным выбором.

**Требования:**

- Обычный выпадающий список с выбором одного варианта.
- Список с множественным выбором (атрибут `multiple`).
- Один из вариантов должен быть выбран по умолчанию (атрибут `selected`).

**Пример:**

```html
<h2>Выберите город</h2>
<div>
    <label for="city">Город:</label>
    <select id="city" name="city">
        <option value="msk">Москва</option>
        <option value="spb" selected>Санкт-Петербург</option>
        <option value="kazan">Казань</option>
        <option value="novosib">Новосибирск</option>
    </select>
</div>

<div>
    <label for="hobbies">Интересы (выберите несколько):</label>
    <select id="hobbies" name="hobbies" multiple size="4">
        <option value="music">Музыка</option>
        <option value="sport">Спорт</option>
        <option value="reading">Чтение</option>
        <option value="travel">Путешествия</option>
    </select>
</div>
```

# Задание 8. Поле для загрузки файлов

**Описание:** Добавьте поле для загрузки файлов и кнопку отправки формы.

**Требования:**

- Поле для загрузки файлов (`<input type="file">`).
- Ограничение на тип файлов (атрибут `accept`).
- Кнопка отправки формы (`<input type="submit">`).
- Кнопка сброса формы (`<input type="reset">`).

**Пример:**

```html
<h2>Загрузка файлов</h2>
<form action="#" method="post" enctype="multipart/form-data">
    <div>
        <label for="avatar">Загрузите аватар:</label>
        <input type="file" id="avatar" name="avatar" accept=".jpg,.jpeg,.png,.gif">
    </div>
    
    <div>
        <label for="doc">Загрузите документ (PDF):</label>
        <input type="file" id="doc" name="doc" accept=".pdf">
    </div>
    
    <div>
        <input type="submit" value="Отправить">
        <input type="reset" value="Очистить">
    </div>
</form>
```
# Задание 9. Базовая стилизация элементов CSS

**Описание:** Примените CSS-стили к созданным элементам.

**Требования:**

- Задайте цвет фона страницы, цвет и размер шрифта для всего документа.
- Стилизуйте заголовки (разные цвета для разных уровней).
- Добавьте отступы и рамки для полей формы.
- Измените цвет ссылок при наведении (`:hover`).
- Добавьте стили для списков (измените маркеры, цвет текста).

**Пример CSS:**

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: Arial, sans-serif;
    background-color: #f5f5f5;
    color: #333;
    padding: 20px;
    max-width: 800px;
    margin: 0 auto;
}

h1 {
    color: #2c3e50;
    border-bottom: 3px solid #3498db;
    padding-bottom: 10px;
}

h2 {
    color: #2980b9;
    margin-top: 30px;
}

h3 {
    color: #3498db;
}

h4 {
    color: #7f8c8d;
}

/* Стили для ссылок */
a {
    color: #2980b9;
    text-decoration: none;
    transition: color 0.3s;
}

a:hover {
    color: #e74c3c;
    text-decoration: underline;
}

/* Стили для полей формы */
input, textarea, select {
    width: 100%;
    padding: 10px;
    margin: 5px 0 15px 0;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 16px;
}

/* Стили для списков */
ul {
    list-style-type: square;
    color: #2c3e50;
}

ol {
    color: #2c3e50;
}

ul ul {
    list-style-type: circle;
    margin-left: 20px;
}
```

# Задание 10. Стилизация элементов форм при взаимодействии

**Описание:** Добавьте стили для интерактивных состояний элементов формы.

**Требования:**

- Стилизация полей ввода при наведении мышью (`:hover`).
- Стилизация полей ввода при фокусе (`:focus`).
- Изменение цвета плейсхолдеров.
- Стилизация кнопок при наведении и нажатии.
- Стилизация чекбоксов и радиокнопок (кастомные).

**Пример CSS (продолжение):**

```css
/* Стили для полей при наведении */
input:hover, textarea:hover, select:hover {
    border-color: #3498db;
    background-color: #f0f8ff;
}

/* Стили для полей при фокусе */
input:focus, textarea:focus, select:focus {
    border-color: #2980b9;
    outline: none;
    box-shadow: 0 0 8px rgba(41, 128, 185, 0.3);
    background-color: #fff;
}

/* Стили для плейсхолдеров */
::placeholder {
    color: #999;
    font-style: italic;
}

input:focus::placeholder, textarea:focus::placeholder {
    color: #bbb;
    opacity: 0.5;
}

/* Стили для кнопок */
input[type="submit"], input[type="reset"] {
    background-color: #3498db;
    color: white;
    border: none;
    padding: 12px 24px;
    cursor: pointer;
    border-radius: 4px;
    font-weight: bold;
    transition: all 0.3s;
    width: auto;
}

input[type="submit"]:hover {
    background-color: #2980b9;
    transform: scale(1.02);
}

input[type="reset"] {
    background-color: #95a5a6;
}

input[type="reset"]:hover {
    background-color: #7f8c8d;
}

input[type="submit"]:active, input[type="reset"]:active {
    transform: scale(0.98);
}

/* Кастомные чекбоксы и радиокнопки */
input[type="checkbox"], input[type="radio"] {
    width: auto;
    margin-right: 8px;
    accent-color: #3498db;
    transform: scale(1.2);
}

input[type="checkbox"]:hover, input[type="radio"]:hover {
    cursor: pointer;
    transform: scale(1.3);
}

/* Стили для полей с ошибкой (валидация) */
input:invalid, textarea:invalid {
    border-color: #e74c3c;
}

input:valid, textarea:valid {
    border-color: #2ecc71;
}
