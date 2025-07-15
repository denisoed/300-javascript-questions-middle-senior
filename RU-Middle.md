

## Фронтенд

42. В чем принципиальная разница между событиями mouseleave и mouseout?

**Ответ:**
Основная разница в том, как эти события обрабатывают всплытие (bubbling) и дочерние элементы:

```javascript
// mouseleave - НЕ всплывает, срабатывает только при выходе из самого элемента
// mouseout - всплывает, срабатывает при выходе из элемента И его дочерних элементов

// HTML
// <div id="parent">
//   <div id="child">Child</div>
// </div>

const parent = document.getElementById('parent');
const child = document.getElementById('child');

// mouseleave - только при выходе из parent
parent.addEventListener('mouseleave', () => {
  console.log('Mouse left parent'); // Срабатывает только при выходе из parent
});

// mouseout - при выходе из parent ИЛИ child
parent.addEventListener('mouseout', () => {
  console.log('Mouse out from parent'); // Срабатывает при движении к child!
});

// Практический пример - tooltip
class Tooltip {
  show(element, message) {
    const tooltip = document.createElement('div');
    tooltip.textContent = message;
    tooltip.className = 'tooltip';
    document.body.appendChild(tooltip);
    
    // Используем mouseleave, чтобы tooltip не исчезал при наведении на его содержимое
    element.addEventListener('mouseleave', () => {
      document.body.removeChild(tooltip);
    });
    
    // Если бы использовали mouseout, tooltip исчезал бы при наведении на дочерние элементы
  }
}

// Аналогично для mouseenter vs mouseover
element.addEventListener('mouseenter', () => {
  // Не всплывает, срабатывает только при входе в элемент
});

element.addEventListener('mouseover', () => {
  // Всплывает, срабатывает при входе в элемент и его дочерние элементы
});
```

43. В каком порядке обрабатываются пользовательские события в DOM (click, mouseover и т.д.)? FIFO или LIFO?

**Ответ:**
События обрабатываются в порядке **FIFO** (First In, First Out) - первый добавленный обработчик выполняется первым.

```javascript
const button = document.createElement('button');

// Порядок выполнения будет: 1, 2, 3
button.addEventListener('click', () => console.log('1')); // Первый
button.addEventListener('click', () => console.log('2')); // Второй
button.addEventListener('click', () => console.log('3')); // Третий

// При клике выведется: 1, 2, 3

// Фазы обработки событий:
// 1. Capturing phase (сверху вниз по DOM)
// 2. Target phase (на целевом элементе)
// 3. Bubbling phase (снизу вверх по DOM)

// Пример с разными фазами
document.addEventListener('click', () => console.log('Document capturing'), true);
parent.addEventListener('click', () => console.log('Parent capturing'), true);
child.addEventListener('click', () => console.log('Child target'));
parent.addEventListener('click', () => console.log('Parent bubbling'));
document.addEventListener('click', () => console.log('Document bubbling'));

// При клике на child:
// Document capturing
// Parent capturing
// Child target
// Parent bubbling
// Document bubbling

// Очередь событий (Event Queue)
// События добавляются в очередь и обрабатываются по порядку
button.addEventListener('click', () => {
  console.log('Sync 1');
  setTimeout(() => console.log('Async 1'), 0);
});

button.addEventListener('click', () => {
  console.log('Sync 2');
  setTimeout(() => console.log('Async 2'), 0);
});

// Результат:
// Sync 1
// Sync 2
// Async 1
// Async 2
```

44. Что такое Event bubbling и Event capturing?

**Ответ:**
Event bubbling и capturing — это два способа распространения событий в DOM дереве.

```javascript
// HTML структура:
// <div id="outer">
//   <div id="middle">
//     <div id="inner">Click me</div>
//   </div>
// </div>

const outer = document.getElementById('outer');
const middle = document.getElementById('middle');
const inner = document.getElementById('inner');

// CAPTURING (погружение) - сверху вниз
// Третий параметр true включает capturing
outer.addEventListener('click', () => console.log('Outer capturing'), true);
middle.addEventListener('click', () => console.log('Middle capturing'), true);
inner.addEventListener('click', () => console.log('Inner capturing'), true);

// BUBBLING (всплытие) - снизу вверх (по умолчанию)
inner.addEventListener('click', () => console.log('Inner bubbling'));
middle.addEventListener('click', () => console.log('Middle bubbling'));
outer.addEventListener('click', () => console.log('Outer bubbling'));

// При клике на inner порядок выполнения:
// 1. Outer capturing
// 2. Middle capturing
// 3. Inner capturing
// 4. Inner bubbling
// 5. Middle bubbling
// 6. Outer bubbling

// Остановка распространения
inner.addEventListener('click', (e) => {
  e.stopPropagation(); // Останавливает дальнейшее распространение
  console.log('Inner - propagation stopped');
});

// Предотвращение действия по умолчанию
const link = document.querySelector('a');
link.addEventListener('click', (e) => {
  e.preventDefault(); // Не переходить по ссылке
  console.log('Link clicked but not followed');
});

// Делегирование событий (Event Delegation)
const list = document.getElementById('todo-list');

// Вместо добавления обработчика каждому элементу списка
list.addEventListener('click', (e) => {
  if (e.target.classList.contains('todo-item')) {
    console.log('Todo item clicked:', e.target.textContent);
  }
  
  if (e.target.classList.contains('delete-btn')) {
    e.target.closest('.todo-item').remove();
  }
});

// Полезные свойства события
document.addEventListener('click', (e) => {
  console.log('target:', e.target);           // Элемент, на который кликнули
  console.log('currentTarget:', e.currentTarget); // Элемент с обработчиком
  console.log('eventPhase:', e.eventPhase);   // 1=capturing, 2=target, 3=bubbling
});

// Пример практического использования
class DropdownMenu {
  constructor(element) {
    this.element = element;
    this.isOpen = false;
    
    // Используем capturing для закрытия при клике вне элемента
    document.addEventListener('click', this.handleDocumentClick.bind(this), true);
  }
  
  handleDocumentClick(e) {
    if (!this.element.contains(e.target) && this.isOpen) {
      this.close();
    }
  }
  
  toggle() {
    this.isOpen = !this.isOpen;
    this.element.classList.toggle('open', this.isOpen);
  }
  
  close() {
    this.isOpen = false;
    this.element.classList.remove('open');
  }
}
```

45. Сравните методы объекта event stopPropagation и stopImmediateProparation.

**Ответ:**

