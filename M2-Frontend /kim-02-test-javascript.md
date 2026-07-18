# Тест по JavaScript


**Вопрос 1.** Какой метод используется для получения элемента по его уникальному идентификатору `id`?

-  A) `document.querySelector('#id')`

-  B) `document.getElementById('id')`

-  C) `document.getElementsById('id')`

-  D) `document.getElementById('id')` и `document.querySelector('#id')` -- оба верны

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: D)** Оба метода `getElementById` и `querySelector` с `#` корректно работают для получения элемента по `id`.

</details>

---

**Вопрос 2.** Какой метод возвращает коллекцию (HTMLCollection) всех элементов с указанным тегом, например, все `<p>`?

-  A) `document.getElementsByTag('p')`

-  B) `document.getElementsByTagName('p')`

-  C) `document.querySelectorAll('p')`

-  D) `document.getElementsByTagNames('p')`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `document.getElementsByTagName('p')`

</details>

---

**Вопрос 3.** Что вернет `document.getElementsByClassName('example')`, если на странице нет элементов с классом `example`?

-  A) `null`

-  B) \`\`

-  C) Пустую `HTMLCollection` (похожую на массив, но с длиной 0)

-  D) `false`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: C)** Пустую `HTMLCollection` (это массивоподобный объект с `length = 0`)

</details>

---

**Вопрос 4.** Какой метод позволяет получить первый элемент на странице, соответствующий CSS-селектору `.my-class div > span`?

-  A) `document.querySelector('.my-class div > span')`

-  B) `document.querySelectorAll('.my-class div > span')[0]`

-  C) `document.getElementsBySelector('.my-class div > span')`

-  D) И A, и B являются корректными способами

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: D)** Оба способа рабочие: `querySelector` возвращает первый найденный, а `querySelectorAll` возвращает коллекцию, из которой можно взять нулевой элемент.

</details>

---

**Вопрос 5.** В чем разница между `document.getElementsByClassName('my-class')` и `document.querySelectorAll('.my-class')`?

-  A) Это одно и то же, просто разные названия

-  B) `getElementsByClassName` возвращает "живую" коллекцию (обновляется при изменении DOM), а `querySelectorAll` -- статический `NodeList`

-  C) `getElementsByClassName` работает только на `document`, а `querySelectorAll` -- на любом элементе

-  D) `querySelectorAll` не умеет работать с классами

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `getElementsByClassName` возвращает "живую" коллекцию, которая автоматически обновляется при изменении DOM, а `querySelectorAll` возвращает статический `NodeList`.

</details>

---

**Вопрос 6.** Как получить текстовое содержимое элемента с `id="title"`?

-  A) `document.getElementById('title').innerHTML`

-  B) `document.getElementById('title').textContent`

-  C) `document.getElementById('title').value`

-  D) И A, и B верны (но с разными нюансами)

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: D)** Оба свойства вернут текст, но `innerHTML` может включать HTML-теги, а `textContent` -- только чистый текст.

</details>

---

**Вопрос 7.** Какой метод позволяет изменить CSS-класс элемента?

-  A) `element.className = 'new-class'`

-  B) `element.classList.add('new-class')`

-  C) `element.setAttribute('class', 'new-class')`

-  D) Все варианты верны

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: D)** Все варианты корректно изменяют класс элемента. `classList` предоставляет более удобные методы (`add`, `remove`, `toggle`).

</details>

---

**Вопрос 8.** Что произойдет при выполнении `document.getElementById('nonexistent')`?

-  A) Будет выброшено исключение

-  B) Вернется `null`

-  C) Вернется \`\`

-  D) Вернется пустой объект

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** Вернется `null`, так как элемент не найден.

</details>

---


**Вопрос 9.** Какое событие возникает при щелчке (клике) на элементе?

-  A) `onchange`

-  B) `onmouseover`

-  C) `onclick`

-  D) `onpress`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: C)** `onclick`

</details>

---

**Вопрос 10.** Какое событие срабатывает, когда элемент (например, поле ввода) получает фокус?

-  A) `onfocus`

-  B) `onblur`

-  C) `onselect`

-  D) `onfocusin`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: A)** `onfocus`

</details>

---

**Вопрос 11.** Какое событие срабатывает, когда элемент теряет фокус?

-  A) `onfocusout`

-  B) `onblur`

-  C) `onleave`

-  D) `onchange`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `onblur`

</details>

---

**Вопрос 12.** Какое событие срабатывает при изменении значения в `<select>`?

-  A) `oninput`

-  B) `onchange`

-  C) `onselect`

-  D) `onupdate`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `onchange` срабатывает после подтверждения изменения (например, при потере фокуса или выборе опции).

</details>

---

**Вопрос 13.** Какое событие срабатывает при каждом изменении значения поля `<input>`?
- A) `onkeypress`
- B) `oninput`
- C) `onchange`
- D) `onkeydown`

<details>
<summary>Показать ответ</summary>
**Правильный ответ: B)** `oninput` срабатывает при каждом изменении значения поля ввода, включая ввод с клавиатуры, вставку текста и автозаполнение.
</details>


---

**Вопрос 14.** Какой способ позволяет получить **все** дочерние элементы узла в виде коллекции?
- A) `element.children`
- B) `element.childNodes`
- C) `element.querySelectorAll('*')`
- D) Все варианты верны, но возвращают разные типы коллекций

<details>
<summary>Показать ответ</summary>
**Правильный ответ: D)** Все варианты верны, но возвращают разные коллекции
</details>

---

**Вопрос 15.** Как правильно назначить обработчик события `click` на кнопку с `id="myBtn"`?

-  A) `document.getElementById('myBtn').onclick = function() { ... }`

-  B) `document.getElementById('myBtn').addEventListener('click', function() { ... })`

-  C) `<button onclick="myFunction()">`

-  D) Все варианты корректны

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: D)** Все способы работают, но `addEventListener` предпочтительнее, так как позволяет добавить несколько обработчиков.

</details>

---

**Вопрос 16.** Как остановить всплытие события (event bubbling) в обработчике?

-  A) `event.cancelBubble()`

-  B) `event.stopPropagation()`

-  C) `event.preventDefault()`

-  D) `event.stopImmediatePropagation()`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `event.stopPropagation()` останавливает всплытие события.

</details>

---


**Вопрос 17.** Какой метод консоли выводит информацию в виде таблицы?

-  A) `console.log()`

-  B) `console.table()`

-  C) `console.info()`

-  D) `console.debug()`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `console.table()` отображает данные в виде таблицы для удобного просмотра массивов и объектов.

</details>

---

**Вопрос 18.** Какой метод консоли используется для вывода сообщения об ошибке?

-  A) `console.log()`

-  B) `console.error()`

-  C) `console.warn()`

-  D) `console.exception()`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `console.error()` выводит сообщение как ошибку с соответствующим стилем и иконкой.

</details>

---

**Вопрос 19.** Как в консоли установить точку остановки (breakpoint) в коде для отладки?

-  A) Вставить `debugger;` в код

-  B) Кликнуть на номер строки в панели "Sources" (Источники) в инструментах разработчика

-  C) Использовать `console.break()`

-  D) И A, и B -- корректные способы

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: D)** Оба способа работают: `debugger;` в коде и установка точки остановки в панели "Sources".

</details>

---

**Вопрос 20.** Что выведет в консоль `console.log(typeof null)`?

-  A) `"null"`

-  B) `"object"`

-  C) `""`

-  D) `"number"`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** Это известная ошибка JavaScript: `typeof null` возвращает `"object"`.

</details>

---

**Вопрос 21.** Какая панель в инструментах разработчика позволяет просматривать и изменять CSS-стили в реальном времени?

-  A) Console

-  B) Sources

-  C) Elements

-  D) Network

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: C)** Панель "Elements" (или "Инспектор") позволяет просматривать DOM-дерево и редактировать CSS-стили элементов на лету.

</details>

---


**Вопрос 22.** Что вернет `isNaN("Hello")`?

-  A) `true`

-  B) `false`

-  C) \`\`

-  D) Выбросит ошибку

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: A)** `isNaN()` пытается преобразовать аргумент к числу. "Hello" не может быть преобразовано в число, поэтому возвращает `true`.

</details>

---

**Вопрос 23.** Что вернет `isNaN(42)`?

-  A) `true`

-  B) `false`

-  C) \`\`

-  D) Выбросит ошибку

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `42` -- это число, поэтому `isNaN(42)` возвращает `false`.

</details>

---

**Вопрос 24.** Что вернет `Number.isNaN("Hello")`?

-  A) `true`

-  B) `false`

-  C) \`\`

-  D) Выбросит ошибку

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `Number.isNaN()` более строгий: он возвращает `true` только для значений типа `NaN`. "Hello" -- это строка, поэтому возвращает `false`.

</details>

---

**Вопрос 25.** Какое значение вернет `null ==` ?

-  A) `true`

-  B) `false`

-  C) \`\`

-  D) `null`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: A)** При нестрогом сравнении (`==`) `null` и \`\` считаются равными.

</details>

---

**Вопрос 26.** Какое значение вернет `null ===` ?

-  A) `true`

-  B) `false`

-  C) \`\`

-  D) `null`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** При строгом сравнении (`===`) `null` и \`\` имеют разные типы, поэтому возвращается `false`.

</details>

---

**Вопрос 27.** Что вернет `5 == "5"`?

-  A) `true`

-  B) `false`

-  C) \`\`

-  D) Выбросит ошибку

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: A)** При нестрогом сравнении (`==`) происходит приведение типов, строка "5" преобразуется в число 5, и сравнение дает `true`.

</details>

---

**Вопрос 28.** Что вернет `5 === "5"`?

-  A) `true`

-  B) `false`

-  C) \`\`

-  D) Выбросит ошибку

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** При строгом сравнении (`===`) типы не приводятся, число и строка считаются разными, поэтому возвращается `false`.

</details>

---

**Вопрос 29.** Что вернет `Boolean(null)`?

-  A) `true`

-  B) `false`

-  C) `null`

-  D) \`\`

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: B)** `null` является "ложным" (falsy) значением, поэтому `Boolean(null)` возвращает `false`.

</details>

---

**Вопрос 30.** Что вернет `Boolean("false")`?

-  A) `true`

-  B) `false`

-  C) \`\`

-  D) Выбросит ошибку

<details>

<summary>

Показать ответ

</summary>

 **Правильный ответ: A)** Непустая строка (даже содержащая "false") является "истинным" (truthy) значением, поэтому `Boolean("false")` возвращает `true`.

</details>
