

## Фронтенд и Верстка

**Все вопросы по фронтенду и верстке (42-64) вынесены в отдельные файлы в папке `questions/frontend/`:**

- **questions/frontend/1.md** - Вопросы 42-49: События DOM, оптимизация, хранение данных
- **questions/frontend/2.md** - Вопросы 50-58: Производительность, Web Workers, CSS основы  
- **questions/frontend/3.md** - Вопросы 59-64: Продвинутые темы CSS и оптимизация
- **questions/frontend/test.md** - Проверочные вопросы по Frontend и Верстке

## Дополнительные вопросы для Middle уровня

65. Что такое Tree Shaking и как оно работает?

🎯 **Простое объяснение:**
Tree Shaking - это как уборка в саду: вы срезаете мертвые и ненужные ветки (неиспользуемый код), оставляя только живые и нужные части дерева (используемый код). Итоговый бандл получается легче и быстрее.

🔍 **Детальный пример:**
```javascript
// math.js - библиотека функций
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) { // Не используется - будет удалена
  return a - b;
}

export function multiply(a, b) { // Не используется - будет удалена  
  return a * b;
}

// main.js - используем только add
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

🧠 **Мнемоника:**
"Tree Shaking - трясем дерево кода, лишние ветки отваливаются!"

⚡ **Практические применения:**
- Уменьшение размера bundle в современных приложениях
- Оптимизация импорта из больших библиотек (lodash, material-ui)
- Удаление неиспользуемых утилит и компонентов
- Автоматическая оптимизация в production сборках

✅ **Заключение:**
Tree Shaking автоматически удаляет неиспользуемый код из финального бандла, работая только с ES6 модулями. Это значительно уменьшает размер приложения и улучшает производительность загрузки.

66. Объясните разницу между imperative и declarative программирования. Приведите примеры.

🎯 **Простое объяснение:**
Представьте поход в ресторан. Императивный подход - это как если бы вы шли на кухню и говорили повару: "Сначала нарежь лук, потом обжарь мясо, добавь соль...". Декларативный подход - это просто сказать: "Хочу стейк средней прожарки". Вы говорите ЧТО хотите, а не КАК это сделать.

🔍 **Детальный пример:**
```javascript
// ИМПЕРАТИВНО - КАК делать (как повар на кухне)
const numbers = [];
for (let i = 0; i < 20; i++) {
  if (i < 5) {
    numbers.push(i);
  }
}
console.log(numbers); // [0, 1, 2, 3, 4]
// Здесь вы сами контролируете каждую итерацию и состояние массива

// ДЕКЛАРАТИВНО - ЧТО получить (как заказ в ресторане)
const numbersDeclarative = Array.from({length: 20}, (_, i) => i).filter(x => x < 5);
console.log(numbersDeclarative); // [0, 1, 2, 3, 4]
// Вы просто описываете, что хотите — все числа меньше 5

// Другие примеры:

// Императивный подход - DOM (шаг за шагом)
function imperativeToggleVisibility(elements) {
  for (let i = 0; i < elements.length; i++) {
    if (elements[i].style.display === 'none') {
      elements[i].style.display = 'block';
    } else {
      elements[i].style.display = 'none';
    }
  }
}

// Декларативный подход - React (описание результата)
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

🧠 **Мнемоника:**
"Императивный - как повар на кухне (КАК делать), декларативный - как заказ в ресторане (ЧТО хочу)!"

⚡ **Практические применения:**
- Императивный: точный контроль алгоритмов, производительность
- Декларативный: React, SQL, HTML, CSS
- Императивный: манипуляции DOM, анимации
- Декларативный: описание UI состояний

✅ **Заключение:**
Императивный стиль описывает КАК делать (пошаговые инструкции), декларативный - ЧТО получить (описание результата). Декларативный код обычно более читабелен и поддерживаем, императивный дает больше контроля над процессом.

67. Что такое Functional Programming? Назовите основные принципы.

🎯 **Простое объяснение:**
Функциональное программирование - это как готовка по рецепту: у вас есть ингредиенты (данные), которые вы не изменяете, а создаете новые блюда (результаты) с помощью чистых функций (рецептов). Каждый рецепт дает один и тот же результат для одних и тех же ингредиентов.

🔍 **Детальный пример:**
```javascript
// ПРИНЦИПЫ ФУНКЦИОНАЛЬНОГО ПРОГРАММИРОВАНИЯ:

// 1. Чистые функции (Pure Functions) - как надежный рецепт
// Плохо - не чистая функция
let counter = 0;
function impureIncrement() {
  return ++counter; // Изменяет внешнее состояние
}

// Хорошо - чистая функция
function pureIncrement(value) {
  return value + 1; // Не изменяет внешнее состояние
}

// 2. Неизменяемость (Immutability) - как работа с копиями
// Плохо - мутация
function addItemMutable(array, item) {
  array.push(item); // Изменяет исходный массив
  return array;
}

// Хорошо - иммутабельность
function addItemImmutable(array, item) {
  return [...array, item]; // Возвращает новый массив
}

// 3. Функции высшего порядка - как рецепты для создания рецептов
function createMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

// 4. Композиция функций - как комбинирование рецептов
const pipe = (...fns) => (value) => fns.reduce((acc, fn) => fn(acc), value);

const addOne = x => x + 1;
const multiplyByTwo = x => x * 2;
const square = x => x * x;

const complexOperation = pipe(addOne, multiplyByTwo, square);
console.log(complexOperation(3)); // ((3 + 1) * 2)² = 64

// 5. Рекурсия вместо циклов - как матрешка
function factorialRecursive(n) {
  return n <= 1 ? 1 : n * factorialRecursive(n - 1);
}

// 6. Каррирование - как пошаговое приготовление
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
```