```javascript
// stopPropagation() - останавливает распространение, но выполняет все обработчики текущего элемента
// stopImmediatePropagation() - останавливает распространение И остальные обработчики текущего элемента

const button = document.createElement('button');

// Добавляем несколько обработчиков
button.addEventListener('click', (e) => {
  console.log('Handler 1');
  e.stopPropagation(); // Остановит распространение, но не следующие обработчики
});

button.addEventListener('click', () => {
  console.log('Handler 2'); // Выполнится!
});

button.addEventListener('click', () => {
  console.log('Handler 3'); // Выполнится!
});

// С stopImmediatePropagation
button.addEventListener('click', (e) => {
  console.log('Handler 1');
  e.stopImmediatePropagation(); // Остановит всё
});

button.addEventListener('click', () => {
  console.log('Handler 2'); // НЕ выполнится!
});

// Практический пример
class Modal {
  constructor() {
    this.overlay = document.querySelector('.modal-overlay');
    this.modal = document.querySelector('.modal');
    
    this.overlay.addEventListener('click', this.handleOverlayClick.bind(this));
    this.modal.addEventListener('click', this.handleModalClick.bind(this));
  }
  
  handleOverlayClick(e) {
    // Закрываем модал при клике на overlay
    this.close();
  }
  
  handleModalClick(e) {
    // Предотвращаем закрытие при клике на сам модал
    e.stopPropagation(); // Достаточно stopPropagation
  }
}

// Пример с формой
class FormValidator {
  constructor(form) {
    this.form = form;
    
    // Первый обработчик - валидация
    form.addEventListener('submit', (e) => {
      if (!this.validate()) {
        e.preventDefault();
        e.stopImmediatePropagation(); // Останавливаем другие обработчики submit
        this.showErrors();
      }
    });
    
    // Второй обработчик - аналитика (не выполнится если форма невалидна)
    form.addEventListener('submit', () => {
      this.trackSubmission();
    });
    
    // Третий обработчик - показ лоадера (не выполнится если форма невалидна)
    form.addEventListener('submit', () => {
      this.showLoader();
    });
  }
  
  validate() {
    // Логика валидации
    return false; // Для примера
  }
  
  showErrors() {
    console.log('Показываем ошибки');
  }
  
  trackSubmission() {
    console.log('Отправляем аналитику');
  }
  
  showLoader() {
    console.log('Показываем лоадер');
  }
}

// Сравнительная таблица:
// stopPropagation():
// ✓ Останавливает bubbling/capturing
// ✓ Выполняет остальные обработчики на том же элементе
// ✗ Не останавливает обработчики, добавленные с once: true

// stopImmediatePropagation():
// ✓ Останавливает bubbling/capturing  
// ✓ Останавливает остальные обработчики на том же элементе
// ✓ Останавливает обработчики, добавленные с once: true

// Порядок обработчиков важен!
button.addEventListener('click', (e) => {
  console.log('First handler');
  // Если здесь stopImmediatePropagation(), дальше ничего не выполнится
});

button.addEventListener('click', (e) => {
  console.log('Second handler');
  e.stopImmediatePropagation(); // Остановит третий обработчик
});

button.addEventListener('click', () => {
  console.log('Third handler'); // Не выполнится
});
```

48-56. (остальные вопросы Фронтенд)

46. **Оптимизация производительности веб-страницы**
```javascript
// 1. Ленивая загрузка изображений
const images = document.querySelectorAll('img[data-src]');
const imageObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;
      img.removeAttribute('data-src');
      imageObserver.unobserve(img);
    }
  });
});

images.forEach(img => imageObserver.observe(img));

// 2. Дебаунс и тротлинг
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

function throttle(func, limit) {
  let inThrottle;
  return function(...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}

// 3. Виртуализация списков
class VirtualList {
  constructor(container, items, itemHeight) {
    this.container = container;
    this.items = items;
    this.itemHeight = itemHeight;
    this.visibleCount = Math.ceil(container.clientHeight / itemHeight);
    this.render();
  }
  
  render() {
    const scrollTop = this.container.scrollTop;
    const startIndex = Math.floor(scrollTop / this.itemHeight);
    const endIndex = Math.min(startIndex + this.visibleCount, this.items.length);
    
    // Рендерим только видимые элементы
    const visibleItems = this.items.slice(startIndex, endIndex);
    this.container.innerHTML = visibleItems.map((item, index) => 
      `<div style="height: ${this.itemHeight}px">${item}</div>`
    ).join('');
  }
}
```

47. **Same-origin policy**
```javascript
// Same-origin policy ограничивает доступ к ресурсам с других доменов

// Одинаковый origin:
// https://example.com:443/page1
// https://example.com:443/page2

// Разный origin:
// https://example.com и http://example.com (протокол)
// https://example.com и https://api.example.com (поддомен)
// https://example.com:443 и https://example.com:8080 (порт)

// CORS для разрешения cross-origin запросов
fetch('https://api.example.com/data', {
  headers: {
    'Access-Control-Allow-Origin': '*'
  }
});

// Обход для разработки
const proxy = 'https://cors-anywhere.herokuapp.com/';
fetch(proxy + 'https://api.example.com/data');

// PostMessage для общения между окнами/iframe
window.postMessage({ type: 'data', payload: data }, 'https://trusted-domain.com');

window.addEventListener('message', (event) => {
  if (event.origin !== 'https://trusted-domain.com') return;
  console.log('Received:', event.data);
});
```

48. **Способы хранения данных в браузере**
```javascript
// 1. localStorage - постоянное хранение
localStorage.setItem('user', JSON.stringify({ name: 'John', id: 123 }));
const user = JSON.parse(localStorage.getItem('user'));

// 2. sessionStorage - до закрытия вкладки
sessionStorage.setItem('tempData', 'temporary');

// 3. Cookies - отправляются с каждым запросом
document.cookie = 'username=john; expires=Thu, 18 Dec 2025 12:00:00 UTC; path=/';

// 4. IndexedDB - NoSQL база данных
const request = indexedDB.open('MyDB', 1);
request.onsuccess = (event) => {
  const db = event.target.result;
  const transaction = db.transaction(['users'], 'readwrite');
  const objectStore = transaction.objectStore('users');
  objectStore.add({ id: 1, name: 'John' });
};

// 5. Cache API - для Service Workers
caches.open('v1').then(cache => {
  cache.addAll(['/page1', '/page2', '/styles.css']);
});

// Сравнение:
// localStorage: 5-10MB, постоянное, синхронное
// sessionStorage: 5-10MB, до закрытия вкладки, синхронное  
// Cookies: 4KB, с истечением, отправляются на сервер
// IndexedDB: неограниченное, постоянное, асинхронное
// Cache API: неограниченное, для Service Workers
```

49. **Виртуальный DOM. Зачем нужен?**
```javascript
// Виртуальный DOM - это JavaScript представление реального DOM
// Позволяет оптимизировать обновления через diffing алгоритм

// Пример концепции
const virtualElement = {
  tag: 'div',
  props: { className: 'container' },
  children: [
    { tag: 'h1', props: {}, children: ['Hello World'] }
  ]
};

// Преимущества:
// - Батчинг обновлений
// - Diffing и reconciliation
// - Предсказуемость
// - Кроссбраузерность
```

50. **Debounce vs Throttle**
```javascript
// Debounce - выполняет функцию только после паузы
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// Throttle - ограничивает частоту выполнения
function throttle(func, limit) {
  let inThrottle;
  return function(...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}

// Использование
const debouncedSearch = debounce(searchHandler, 300);
const throttledScroll = throttle(scrollHandler, 100);
```

51. **Critical Rendering Path**
```javascript
// Этапы рендеринга страницы:
// 1. HTML → DOM
// 2. CSS → CSSOM  
// 3. DOM + CSSOM → Render Tree
// 4. Layout (Reflow)
// 5. Paint
// 6. Composite

// Оптимизация:
// - Минификация CSS/JS
// - Critical CSS inline
// - Async/defer для скриптов
// - Resource hints (preload, prefetch)
```

