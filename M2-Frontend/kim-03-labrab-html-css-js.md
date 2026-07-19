# Лабораторная работа 
## Тема: Визуализация данных

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