🧠 **Мнемоника:**
"Функциональное программирование - как готовка: чистые рецепты, неизменные ингредиенты, композиция блюд!"

⚡ **Практические применения:**
- Обработка данных без побочных эффектов
- Создание предсказуемых и тестируемых функций
- Параллельные вычисления
- Библиотеки как Lodash, Ramda, RxJS

✅ **Заключение:**
Функциональное программирование строится на чистых функциях, неизменяемости данных и композиции. Это делает код более предсказуемым, легко тестируемым и менее подверженным ошибкам.

68. Что такое микрофронтенды? Какие есть подходы к их реализации?

🎯 **Простое объяснение:**
Микрофронтенды - это как строительство торгового центра: вместо одного огромного магазина, вы создаете множество маленьких независимых магазинов. Каждый магазин (микрофронтенд) работает самостоятельно, но все вместе создают единый торговый центр (приложение).

🔍 **Детальный пример:**
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

🎯 **Простое объяснение:**
Представьте ресторан: SSR - это как готовить блюда по заказу каждого клиента (каждый запрос генерирует HTML заново), а SSG - это как подготовить блюда заранее и просто разогреть их (HTML генерируется при сборке и отдается готовым).

🔍 **Детальный пример:**
```javascript
// SSR - Server-Side Rendering (готовим по заказу)
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

// SSG - Static Site Generation (готовим заранее)
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

🧠 **Мнемоника:**
"SSR - готовим по заказу (Server Side), SSG - готовим заранее (Static Generation)!"

⚡ **Практические применения:**
- SSR: динамический контент, персонализация, авторизация
- SSG: блоги, лендинги, документация, каталоги
- ISR: компромисс между SSR и SSG
- Гибридный подход: разные страницы разными способами

✅ **Заключение:**
SSR генерирует HTML на каждый запрос (медленнее, но актуальные данные), SSG генерирует HTML при сборке (быстрее, но данные могут устареть). ISR объединяет преимущества обоих подходов.

71. Что такое Web Components? Приведите практический пример использования.

🎯 **Простое объяснение:**
Web Components - это как создание собственных HTML-тегов, которые работают во всех браузерах и фреймворках. Представьте, что вы создаете новый тип LEGO-блока, который можно использовать в любом наборе LEGO.

🔍 **Детальный пример:**
```javascript
// Web Components - стандарт для создания переиспользуемых элементов

// 1. Custom Elements - создаем свой HTML-тег
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

🧠 **Мнемоника:**
"Web Components - как создать свой LEGO-блок: Custom Elements, Shadow DOM, Templates!"

⚡ **Практические применения:**
- Создание переиспользуемых UI компонентов
- Интеграция с любыми фреймворками (React, Vue, Angular)
- Изоляция стилей и логики через Shadow DOM
- Создание дизайн-систем для больших проектов

✅ **Заключение:**
Web Components позволяют создавать кастомные HTML-элементы, которые работают нативно в браузере. Они обеспечивают инкапсуляцию, переиспользование и совместимость с любыми фреймворками.

72. Объясните принципы SOLID применительно к JavaScript.

🎯 **Простое объяснение:**
SOLID принципы - это как правила хорошего тона для программистов. Представьте организацию работы в офисе: каждый сотрудник (класс) должен иметь одну специальность (S), офис должен быть открыт для новых сотрудников, но закрыт для изменения структуры (O), любой сотрудник должен уметь заменить коллегу своего уровня (L), не заставляйте людей делать то, что им не нужно (I), и начальство не должно зависеть от подчиненных (D).

🔍 **Детальный пример:**
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

🧠 **Мнемоника:**
"SOLID - как хорошая организация офиса: S - одна специальность, O - открыт для новых, L - заменяемость, I - не заставляй лишнего, D - начальство не зависит от подчиненных!"

⚡ **Практические применения:**
- Создание поддерживаемого и расширяемого кода
- Упрощение тестирования и отладки
- Разделение ответственности в больших проектах
- Dependency Injection для гибкости архитектуры

✅ **Заключение:**
SOLID принципы помогают создавать качественный, поддерживаемый код. Они применимы не только к классам, но и к функциям и модулям в JavaScript. Следование этим принципам делает код более гибким, тестируемым и менее подверженным ошибкам.

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