52. **Web Workers**
```javascript
// main.js
const worker = new Worker('worker.js');

worker.postMessage({ data: largeArray });

worker.onmessage = function(e) {
  console.log('Result:', e.data);
};

// worker.js
self.onmessage = function(e) {
  const { data } = e.data;
  
  // Тяжелые вычисления
  const result = data.map(item => expensiveOperation(item));
  
  self.postMessage(result);
};
```

53. **Service Workers**
```javascript
// Регистрация
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js');
}

// sw.js
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open('v1').then(cache => {
      return cache.addAll(['/index.html', '/styles.css']);
    })
  );
});

self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(response => {
      return response || fetch(event.request);
    })
  );
});
```

54. **IndexedDB**
```javascript
// Открытие базы данных
const request = indexedDB.open('MyDB', 1);

request.onupgradeneeded = function(event) {
  const db = event.target.result;
  const objectStore = db.createObjectStore('users', { keyPath: 'id' });
  objectStore.createIndex('email', 'email', { unique: true });
};

request.onsuccess = function(event) {
  const db = event.target.result;
  
  // Добавление данных
  const transaction = db.transaction(['users'], 'readwrite');
  const objectStore = transaction.objectStore('users');
  objectStore.add({ id: 1, name: 'John', email: 'john@example.com' });
};
```

## Проверочные вопросы - Фронтенд

1. **В чем разница между mouseleave и mouseout?**
   - mouseleave не всплывает, mouseout всплывает и срабатывает на дочерних элементах

2. **В каком порядке выполняются фазы событий?**
   - Capturing → Target → Bubbling

3. **Что делает stopImmediatePropagation в отличие от stopPropagation?**
   - Останавливает также остальные обработчики на том же элементе

4. **Назовите 3 способа оптимизации производительности веб-страницы.**
   - Lazy loading, debouncing/throttling, виртуализация списков

5. **В чем разница между debounce и throttle?**
   - Debounce ждет паузы, throttle ограничивает частоту выполнения

// ... existing code ...

## Верстка

55. Объясните разницу между единицами измерения px, em, rem.

**Ответ:**

```css
/* px - абсолютная единица, пиксели */
.element {
  font-size: 16px; /* Всегда 16 пикселей */
  margin: 10px;    /* Всегда 10 пикселей */
}

/* em - относительно размера шрифта родительского элемента */
.parent {
  font-size: 16px;
}

.child {
  font-size: 1.5em; /* 16px * 1.5 = 24px */
  margin: 1em;      /* 24px (размер шрифта самого элемента) */
}

/* rem - относительно размера шрифта корневого элемента (html) */
html {
  font-size: 16px; /* root element */
}

.element {
  font-size: 1.2rem; /* 16px * 1.2 = 19.2px */
  margin: 2rem;      /* 16px * 2 = 32px */
}

/* Практические примеры */
/* Адаптивная типографика */
@media (max-width: 768px) {
  html { font-size: 14px; }
}

.responsive-text {
  font-size: 1.5rem; /* Автоматически изменится на мобильных */
}

/* Компонент с em для масштабирования */
.button {
  font-size: 1em;      /* Наследует от родителя */
  padding: 0.5em 1em;  /* Масштабируется вместе с шрифтом */
  border-radius: 0.25em;
}
```

**Когда использовать:**
- **px**: фиксированные размеры (border, shadow, точные позиции)
- **em**: локальное масштабирование относительно родителя
- **rem**: глобальное масштабирование относительно корня

56. Для чего нужны CSS-переменные? Приведите несколько примеров использования.

**Ответ:**

```css
/* Объявление CSS-переменных */
:root {
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
  --font-size-base: 16px;
  --border-radius: 4px;
  --spacing-unit: 8px;
  --transition-duration: 0.3s;
}

/* Использование переменных */
.button {
  background-color: var(--primary-color);
  border-radius: var(--border-radius);
  padding: calc(var(--spacing-unit) * 2);
  transition: all var(--transition-duration);
}

/* Темная тема */
[data-theme="dark"] {
  --primary-color: #2980b9;
  --background-color: #2c3e50;
  --text-color: #ecf0f1;
}

/* Адаптивные переменные */
:root {
  --container-width: 1200px;
}

@media (max-width: 768px) {
  :root {
    --container-width: 100%;
  }
}

/* Компонентные переменные */
.card {
  --card-bg: white;
  --card-padding: 1rem;
  --card-shadow: 0 2px 4px rgba(0,0,0,0.1);
  
  background: var(--card-bg);
  padding: var(--card-padding);
  box-shadow: var(--card-shadow);
}

/* JavaScript интеграция */
```

```javascript
// Изменение CSS-переменных из JavaScript
document.documentElement.style.setProperty('--primary-color', '#e74c3c');

// Получение значения CSS-переменной
const primaryColor = getComputedStyle(document.documentElement)
  .getPropertyValue('--primary-color');
```

57. **Box-sizing: border-box**
```css
/* Стандартная модель: ширина = content */
.standard-box {
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* Итоговая ширина: 200 + 20*2 + 5*2 = 250px */
}

/* Border-box модель: ширина включает content + padding + border */
.border-box {
  box-sizing: border-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* Итоговая ширина: 200px (content автоматически 150px) */
}

/* Применение ко всем элементам */
*, *::before, *::after {
  box-sizing: border-box;
}
```

58. **Печать (CSS для print media)**
```css
@media print {
  /* Скрывать ненужные элементы */
  .no-print, nav, .sidebar {
    display: none !important;
  }
  
  /* Настройки для печати */
  body {
    font-size: 12pt;
    line-height: 1.4;
    color: black !important;
    background: white !important;
  }
  
  /* Разрывы страниц */
  .page-break {
    page-break-before: always;
  }
  
  /* Ссылки */
  a[href]:after {
    content: " (" attr(href) ")";
  }
  
  /* Размер страницы */
  @page {
    margin: 2cm;
    size: A4;
  }
}
```

59. **Кастомизация форм**
```css
/* Убираем стандартные стили */
input, select, textarea, button {
  appearance: none;
  -webkit-appearance: none;
  -moz-appearance: none;
}

/* Кастомный checkbox */
.custom-checkbox {
  position: relative;
  display: inline-block;
}

.custom-checkbox input {
  opacity: 0;
  position: absolute;
}

.custom-checkbox .checkmark {
  width: 20px;
  height: 20px;
  background: white;
  border: 2px solid #ccc;
  border-radius: 3px;
}

.custom-checkbox input:checked + .checkmark {
  background: #007bff;
  border-color: #007bff;
}

.custom-checkbox input:checked + .checkmark::after {
  content: '✓';
  color: white;
  position: absolute;
  left: 3px;
  top: -2px;
}
```

60. **Progressive рендеринг**
```html
<!-- Critical CSS inline -->
<style>
  /* Критические стили для above-the-fold контента */
  .header { /* стили */ }
  .hero { /* стили */ }
</style>

<!-- Некритические стили асинхронно -->
<link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">

<!-- Resource hints -->
<link rel="dns-prefetch" href="//fonts.googleapis.com">
<link rel="preconnect" href="//api.example.com">
<link rel="prefetch" href="/next-page.html">

<!-- Lazy loading изображений -->
<img src="small-placeholder.jpg" 
     data-src="large-image.jpg" 
     loading="lazy" 
     class="lazy-image">
```

61. **Lazy loading изображений**
```javascript
// Intersection Observer API
const imageObserver = new IntersectionObserver((entries, observer) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;
      img.classList.remove('lazy');
      observer.unobserve(img);
    }
  });
});

// Применяем к изображениям
document.querySelectorAll('img[data-src]').forEach(img => {
  imageObserver.observe(img);
});

// Прогрессивная загрузка с placeholder
.lazy-image {
  filter: blur(5px);
  transition: filter 0.3s;
}

.lazy-image.loaded {
  filter: blur(0);
}
```

62. **CSS методологии**
```css
/* BEM (Block Element Modifier) */
.card { /* Block */ }
.card__title { /* Element */ }
.card__title--large { /* Modifier */ }

/* SMACSS категории */
/* Base */
body, h1, p { margin: 0; }

/* Layout */
.l-header, .l-sidebar, .l-main { /* layouts */ }

/* Modules */
.button, .card, .nav { /* components */ }

/* State */
.is-hidden, .is-active, .is-disabled { /* states */ }

/* Theme */
.theme-dark .card { background: black; }

/* OOCSS принципы */
/* Отделение структуры от оформления */
.btn { /* структура */ }
.btn-primary { /* оформление */ }
```

63. **CSS Grid**
```css
/* Основы Grid */
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* 3 равные колонки */
  grid-template-rows: 100px auto 50px;
  gap: 20px;
  
  /* Именованные области */
  grid-template-areas:
    "header header header"
    "sidebar main main"
    "footer footer footer";
}

.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main { grid-area: main; }
.footer { grid-area: footer; }

/* Адаптивная сетка */
.responsive-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}

/* Выравнивание */
.grid-align {
  justify-items: center; /* по горизонтали */
  align-items: center; /* по вертикали */
  justify-content: space-between; /* контейнер по горизонтали */
  align-content: center; /* контейнер по вертикали */
}
```

64. **GPU ускорение и композитные слои**
```css
/* Принудительное создание композитного слоя */
.gpu-layer {
  transform: translateZ(0); /* hack для создания слоя */
  will-change: transform; /* уведомляем браузер о планируемых изменениях */
  backface-visibility: hidden; /* еще один способ */
}

/* Оптимизированные анимации (только transform и opacity) */
.optimized-animation {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.optimized-animation:hover {
  transform: scale(1.1) translateX(10px);
  opacity: 0.8;
}

/* Избегать (вызывают layout/paint) */
.bad-animation {
  transition: width 0.3s ease; /* плохо - layout */
  transition: background-color 0.3s ease; /* плохо - paint */
}
```

## Проверочные вопросы - Верстка

1. **В чем разница между px, em и rem?**
   - px: абсолютная единица; em: относительно родителя; rem: относительно корневого элемента

2. **Что включает в себя ширина элемента при box-sizing: border-box?**
   - content + padding + border (margin не включается)

3. **Какие CSS свойства лучше всего анимировать для производительности?**
   - transform и opacity (не вызывают layout и paint)

4. **Назовите три принципа CSS методологии BEM.**
   - Block (блок), Element (элемент), Modifier (модификатор)

5. **В чем преимущество CSS Grid перед Flexbox?**
   - Grid работает в двух измерениях, позволяет создавать сложные макеты с именованными областями

**70. Переиспользование SVG**
```html
<defs>
  <symbol id="icon-heart" viewBox="0 0 24 24">
    <path d="..."/>
  </symbol>
</defs>
<use href="#icon-heart"></use>
```

**71. Оптимизация SVG**
- SVGO для минификации
- Удаление неиспользуемых атрибутов
- Объединение путей

**72. Иконочные шрифты**
```css
@font-face {
  font-family: 'Icons';
  src: url('icons.woff2') format('woff2');
}
```

**73. Faux стили**
Искусственное создание жирного/курсивного текста браузером

**74. Shadow DOM**
```javascript
const shadow = element.attachShadow({mode: 'closed'});
```

**75. Custom Elements**
```javascript
class MyElement extends HTMLElement {
  connectedCallback() {
    this.innerHTML = '<p>Custom Element</p>';
  }
}
customElements.define('my-element', MyElement);
```

**76. HTML минификация**
Удаление пробелов не влияет на загрузку из-за gzip сжатия

**77. Canvas контексты**
```javascript
const ctx2d = canvas.getContext('2d');      // 2D графика
const ctxWebGL = canvas.getContext('webgl'); // 3D графика
```

// ... existing code ...

## Дополнительные вопросы для Middle уровня

65. Что такое Tree Shaking и как оно работает?

**Ответ:**
Tree Shaking — это техника удаления неиспользуемого кода из финального бандла.

```javascript
// math.js
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) { // Не используется
  return a - b;
}

export function multiply(a, b) { // Не используется
  return a * b;
}

// main.js
import { add } from './math.js'; // Только add попадет в бандл

console.log(add(2, 3));

// Webpack configuration для tree shaking
module.exports = {
  mode: 'production', // Включает tree shaking
  optimization: {
    usedExports: true,
    sideEffects: false // Указывает, что модули не имеют побочных эффектов
  }
};

// package.json
{
  "sideEffects": ["*.css", "*.scss"] // CSS файлы имеют побочные эффекты
}
```

66. Объясните разницу между imperative и declarative программирования. Приведите примеры.

**Ответ:**

**Императивный стиль** — вы говорите компьютеру, какие шаги выполнить. Это как рецепт: «сначала сделай A, потом B, потом C». Вы сами контролируете порядок и изменения состояния.

**Декларативный стиль** — вы говорите, чего хотите достичь, но не указываете, как это сделать. Система сама решает, как выполнить вашу цель.

```javascript
// 🔍 Примеры

// ИМПЕРАТИВНО - КАК делать
const numbers = [];
for (let i = 0; i < 20; i++) {
  if (i < 5) {
    numbers.push(i);
  }
}
console.log(numbers); // [0, 1, 2, 3, 4]
// Здесь вы сами контролируете каждую итерацию и состояние массива

// ДЕКЛАРАТИВНО - ЧТО получить
const numbersDeclarative = Array.from({length: 20}, (_, i) => i).filter(x => x < 5);
console.log(numbersDeclarative); // [0, 1, 2, 3, 4]
// Вы просто описываете, что хотите — все числа меньше 5

// Другие примеры:

// Императивный подход - DOM
function imperativeToggleVisibility(elements) {
  for (let i = 0; i < elements.length; i++) {
    if (elements[i].style.display === 'none') {
      elements[i].style.display = 'block';
    } else {
      elements[i].style.display = 'none';
    }
  }
}

// Декларативный подход - React
function DeclarativeComponent({ isVisible, children }) {
  return isVisible ? <div>{children}</div> : null;
}

// Императивное удвоение массива
function imperativeDouble(numbers) {
  const result = [];
  for (let i = 0; i < numbers.length; i++) {
    result[i] = numbers[i] * 2;
  }
  return result;
}

// Декларативное удвоение массива
const declarativeDouble = numbers => numbers.map(n => n * 2);
```

**🧠 Как запомнить?**
- **Императивный** = **как** делать (как-то → how)
- **Декларативный** = **что** получить (что-то → what)

**🛠 Когда что использовать?**
- Когда важен тонкий контроль и порядок действий → **императивный**
- Когда важно кратко и понятно описать цель → **декларативный**

**Вывод:**
- **Императивный стиль** — вы детально управляете процессом
- **Декларативный стиль** — вы задаёте цель, система сама решает, как её достичь

67. Что такое Functional Programming? Назовите основные принципы.

**Ответ:**

```javascript
// ПРИНЦИПЫ ФУНКЦИОНАЛЬНОГО ПРОГРАММИРОВАНИЯ:

// 1. Чистые функции (Pure Functions)
// Плохо - не чистая функция
let counter = 0;
function impureIncrement() {
  return ++counter; // Изменяет внешнее состояние
}

// Хорошо - чистая функция
function pureIncrement(value) {
  return value + 1; // Не изменяет внешнее состояние
}

// 2. Неизменяемость (Immutability)
// Плохо - мутация
function addItemMutable(array, item) {
  array.push(item); // Изменяет исходный массив
  return array;
}

// Хорошо - иммутабельность
function addItemImmutable(array, item) {
  return [...array, item]; // Возвращает новый массив
}

// 3. Функции высшего порядка (Higher-Order Functions)
function createMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

// 4. Композиция функций
const pipe = (...fns) => (value) => fns.reduce((acc, fn) => fn(acc), value);

const addOne = x => x + 1;
const multiplyByTwo = x => x * 2;
const square = x => x * x;

const complexOperation = pipe(addOne, multiplyByTwo, square);
console.log(complexOperation(3)); // ((3 + 1) * 2)² = 64

// 5. Рекурсия вместо циклов
// Императивный подход
function factorialIterative(n) {
  let result = 1;
  for (let i = 1; i <= n; i++) {
    result *= i;
  }
  return result;
}

// Функциональный подход
function factorialRecursive(n) {
  return n <= 1 ? 1 : n * factorialRecursive(n - 1);
}

// 6. Каррирование и частичное применение
const curry = (fn) => {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return (...nextArgs) => curried(...args, ...nextArgs);
  };
};

const add = curry((a, b, c) => a + b + c);
const add5 = add(5);
const add5and10 = add5(10);
console.log(add5and10(15)); // 30

// 7. Монады (упрощенно)
class Maybe {
  constructor(value) {
    this.value = value;
  }
  
  static of(value) {
    return new Maybe(value);
  }
  
  map(fn) {
    return this.value == null ? Maybe.of(null) : Maybe.of(fn(this.value));
  }
  
  flatMap(fn) {
    return this.value == null ? Maybe.of(null) : fn(this.value);
  }
}

// Безопасная цепочка операций
const result = Maybe.of('  hello world  ')
  .map(s => s.trim())
  .map(s => s.toUpperCase())
  .map(s => s.replace(/\s+/g, '-'));

console.log(result.value); // HELLO-WORLD
```

68. Что такое микрофронтенды? Какие есть подходы к их реализации?

**Ответ:**

```javascript
// Микрофронтенды - архитектурный подход для разделения фронтенд приложения

// 1. BUILD-TIME INTEGRATION (сборка)
// package.json
{
  "dependencies": {
    "@company/header-mf": "^1.0.0",
    "@company/footer-mf": "^1.0.0"
  }
}

// main.js
import Header from '@company/header-mf';
import Footer from '@company/footer-mf';

// 2. RUN-TIME INTEGRATION через Module Federation (Webpack 5)
// webpack.config.js - Host приложение
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'host',
      remotes: {
        headerApp: 'header@http://localhost:3001/remoteEntry.js',
        footerApp: 'footer@http://localhost:3002/remoteEntry.js',
      },
    }),
  ],
};

// Использование
const Header = React.lazy(() => import('headerApp/Header'));
const Footer = React.lazy(() => import('footerApp/Footer'));

function App() {
  return (
    <div>
      <Suspense fallback={<div>Loading Header...</div>}>
        <Header />
      </Suspense>
      <main>Content</main>
      <Suspense fallback={<div>Loading Footer...</div>}>
        <Footer />
      </Suspense>
    </div>
  );
}

// 3. WEB COMPONENTS
// header-component.js
class HeaderComponent extends HTMLElement {
  connectedCallback() {
    this.innerHTML = `
      <header>
        <h1>Micro Frontend Header</h1>
      </header>
    `;
  }
}

customElements.define('mf-header', HeaderComponent);

// Использование в любом фреймворке
<mf-header></mf-header>

// 4. IFRAME подход
function MicroFrontend({ name, host, document }) {
  useEffect(() => {
    const script = document.createElement('script');
    script.src = `${host}/asset-manifest.json`;
    document.head.appendChild(script);
    
    return () => {
      document.head.removeChild(script);
    };
  }, [host, document]);

  return <iframe src={`${host}/${name}`} />;
}

// 5. SINGLE-SPA подход
import { registerApplication, start } from 'single-spa';

registerApplication({
  name: '@company/header',
  app: () => System.import('@company/header'),
  activeWhen: ['/'],
});

registerApplication({
  name: '@company/dashboard',
  app: () => System.import('@company/dashboard'),
  activeWhen: ['/dashboard'],
});

start();

// Коммуникация между микрофронтендами
// 1. Custom Events
window.dispatchEvent(new CustomEvent('user-logged-in', {
  detail: { userId: 123, userName: 'John' }
}));

window.addEventListener('user-logged-in', (event) => {
  console.log('User logged in:', event.detail);
});

// 2. Shared State (Redux/Zustand)
const SharedStore = {
  state: { user: null },
  listeners: [],
  
  setState(newState) {
    this.state = { ...this.state, ...newState };
    this.listeners.forEach(listener => listener(this.state));
  },
  
  subscribe(listener) {
    this.listeners.push(listener);
    return () => {
      this.listeners = this.listeners.filter(l => l !== listener);
    };
  }
};

// 3. PostMessage API
// Родительское приложение
window.postMessage({
  type: 'USER_UPDATED',
  payload: { user: userData }
}, '*');

// Дочерний микрофронтенд
window.addEventListener('message', (event) => {
  if (event.data.type === 'USER_UPDATED') {
    setUser(event.data.payload.user);
  }
});
```

69. Расскажите о Performance API. Как измерить производительность веб-приложения?

**Ответ:**

```javascript
// PERFORMANCE API для измерения производительности

// 1. Navigation Timing API
const perfData = performance.getEntriesByType('navigation')[0];
console.log('DNS lookup:', perfData.domainLookupEnd - perfData.domainLookupStart);
console.log('TCP connection:', perfData.connectEnd - perfData.connectStart);
console.log('Request:', perfData.responseStart - perfData.requestStart);
console.log('Response:', perfData.responseEnd - perfData.responseStart);
console.log('DOM processing:', perfData.domComplete - perfData.domLoading);

// 2. Resource Timing API
performance.getEntriesByType('resource').forEach(resource => {
  console.log(`${resource.name}: ${resource.duration}ms`);
});

// 3. User Timing API - кастомные измерения
performance.mark('function-start');

// Ваш код
function expensiveOperation() {
  // Сложная операция
  for (let i = 0; i < 1000000; i++) {
    Math.sqrt(i);
  }
}

performance.mark('function-end');
performance.measure('function-duration', 'function-start', 'function-end');

const measure = performance.getEntriesByName('function-duration')[0];
console.log(`Function took: ${measure.duration}ms`);

// 4. Web Vitals - ключевые метрики
// Core Web Vitals
function measureWebVitals() {
  // Largest Contentful Paint (LCP)
  new PerformanceObserver((entryList) => {
    const entries = entryList.getEntries();
    const lastEntry = entries[entries.length - 1];
    console.log('LCP:', lastEntry.startTime);
  }).observe({ entryTypes: ['largest-contentful-paint'] });

  // First Input Delay (FID)
  new PerformanceObserver((entryList) => {
    entryList.getEntries().forEach((entry) => {
      console.log('FID:', entry.processingStart - entry.startTime);
    });
  }).observe({ entryTypes: ['first-input'] });

  // Cumulative Layout Shift (CLS)
  let clsValue = 0;
  new PerformanceObserver((entryList) => {
    entryList.getEntries().forEach((entry) => {
      if (!entry.hadRecentInput) {
        clsValue += entry.value;
      }
    });
    console.log('CLS:', clsValue);
  }).observe({ entryTypes: ['layout-shift'] });
}

// 5. Memory API
if ('memory' in performance) {
  console.log('Used JS Heap Size:', performance.memory.usedJSHeapSize);
  console.log('Total JS Heap Size:', performance.memory.totalJSHeapSize);
  console.log('JS Heap Size Limit:', performance.memory.jsHeapSizeLimit);
}

// 6. Intersection Observer для измерения видимости
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      performance.mark(`${entry.target.id}-visible`);
    }
  });
});

// 7. Performance Budget мониторинг
class PerformanceBudget {
  constructor(budgets) {
    this.budgets = budgets;
    this.checkBudgets();
  }
  
  checkBudgets() {
    const navigation = performance.getEntriesByType('navigation')[0];
    
    Object.entries(this.budgets).forEach(([metric, budget]) => {
      const value = this.getMetricValue(navigation, metric);
      if (value > budget) {
        console.warn(`Budget exceeded for ${metric}: ${value}ms > ${budget}ms`);
        // Отправить в аналитику
        this.reportBudgetViolation(metric, value, budget);
      }
    });
  }
  
  getMetricValue(navigation, metric) {
    switch (metric) {
      case 'FCP':
        return performance.getEntriesByName('first-contentful-paint')[0]?.startTime || 0;
      case 'LCP':
        return performance.getEntriesByType('largest-contentful-paint').pop()?.startTime || 0;
      case 'TTI':
        return navigation.domInteractive - navigation.navigationStart;
      default:
        return 0;
    }
  }
  
  reportBudgetViolation(metric, actual, budget) {
    // Отправка данных в систему мониторинга
    fetch('/api/performance-violation', {
      method: 'POST',
      body: JSON.stringify({ metric, actual, budget, url: location.href })
    });
  }
}

// Использование
new PerformanceBudget({
  FCP: 2000,  // First Contentful Paint < 2s
  LCP: 2500,  // Largest Contentful Paint < 2.5s
  TTI: 3000   // Time to Interactive < 3s
});

// 8. Real User Monitoring (RUM)
class RUMCollector {
  constructor() {
    this.metrics = {};
    this.collectMetrics();
  }
  
  collectMetrics() {
    // Collect navigation timing
    window.addEventListener('load', () => {
      setTimeout(() => {
        const navigation = performance.getEntriesByType('navigation')[0];
        this.metrics.loadTime = navigation.loadEventEnd - navigation.navigationStart;
        this.metrics.domContentLoaded = navigation.domContentLoadedEventEnd - navigation.navigationStart;
        this.sendMetrics();
      }, 0);
    });
    
    // Collect errors
    window.addEventListener('error', (event) => {
      this.metrics.errors = (this.metrics.errors || 0) + 1;
    });
  }
  
  sendMetrics() {
    navigator.sendBeacon('/api/rum', JSON.stringify({
      ...this.metrics,
      url: location.href,
      userAgent: navigator.userAgent,
      timestamp: Date.now()
    }));
  }
}

new RUMCollector();
```

70. Что такое Server-Side Rendering (SSR) и Static Site Generation (SSG)? В чем разница?

**Ответ:**

```javascript
// SSR - Server-Side Rendering
// HTML генерируется на сервере для каждого запроса

// Next.js SSR пример
export async function getServerSideProps(context) {
  // Выполняется на каждом запросе
  const res = await fetch(`https://api.example.com/posts/${context.params.id}`);
  const post = await res.json();

  return {
    props: { post }, // Передается в компонент как props
  };
}

function PostPage({ post }) {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
}

// SSG - Static Site Generation  
// HTML генерируется во время сборки

// Next.js SSG пример
export async function getStaticProps() {
  // Выполняется во время сборки
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  return {
    props: { posts },
    revalidate: 3600, // Регенерация каждый час (ISR)
  };
}

export async function getStaticPaths() {
  // Генерируем пути для статических страниц
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  const paths = posts.map((post) => ({
    params: { id: post.id.toString() },
  }));

  return {
    paths,
    fallback: 'blocking', // Генерировать новые страницы по требованию
  };
}

// CSR - Client-Side Rendering (для сравнения)
function ClientSidePost() {
  const [post, setPost] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/post/1')
      .then(res => res.json())
      .then(data => {
        setPost(data);
        setLoading(false);
      });
  }, []);

  if (loading) return <div>Loading...</div>;
  return <div>{post.title}</div>;
}

// СРАВНЕНИЕ:

// SSR:
// ✅ SEO-friendly
// ✅ Быстрый первый рендер
// ✅ Работает без JavaScript
// ❌ Медленная навигация
// ❌ Нагрузка на сервер
// ❌ Время ответа сервера

// SSG:
// ✅ SEO-friendly  
// ✅ Очень быстрая загрузка
// ✅ CDN кэширование
// ✅ Низкая нагрузка на сервер
// ❌ Данные могут устареть
// ❌ Долгое время сборки

// CSR:
// ✅ Быстрая навигация
// ✅ Интерактивность
// ✅ Низкая нагрузка на сервер
// ❌ Плохо для SEO
// ❌ Медленный первый рендер
// ❌ Не работает без JavaScript

// Гибридный подход - Incremental Static Regeneration (ISR)
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  return {
    props: { posts },
    revalidate: 60, // Регенерировать через 60 секунд если есть запросы
  };
}

// React Streaming SSR (React 18)
import { renderToReadableStream } from 'react-dom/server';
import { Suspense } from 'react';

function App() {
  return (
    <html>
      <body>
        <header>Static Header</header>
        <Suspense fallback={<div>Loading main content...</div>}>
          <MainContent />
        </Suspense>
        <Suspense fallback={<div>Loading sidebar...</div>}>
          <Sidebar />
        </Suspense>
      </body>
    </html>
  );
}

// Сервер
async function handler(request, response) {
  const stream = await renderToReadableStream(<App />);
  
  response.setHeader('Content-Type', 'text/html');
  stream.pipeTo(response);
}
```

71. Что такое Web Components? Приведите практический пример использования.

**Ответ:**

```javascript
// Web Components - стандарт для создания переиспользуемых элементов

// 1. Custom Elements
class TodoItem extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
  }
  
  static get observedAttributes() {
    return ['text', 'completed'];
  }
  
  connectedCallback() {
    this.render();
    this.attachEventListeners();
  }
  
  attributeChangedCallback(name, oldValue, newValue) {
    if (oldValue !== newValue) {
      this.render();
    }
  }
  
  render() {
    const text = this.getAttribute('text') || '';
    const completed = this.hasAttribute('completed');
    
    this.shadowRoot.innerHTML = `
      <style>
        :host {
          display: block;
          padding: 10px;
          border: 1px solid #ddd;
          margin: 5px 0;
        }
        
        :host([completed]) {
          opacity: 0.6;
          text-decoration: line-through;
        }
        
        .todo-content {
          display: flex;
          align-items: center;
          gap: 10px;
        }
        
        input[type="checkbox"] {
          margin: 0;
        }
        
        .delete-btn {
          margin-left: auto;
          background: #ff4444;
          color: white;
          border: none;
          padding: 5px 10px;
          cursor: pointer;
        }
      </style>
      
      <div class="todo-content">
        <input type="checkbox" ${completed ? 'checked' : ''}>
        <span>${text}</span>
        <button class="delete-btn">Delete</button>
      </div>
    `;
  }
  
  attachEventListeners() {
    const checkbox = this.shadowRoot.querySelector('input[type="checkbox"]');
    const deleteBtn = this.shadowRoot.querySelector('.delete-btn');
    
    checkbox?.addEventListener('change', (e) => {
      if (e.target.checked) {
        this.setAttribute('completed', '');
      } else {
        this.removeAttribute('completed');
      }
      
      this.dispatchEvent(new CustomEvent('toggle', {
        detail: { completed: e.target.checked },
        bubbles: true
      }));
    });
    
    deleteBtn?.addEventListener('click', () => {
      this.dispatchEvent(new CustomEvent('delete', {
        bubbles: true
      }));
    });
  }
}

// Регистрация элемента
customElements.define('todo-item', TodoItem);

// 2. Более сложный пример - Modal Component
class ModalDialog extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this.isOpen = false;
  }
  
  connectedCallback() {
    this.render();
    this.attachEventListeners();
  }
  
  render() {
    this.shadowRoot.innerHTML = `
      <style>
        :host {
          position: fixed;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          z-index: 1000;
          display: none;
        }
        
        :host([open]) {
          display: flex;
          align-items: center;
          justify-content: center;
        }
        
        .backdrop {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          background: rgba(0, 0, 0, 0.5);
        }
        
        .modal {
          background: white;
          border-radius: 8px;
          padding: 20px;
          max-width: 500px;
          max-height: 80vh;
          overflow: auto;
          position: relative;
          box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
        }
        
        .header {
          display: flex;
          justify-content: space-between;
          align-items: center;
          margin-bottom: 15px;
        }
        
        .close-btn {
          background: none;
          border: none;
          font-size: 24px;
          cursor: pointer;
          padding: 0;
          width: 30px;
          height: 30px;
          display: flex;
          align-items: center;
          justify-content: center;
        }
      </style>
      
      <div class="backdrop"></div>
      <div class="modal">
        <div class="header">
          <h2><slot name="title">Modal Title</slot></h2>
          <button class="close-btn">&times;</button>
        </div>
        <div class="content">
          <slot name="content"></slot>
        </div>
        <div class="footer">
          <slot name="footer"></slot>
        </div>
      </div>
    `;
  }
  
  attachEventListeners() {
    const backdrop = this.shadowRoot.querySelector('.backdrop');
    const closeBtn = this.shadowRoot.querySelector('.close-btn');
    
    backdrop.addEventListener('click', () => this.close());
    closeBtn.addEventListener('click', () => this.close());
    
    document.addEventListener('keydown', (e) => {
      if (e.key === 'Escape' && this.isOpen) {
        this.close();
      }
    });
  }
  
  open() {
    this.isOpen = true;
    this.setAttribute('open', '');
    document.body.style.overflow = 'hidden';
    
    this.dispatchEvent(new CustomEvent('modal-open', {
      bubbles: true
    }));
  }
  
  close() {
    this.isOpen = false;
    this.removeAttribute('open');
    document.body.style.overflow = '';
    
    this.dispatchEvent(new CustomEvent('modal-close', {
      bubbles: true
    }));
  }
}

customElements.define('modal-dialog', ModalDialog);

// 3. Использование Web Components
// HTML
/*
<todo-item text="Buy groceries" completed></todo-item>
<todo-item text="Walk the dog"></todo-item>

<modal-dialog id="myModal">
  <span slot="title">Confirm Action</span>
  <div slot="content">
    <p>Are you sure you want to delete this item?</p>
  </div>
  <div slot="footer">
    <button onclick="document.getElementById('myModal').close()">Cancel</button>
    <button onclick="confirmDelete()">Delete</button>
  </div>
</modal-dialog>
*/

// JavaScript
document.addEventListener('DOMContentLoaded', () => {
  // Обработка событий todo items
  document.addEventListener('toggle', (e) => {
    console.log('Todo toggled:', e.detail);
  });
  
  document.addEventListener('delete', (e) => {
    const modal = document.getElementById('myModal');
    modal.open();
  });
});

// 4. Интеграция с фреймворками
// React
function App() {
  const handleTodoToggle = (e) => {
    console.log('Todo toggled in React:', e.detail);
  };
  
  return (
    <div>
      <todo-item 
        text="React integration" 
        onToggle={handleTodoToggle}
      />
    </div>
  );
}

// Vue
export default {
  methods: {
    handleTodoToggle(event) {
      console.log('Todo toggled in Vue:', event.detail);
    }
  },
  template: `
    <div>
      <todo-item 
        text="Vue integration" 
        @toggle="handleTodoToggle"
      />
    </div>
  `
};

// 5. Передача сложных данных
class DataTable extends HTMLElement {
  set data(value) {
    this._data = value;
    this.render();
  }
  
  get data() {
    return this._data;
  }
  
  render() {
    if (!this._data) return;
    
    this.innerHTML = `
      <table>
        <thead>
          <tr>
            ${Object.keys(this._data[0] || {}).map(key => 
              `<th>${key}</th>`
            ).join('')}
          </tr>
        </thead>
        <tbody>
          ${this._data.map(row => 
            `<tr>
              ${Object.values(row).map(value => 
                `<td>${value}</td>`
              ).join('')}
            </tr>`
          ).join('')}
        </tbody>
      </table>
    `;
  }
}

customElements.define('data-table', DataTable);

// Использование
const table = document.querySelector('data-table');
table.data = [
  { name: 'John', age: 30, city: 'New York' },
  { name: 'Jane', age: 25, city: 'San Francisco' }
];
```

72. Объясните принципы SOLID применительно к JavaScript.

**Ответ:**

```javascript
// SOLID ПРИНЦИПЫ В JAVASCRIPT

// 1. SINGLE RESPONSIBILITY PRINCIPLE (SRP)
// Каждый класс должен иметь только одну причину для изменения

// ❌ Плохо - множество ответственностей
class UserManager {
  constructor(user) {
    this.user = user;
  }
  
  saveUser() {
    // Сохранение в базу данных
    fetch('/api/users', {
      method: 'POST',
      body: JSON.stringify(this.user)
    });
  }
  
  validateUser() {
    // Валидация пользователя
    return this.user.email && this.user.name;
  }
  
  sendEmail() {
    // Отправка email
    emailService.send(this.user.email, 'Welcome!');
  }
  
  formatUserData() {
    // Форматирование данных
    return `${this.user.name} (${this.user.email})`;
  }
}

// ✅ Хорошо - разделение ответственностей
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
}

class UserValidator {
  validate(user) {
    return user.email && user.name && user.email.includes('@');
  }
}

class UserRepository {
  async save(user) {
    const response = await fetch('/api/users', {
      method: 'POST',
      body: JSON.stringify(user)
    });
    return response.json();
  }
}

class EmailService {
  send(email, subject, message) {
    // Логика отправки email
  }
}

class UserFormatter {
  format(user) {
    return `${user.name} (${user.email})`;
  }
}

// 2. OPEN/CLOSED PRINCIPLE (OCP)
// Открыт для расширения, закрыт для модификации

// ❌ Плохо - изменение базового класса для новых типов
class DiscountCalculator {
  calculate(customer, amount) {
    if (customer.type === 'regular') {
      return amount;
    } else if (customer.type === 'premium') {
      return amount * 0.9;
    } else if (customer.type === 'vip') {
      return amount * 0.8;
    }
    // Нужно изменять этот класс для новых типов
  }
}

// ✅ Хорошо - расширение через стратегии
class DiscountStrategy {
  calculate(amount) {
    throw new Error('Must implement calculate method');
  }
}

class RegularDiscount extends DiscountStrategy {
  calculate(amount) {
    return amount;
  }
}

class PremiumDiscount extends DiscountStrategy {
  calculate(amount) {
    return amount * 0.9;
  }
}

class VipDiscount extends DiscountStrategy {
  calculate(amount) {
    return amount * 0.8;
  }
}

class ImprovedDiscountCalculator {
  constructor() {
    this.strategies = new Map();
  }
  
  addStrategy(type, strategy) {
    this.strategies.set(type, strategy);
  }
  
  calculate(customer, amount) {
    const strategy = this.strategies.get(customer.type);
    return strategy ? strategy.calculate(amount) : amount;
  }
}

// 3. LISKOV SUBSTITUTION PRINCIPLE (LSP)
// Подклассы должны заменять базовые классы без нарушения функциональности

// ❌ Плохо - нарушение LSP
class Bird {
  fly() {
    return 'Flying...';
  }
}

class Penguin extends Bird {
  fly() {
    throw new Error('Penguins cannot fly!'); // Нарушает ожидания
  }
}

// ✅ Хорошо - правильная иерархия
class Bird {
  move() {
    return 'Moving...';
  }
}

class FlyingBird extends Bird {
  fly() {
    return 'Flying...';
  }
  
  move() {
    return this.fly();
  }
}

class SwimmingBird extends Bird {
  swim() {
    return 'Swimming...';
  }
  
  move() {
    return this.swim();
  }
}

class Eagle extends FlyingBird {}
class ImprovedPenguin extends SwimmingBird {}

// 4. INTERFACE SEGREGATION PRINCIPLE (ISP)
// Клиенты не должны зависеть от интерфейсов, которые они не используют

// ❌ Плохо - большой интерфейс
class MultiFunctionDevice {
  print() { /* ... */ }
  scan() { /* ... */ }
  fax() { /* ... */ }
  email() { /* ... */ }
}

class SimplePrinter extends MultiFunctionDevice {
  print() {
    return 'Printing...';
  }
  
  scan() {
    throw new Error('This printer cannot scan');
  }
  
  fax() {
    throw new Error('This printer cannot fax');
  }
  
  email() {
    throw new Error('This printer cannot email');
  }
}

// ✅ Хорошо - разделенные интерфейсы (миксины в JS)
const Printable = {
  print() {
    return 'Printing...';
  }
};

const Scannable = {
  scan() {
    return 'Scanning...';
  }
};

const Faxable = {
  fax() {
    return 'Faxing...';
  }
};

class BetterSimplePrinter {
  constructor() {
    Object.assign(this, Printable);
  }
}

class AllInOnePrinter {
  constructor() {
    Object.assign(this, Printable, Scannable, Faxable);
  }
}

// 5. DEPENDENCY INVERSION PRINCIPLE (DIP)
// Зависеть от абстракций, а не от конкретных реализаций

// ❌ Плохо - зависимость от конкретных классов
class MySQLDatabase {
  save(data) {
    console.log('Saving to MySQL:', data);
  }
}

class OrderService {
  constructor() {
    this.database = new MySQLDatabase(); // Жестко привязан к MySQL
  }
  
  createOrder(order) {
    // Бизнес-логика
    this.database.save(order);
  }
}

// ✅ Хорошо - зависимость от абстракции
class DatabaseInterface {
  save(data) {
    throw new Error('Must implement save method');
  }
}

class BetterMySQLDatabase extends DatabaseInterface {
  save(data) {
    console.log('Saving to MySQL:', data);
  }
}

class PostgreSQLDatabase extends DatabaseInterface {
  save(data) {
    console.log('Saving to PostgreSQL:', data);
  }
}

class ImprovedOrderService {
  constructor(database) {
    this.database = database; // Инъекция зависимости
  }
  
  createOrder(order) {
    // Бизнес-логика
    this.database.save(order);
  }
}

// Использование с Dependency Injection
const mysqlDB = new BetterMySQLDatabase();
const postgresDB = new PostgreSQLDatabase();

const orderService1 = new ImprovedOrderService(mysqlDB);
const orderService2 = new ImprovedOrderService(postgresDB);

// DI Container пример
class DIContainer {
  constructor() {
    this.services = new Map();
  }
  
  register(name, factory) {
    this.services.set(name, factory);
  }
  
  get(name) {
    const factory = this.services.get(name);
    return factory ? factory() : null;
  }
}

// Настройка контейнера
const container = new DIContainer();

container.register('database', () => new BetterMySQLDatabase());
container.register('emailService', () => new EmailService());
container.register('orderService', () => {
  return new ImprovedOrderService(
    container.get('database')
  );
});

// Использование
const orderService = container.get('orderService');
```

## Проверочные вопросы - Дополнительные темы

1. **Что такое Tree Shaking и когда он работает?**
   - Удаление неиспользуемого кода из бандла; работает с ES6 модулями и при включенной оптимизации

2. **В чем разница между императивным и декларативным стилем?**
   - Императивный описывает КАК делать; декларативный описывает ЧТО нужно получить

3. **Назовите три принципа функционального программирования.**
   - Чистые функции, неизменяемость данных, функции высшего порядка

4. **Какие подходы к реализации микрофронтендов вы знаете?**
   - Module Federation, Web Components, Single-SPA, Build-time integration

5. **Назовите основные метрики Web Vitals.**
   - LCP (Largest Contentful Paint), FID (First Input Delay), CLS (Cumulative Layout Shift)

6. **В чем разница между SSR и SSG?**
   - SSR генерирует HTML на каждый запрос; SSG генерирует HTML во время сборки