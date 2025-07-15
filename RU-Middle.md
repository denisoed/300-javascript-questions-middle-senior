# Middle

## Общие вопросы

1. Расскажите о пирамиде тестирования.

**Ответ:**

**Пирамида тестирования** — это правило: больше простых тестов внизу, меньше сложных наверху.

```javascript
// 🔍 Три уровня пирамиды:

// 📦 UNIT-ТЕСТЫ (основание) - много, быстро, дешево
function add(a, b) {
  return a + b;
}

test('adds 1 + 2 to equal 3', () => {
  expect(add(1, 2)).toBe(3);
});

// 🔗 ИНТЕГРАЦИОННЫЕ ТЕСТЫ (середина) - меньше, медленнее
test('user service integration', async () => {
  const user = await userService.createUser({ name: 'John' });
  const foundUser = await userService.findById(user.id);
  expect(foundUser.name).toBe('John');
});

// 🌐 E2E ТЕСТЫ (вершина) - мало, медленно, дорого
test('user can register and login', async () => {
  await page.goto('/register');
  await page.fill('[name="email"]', 'user@test.com');
  await page.click('button[type="submit"]');
  await expect(page).toHaveURL('/dashboard');
});
```

**🧠 Как запомнить?**
- **Unit** = одна **единица** кода (функция, класс)
- **Integration** = несколько частей **интегрируются** 
- **E2E** = весь путь **от начала до конца**

**⚖️ Правило пирамиды:**
- 70% unit-тестов (быстрые и надежные)
- 20% интеграционных (проверяют связи)
- 10% e2e тестов (проверяют весь сценарий)

**Вывод:**
Больше простых unit-тестов, меньше сложных e2e тестов — так тесты работают быстро и стабильно.

2. Какие типы автоматизированных тестов вам случалось писать? Какие библиотеки при этом использовали? Какие инструменты предпочитаете и почему?

**Ответ:**

**Автоматизированные тесты** — это код, который проверяет другой код автоматически.

```javascript
// 🔍 Основные типы тестов:

// 📦 UNIT-ТЕСТЫ (Jest, Vitest)
test('function works correctly', () => {
  expect(add(2, 3)).toBe(5);
});

// 🔗 ИНТЕГРАЦИОННЫЕ ТЕСТЫ (Jest + Testing Library)
import { render, screen, fireEvent } from '@testing-library/react';

test('button click triggers callback', () => {
  const handleClick = jest.fn();
  render(<Button onClick={handleClick}>Click me</Button>);
  
  fireEvent.click(screen.getByText('Click me'));
  expect(handleClick).toHaveBeenCalledTimes(1);
});

// 🌐 E2E ТЕСТЫ (Cypress, Playwright)
test('user can login', () => {
  cy.visit('/login');
  cy.get('[data-testid="email"]').type('user@test.com');
  cy.get('[data-testid="password"]').type('password');
  cy.get('button[type="submit"]').click();
  cy.url().should('include', '/dashboard');
});

// 👁️ VISUAL ТЕСТЫ (Chromatic, Percy)
test('button looks correct', () => {
  expect(await page.screenshot()).toMatchSnapshot();
});

// ⚡ PERFORMANCE ТЕСТЫ (Lighthouse CI)
test('page loads fast', () => {
  expect(performanceScore).toBeGreaterThan(90);
});
```

**🛠️ Популярные инструменты:**
- **Jest** — универсальный фреймворк для тестирования
- **Cypress** — простой E2E тестинг
- **Playwright** — мощный E2E тестинг
- **Testing Library** — тестирование React компонентов

**📊 Какой инструмент выбрать?**
- Начинающим → **Jest + Testing Library**
- Для E2E → **Cypress** (проще) или **Playwright** (мощнее)
- Для производительности → **Lighthouse CI**

**Вывод:**
Начните с Jest для unit-тестов, добавьте Cypress для E2E — этого достаточно для большинства проектов.

3. Что такое unit-тесты? Какое место в пирамиде тестирования занимают unit-тесты?

**Ответ:**

**Unit-тесты** — это проверка одной маленькой части кода (функции, класса). Занимают **основание пирамиды** — их больше всего.

```javascript
// 🔍 Пример хорошего unit-теста

function calculateTax(amount, rate) {
  if (amount < 0) throw new Error('Amount must be positive');
  return amount * rate;
}

// ✅ Тестируем только эту функцию
describe('calculateTax', () => {
  it('считает налог правильно', () => {
    expect(calculateTax(100, 0.2)).toBe(20);
  });

  it('возвращает 0 для нулевой суммы', () => {
    expect(calculateTax(0, 0.2)).toBe(0);
  });

  it('выбрасывает ошибку для отрицательной суммы', () => {
    expect(() => calculateTax(-100, 0.2)).toThrow('Amount must be positive');
  });
});
```

**🧠 Что делает unit-тест хорошим?**
- **Быстро** — выполняется за миллисекунды
- **Изолированно** — тестирует только одну функцию
- **Предсказуемо** — всегда одинаковый результат
- **Не зависит** от баз данных, сети, файлов

**📍 Место в пирамиде:**
- **70% всех тестов** — основание пирамиды
- **Самые дешевые** — быстро пишутся и выполняются
- **Первая линия защиты** — сразу показывают поломки

**🎯 Что тестировать unit-тестами?**
- Функции с бизнес-логикой
- Утилиты и хелперы
- Валидаторы
- Вычисления

**Вывод:**
Unit-тесты — это фундамент. Много быстрых простых тестов лучше, чем несколько сложных.

4. Что такое code coverage? Обязательно ли 100% покрытие кода тестами?

**Ответ:**

**Code coverage** — это процент кода, который выполнился во время тестов. Показывает, какие части кода протестированы.

```javascript
// 🔍 Пример функции и её покрытия

function processUser(user) {
  if (!user) return null;           // Строка 1
  
  if (user.age >= 18) {             // Строка 2
    return { ...user, adult: true }; // Строка 3 (не покрыта тестом!)
  }
  
  return { ...user, adult: false };  // Строка 4
}

// Тест покрывает только часть функции
test('returns null for empty user', () => {
  expect(processUser(null)).toBe(null); // Покрывает строку 1
});

test('handles minor user', () => {
  const user = { name: 'John', age: 16 };
  expect(processUser(user)).toEqual({ name: 'John', age: 16, adult: false });
  // Покрывает строки 2, 4. Строка 3 НЕ покрыта!
});

// 📊 Настройка coverage в Jest
module.exports = {
  collectCoverage: true,
  coverageThreshold: {
    global: {
      branches: 80,    // Покрытие веток if/else
      functions: 80,   // Покрытие функций
      lines: 80,       // Покрытие строк
      statements: 80   // Покрытие инструкций
    }
  }
};
```

**🚫 Почему 100% покрытие НЕ нужно?**
- **Качество > количество** — лучше 80% хороших тестов
- **Сложно тестировать** error handling и edge cases  
- **Пустая трата времени** — тестирование тривиального кода
- **Ложная безопасность** — покрытие ≠ качество тестов

**🎯 Оптимальное покрытие:**
- **80-90%** — золотая середина
- **Больше для критичного кода** (платежи, безопасность)
- **Меньше для простого кода** (геттеры, сеттеры)

**🧠 Как запомнить?**
Coverage показывает **сколько** кода протестировано, но не **насколько хорошо**.

**Вывод:**
Стремитесь к 80-90% покрытия, но фокусируйтесь на качестве тестов, а не на проценте.

5. Как запретить браузеру отдавать кэш на HTTP-запрос?

**Ответ:**

**Кэш** — браузер сохраняет ответы сервера и отдает их повторно. Иногда нужны **свежие данные**.

```javascript
// 🔍 Способы отключить кэш:

// 🛠️ СПОСОБ 1: HTTP заголовки на сервере
// Express.js пример
app.get('/api/data', (req, res) => {
  res.setHeader('Cache-Control', 'no-cache, no-store, must-revalidate');
  res.setHeader('Pragma', 'no-cache');       // Для старых браузеров
  res.setHeader('Expires', '0');             // Дата истечения в прошлом
  res.json({ data: 'always fresh' });
});

// 🛠️ СПОСОБ 2: Параметр в URL (cache busting)
const timestamp = Date.now();
const url = `/api/data?_=${timestamp}`; // Каждый раз новый URL

fetch(url); // Браузер считает это новым запросом

// 🛠️ СПОСОБ 3: Заголовки в fetch
fetch('/api/data', {
  headers: {
    'Cache-Control': 'no-cache'
  }
});

// 🛠️ СПОСОБ 4: Режим no-cache в fetch
fetch('/api/data', {
  cache: 'no-cache'  // Простой способ!
});

// 🛠️ СПОСОБ 5: С Axios
axios.get('/api/data', {
  headers: {
    'Cache-Control': 'no-cache'
  }
});
```

**🧠 Какой способ использовать?**
- **Сервер** → устанавливайте заголовки (надежнее всего)
- **Клиент** → используйте `cache: 'no-cache'` в fetch
- **Экстренный случай** → добавьте timestamp к URL

**⚠️ Когда отключать кэш?**
- **API с данными реального времени** (курсы валют, уведомления)
- **Пользовательские настройки** 
- **Критичные обновления**

**🚀 Когда НЕ отключать?**
- Статичные файлы (CSS, JS, изображения)
- Редко меняющиеся данные

**Вывод:**
Для свежих данных используйте `cache: 'no-cache'` в fetch или настройте заголовки на сервере.

6. Что такое XSS (Cross-Site Scripting)?

**Ответ:**

**XSS** — хакер внедряет свой JavaScript код в ваш сайт. Пользователи запускают вредоносный код, думая что это ваш сайт.

```javascript
// 🔍 Как происходит атака:

// ❌ ОПАСНО - Уязвимый код
const userInput = '<script>alert("Хакер украл ваши данные!")</script>';
document.getElementById('welcome').innerHTML = 'Привет, ' + userInput;
// Script выполнится! 💥

// ✅ БЕЗОПАСНО - Защищенный код  
document.getElementById('welcome').textContent = 'Привет, ' + userInput;
// Script отобразится как текст

// 🛡️ Защита через санитизацию
function sanitizeInput(str) {
  const escapeMap = {
    '<': '&lt;',      // < становится &lt;
    '>': '&gt;',      // > становится &gt;
    '&': '&amp;',     // & становится &amp;
    '"': '&quot;',    // " становится &quot;
    "'": '&#x27;'     // ' становится &#x27;
  };
  
  return str.replace(/[<>&"']/g, match => escapeMap[match]);
}

// Безопасное использование
const safeInput = sanitizeInput(userInput);
document.getElementById('welcome').innerHTML = 'Привет, ' + safeInput;

// 🔍 Три типа XSS:

// 1️⃣ REFLECTED XSS (отраженный) - через URL
// https://site.com/search?q=<script>стелать данные</script>

// 2️⃣ STORED XSS (хранимый) - в базе данных
// Комментарий: "<script>украсть cookies</script>"

// 3️⃣ DOM-BASED XSS - через JavaScript
location.hash = '<script>вредоносный код</script>';
```

**🧠 Как запомнить?**
**XSS** = **Cross-Site Scripting** = чужой скрипт работает на вашем сайте

**🛡️ Как защититься?**
- **textContent** вместо innerHTML
- **Санитизация** всех пользовательских данных
- **CSP заголовки** (Content Security Policy)
- **Валидация на сервере**

**⚠️ Что может украсть хакер?**
- Cookies и токены
- Личные данные пользователя
- Может отправить запросы от имени пользователя

**🔒 Простые правила безопасности:**
1. Никогда не доверяйте пользовательскому вводу
2. Всегда экранируйте HTML символы
3. Используйте textContent вместо innerHTML

**Вывод:**
XSS — это когда чужой код выполняется на вашем сайте. Всегда очищайте пользовательский ввод!

7. Расскажите о паттернах Observer, Pub/Sub. Чем они отличаются? Приведите примеры реализации этих паттернов в известных фреймворках (библиотеках, браузерных API).

**Ответ:**

**Observer** — объект следит за другим объектом **напрямую**.  
**Pub/Sub** — объекты общаются через **посредника**, не знают друг о друге.

```javascript
// 🔍 OBSERVER PATTERN - прямая связь

class NewsAgency {
  constructor() {
    this.observers = [];  // Список наблюдателей
    this.news = '';
  }
  
  // Подписка на обновления
  subscribe(observer) {
    this.observers.push(observer);
  }
  
  // Уведомление всех наблюдателей
  setNews(news) {
    this.news = news;
    this.observers.forEach(observer => observer.update(news));
  }
}

class NewsChannel {
  constructor(name) {
    this.name = name;
  }
  
  update(news) {
    console.log(`${this.name} получил новость: ${news}`);
  }
}

// Использование Observer
const agency = new NewsAgency();
const channel1 = new NewsChannel('РБК');
const channel2 = new NewsChannel('Ведомости');

agency.subscribe(channel1);  // Прямая подписка
agency.subscribe(channel2);  // Прямая подписка
agency.setNews('Важные новости!');

// 🔍 PUB/SUB PATTERN - через посредника

class EventBus {
  constructor() {
    this.events = {};  // Хранилище событий
  }
  
  // Подписка на событие
  subscribe(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }
  
  // Публикация события
  publish(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data));
    }
  }
}

// Использование Pub/Sub
const eventBus = new EventBus();

// Подписчики не знают о издателе
eventBus.subscribe('user-login', (user) => {
  console.log(`Аналитика: пользователь ${user.name} вошел`);
});

eventBus.subscribe('user-login', (user) => {
  console.log(`Email: отправка приветствия ${user.name}`);
});

// Издатель не знает о подписчиках
eventBus.publish('user-login', { name: 'Иван' });
```

**🤔 Главная разница:**
- **Observer**: `subject.subscribe(observer)` — знают друг о друге
- **Pub/Sub**: `eventBus.subscribe('event', callback)` — не знают друг о друге

**🌐 Примеры в реальных проектах:**

```javascript
// DOM Events (Pub/Sub)
button.addEventListener('click', handleClick);  // Через браузер-посредник

// Vue.js реактивность (Observer)
data() {
  return { message: 'Hello' };  // Vue следит за изменениями
}

// Redux (Observer)
store.subscribe(() => {
  console.log('State changed:', store.getState());
});

// Node.js EventEmitter (Pub/Sub)
const events = require('events');
const emitter = new events.EventEmitter();

emitter.on('data', (data) => console.log(data));
emitter.emit('data', 'Hello World');
```

**🧠 Как запомнить?**
- **Observer** = прямой **обзор** (subject → observer)
- **Pub/Sub** = почтовый **автобус** (publisher → eventBus → subscriber)

**📊 Когда что использовать?**
- **Observer** → когда объекты связаны по логике
- **Pub/Sub** → когда нужна слабая связность модулей

**Вывод:**
Observer — прямая связь, Pub/Sub — через посредника. Оба помогают объектам реагировать на изменения.

8. С какой целью может быть использован event listener события fetch self.addEventListener `( 'fetch', event => {})`?

**Ответ:**

**Service Worker** — это посредник между сайтом и сетью. Может **перехватывать** все сетевые запросы и решать, что с ними делать.

```javascript
// 🔍 Service Worker - перехват всех запросов

self.addEventListener('fetch', event => {
  console.log('Перехватили запрос:', event.request.url);
  
  // 🗄️ КЭШИРОВАНИЕ - сначала кэш, потом сеть
  if (event.request.destination === 'image') {
    event.respondWith(
      caches.match(event.request)          // Ищем в кэше
        .then(cached => {
          if (cached) {
            console.log('Из кэша:', event.request.url);
            return cached;                 // Отдаем из кэша
          }
          
          return fetch(event.request)      // Загружаем из сети
            .then(response => {
              const cache = caches.open('images-v1');
              cache.then(c => c.put(event.request, response.clone()));
              return response;
            });
        })
    );
  }
  
  // 📱 ОФЛАЙН РЕЖИМ - fallback когда сеть недоступна
  if (event.request.url.includes('/api/')) {
    event.respondWith(
      fetch(event.request)
        .then(response => response)
        .catch(() => {
          // Сеть недоступна - показываем кэшированную страницу
          return caches.match('/offline.html');
        })
    );
  }
  
  // 🔧 МОДИФИКАЦИЯ ЗАПРОСОВ - добавляем авторизацию
  if (event.request.url.includes('/secure-api/')) {
    const modifiedRequest = new Request(event.request.url, {
      method: event.request.method,
      headers: {
        ...event.request.headers,
        'Authorization': 'Bearer my-secret-token'  // Автоматически добавляем токен
      }
    });
    
    event.respondWith(fetch(modifiedRequest));
  }
});

// 🚀 ПРЕДЗАГРУЗКА - загружаем ресурсы заранее
self.addEventListener('fetch', event => {
  if (event.request.url.includes('/page1')) {
    // Пользователь зашел на страницу 1, предзагружаем страницу 2
    caches.open('pages').then(cache => {
      cache.add('/page2');
    });
  }
});
```

**🎯 Основные применения:**

**📊 1. Кэширование**
```javascript
// Стратегия "Cache First" - быстрая загрузка
caches.match(request) || fetch(request)
```

**📱 2. Offline режим**
```javascript
// Показываем офлайн страницу вместо ошибки
fetch(request).catch(() => caches.match('/offline.html'))
```

**🔒 3. Безопасность**
```javascript
// Автоматически добавляем токены авторизации
```

**📈 4. Аналитика**
```javascript
// Отслеживаем все запросы пользователя
```

**🧠 Как запомнить?**
Service Worker = **сервис-официант** — перехватывает заказы (запросы) и решает откуда их взять

**⚡ Полезные паттерны:**
- **Cache First** → быстро (сначала кэш)
- **Network First** → свежие данные (сначала сеть)
- **Stale While Revalidate** → показать старое, обновить в фоне

**Вывод:**
Fetch event listener позволяет контролировать все сетевые запросы — кэшировать, работать офлайн, модифицировать запросы.

9. Что такое Event loop и как он работает? Расскажите о микрозадачах и макрозадачах.

**Ответ:**

**Event Loop** — это диспетчер, который решает какой код выполнить следующим. JavaScript выполняется **по одной команде за раз**.

```javascript
// 🔍 Простой пример приоритетов

console.log('1');                                    // Синхронный код

setTimeout(() => console.log('2'), 0);               // 📦 МАКРОЗАДАЧА

Promise.resolve().then(() => console.log('3'));     // ⚡ МИКРОЗАДАЧА

console.log('4');                                    // Синхронный код

// Результат: 1, 4, 3, 2
// Почему именно такой порядок? 👇
```

**🏃‍♂️ Как работает Event Loop:**

```javascript
// 🔍 Шаги выполнения:

// 1️⃣ CALL STACK (стек вызовов) - выполняется сейчас
function main() {
  console.log('Начало');     // ← Сейчас здесь
  setTimeout(callback, 0);   // → Уходит в Web API
  console.log('Конец');      // ← Следующий в стеке
}

// 2️⃣ WEB APIs - "фоновые" операции
// setTimeout, DOM events, fetch - работают параллельно

// 3️⃣ MICROTASK QUEUE - очередь микрозадач
Promise.resolve().then(() => console.log('Микро'));

// 4️⃣ CALLBACK QUEUE - очередь макрозадач  
setTimeout(() => console.log('Макро'), 0);

// Алгоритм Event Loop:
// 1. Выполни весь синхронный код (Call Stack)
// 2. Выполни ВСЕ микрозадачи
// 3. Выполни ОДНУ макрозадачу
// 4. Повтори с пункта 2
```

**⚡ МИКРОЗАДАЧИ (высший приоритет):**
```javascript
Promise.resolve().then(() => console.log('Promise'));
queueMicrotask(() => console.log('Microtask'));
// Выполняются ВСЕ подряд
```

**📦 МАКРОЗАДАЧИ (низший приоритет):**
```javascript
setTimeout(() => console.log('Timeout'), 0);
setInterval(() => console.log('Interval'), 100);
// Выполняется только ОДНА за раз
```

**🎯 Сложный пример:**
```javascript
console.log('Start');

setTimeout(() => console.log('Timeout 1'), 0);

Promise.resolve()
  .then(() => console.log('Promise 1'))
  .then(() => console.log('Promise 2'));

setTimeout(() => console.log('Timeout 2'), 0);

console.log('End');

// Результат:
// Start
// End
// Promise 1
// Promise 2
// Timeout 1
// Timeout 2
```

**🧠 Как запомнить приоритеты?**
1. **Синхронный код** — выполняется сразу
2. **Микрозадачи** — **микро** = быстрые, **все сразу**
3. **Макрозадачи** — **макро** = медленные, **по одной**

**📊 Практическое применение:**
```javascript
// Плохо - блокирует интерфейс
for (let i = 0; i < 1000000; i++) {
  // Тяжелая работа
}

// Хорошо - разбиваем на части
function heavyWork(i = 0) {
  if (i < 1000000) {
    // Небольшая часть работы
    doSomeWork(i);
    
    // Даем браузеру отдохнуть
    setTimeout(() => heavyWork(i + 1000), 0);
  }
}
```

**Вывод:**
Event Loop следит за порядком: сначала весь синхронный код, потом все микрозадачи, потом одна макрозадача.

## Проверочные вопросы - Общие

1. **Какой тип тестов должен составлять основание пирамиды тестирования и почему?**
   - **Unit-тесты** — быстрые, дешевые, легко поддерживаемые, составляют 70% всех тестов

2. **Назовите три ключевых характеристики хорошего unit-теста.**
   - **Быстро** (миллисекунды), **изолированно** (одна функция), **предсказуемо** (одинаковый результат)

3. **Что произойдет если выполнить: `Promise.resolve().then(() => console.log('A')); setTimeout(() => console.log('B'), 0);`?**
   - Выведется **'A', затем 'B'** (микрозадачи имеют приоритет над макрозадачами)

4. **Какие HTTP заголовки нужны для полного запрета кэширования?**
   - **Три заголовка**: `Cache-Control: no-cache, no-store`, `Pragma: no-cache`, `Expires: 0`

5. **В чем основное отличие паттернов Observer и Pub/Sub?**
   - **Observer**: прямая связь (знают друг о друге); **Pub/Sub**: через посредника (не знают друг о друге)

## JS Core

10. Какие типы данных бывают в JavaScript? Каким будет результат выполнения кода?

```javascript
let firstObj = { name: 'Hello' };
let secondObj = firstObj;
firstObj = { name: 'Bye' };
console.log(secondObj.name);
```

**Ответ:**

В JavaScript **8 типов данных**: 7 примитивных + 1 объектный.

```javascript
// 🔍 7 ПРИМИТИВНЫХ ТИПОВ (хранят значение)

let num = 42;              // number (числа, NaN, Infinity)
let str = "Hello";         // string (текст)
let bool = true;           // boolean (true/false)
let empty = undefined;     // undefined (не определено)
let nothing = null;        // null (пустота)
let unique = Symbol('id'); // symbol (уникальный идентификатор)
let huge = 123n;           // bigint (большие числа)

// 1️⃣ ОБЪЕКТНЫЙ ТИП (хранят ссылку)
let obj = { a: 1 };        // object (объекты, массивы, функции)

// 🔍 Разбор кода:

let firstObj = { name: 'Hello' };  // Создаем объект
let secondObj = firstObj;          // Копируем ССЫЛКУ (не объект!)
firstObj = { name: 'Bye' };        // firstObj теперь ссылается на НОВЫЙ объект
console.log(secondObj.name);       // "Hello" 

// ❗ secondObj все еще ссылается на СТАРЫЙ объект!
```

**🧠 Ключевое отличие:**
- **Примитивы** — копируется **значение**
- **Объекты** — копируется **ссылка**

```javascript
// 📝 Примитивы - копируется значение
let a = 5;
let b = a;        // b получает копию значения 5
a = 10;           // a изменилось
console.log(b);   // 5 (b не изменилось!)

// 🔗 Объекты - копируется ссылка
let obj1 = { value: 5 };
let obj2 = obj1;        // obj2 получает ссылку на тот же объект
obj1.value = 10;        // Изменяем объект
console.log(obj2.value); // 10 (obj2 тоже изменился!)
```

**📊 Проверка типов:**
```javascript
typeof 42;              // "number"
typeof "hello";         // "string"
typeof true;            // "boolean"
typeof undefined;       // "undefined"
typeof null;            // "object" (!) - историческая ошибка
typeof Symbol('id');    // "symbol"
typeof 123n;            // "bigint"
typeof {};              // "object"
typeof [];              // "object"
typeof function(){};    // "function" (но это подвид object)
```

**🎯 Главное правило:**
- **Примитивы неизменяемы** — создается новое значение
- **Объекты изменяемы** — изменяется тот же объект в памяти

**Вывод:**
8 типов данных: 7 примитивных (значение) + 1 объектный (ссылка). Результат кода: `"Hello"`.

11. Что такое temporal dead zone?

**Ответ:**

**Temporal Dead Zone (TDZ)** — это "мертвая зона" между объявлением переменной `let`/`const` и её инициализацией. В этой зоне переменная существует, но к ней нельзя обращаться.

```javascript
// 🔍 Что происходит с разными типами переменных:

// ❌ TDZ для let
console.log(typeof x); // ReferenceError: Cannot access 'x' before initialization
let x = 5;             // TDZ заканчивается здесь

// ❌ TDZ для const  
console.log(y);        // ReferenceError: Cannot access 'y' before initialization
const y = 10;          // TDZ заканчивается здесь

// ✅ var НЕ имеет TDZ (hoisting)
console.log(z);        // undefined (не ошибка!)
var z = 15;

// 🔍 Визуальное объяснение TDZ:

{
  // 💀 TDZ начинается здесь - переменная "существует", но недоступна
  console.log(temp);   // ReferenceError!
  console.log(typeof temp); // ReferenceError!
  
  let temp = 'hello';  // ✅ TDZ заканчивается здесь
  console.log(temp);   // "hello" - теперь можно использовать
}

// 🎯 TDZ в параметрах функции
function badExample(a = b, b = 2) {
  // TDZ: 'b' используется до своей инициализации
  return a + b; // ReferenceError!
}

function goodExample(a = 1, b = 2) {
  return a + b; // OK - нет зависимости
}

// 🔍 Сравнение поведения:

function compareDeclarations() {
  console.log('var:', varVariable);    // undefined
  console.log('let:', letVariable);    // ReferenceError!
  console.log('const:', constVariable); // ReferenceError!
  
  var varVariable = 1;
  let letVariable = 2;
  const constVariable = 3;
}
```

**🧠 Как запомнить?**
- **var** = **подъем** (hoisting) с undefined
- **let/const** = **подъем** в TDZ (недоступна до инициализации)

**⚡ Практическое применение:**
```javascript
// Плохо - можно случайно использовать до объявления
function badCode() {
  if (condition) {
    console.log(data); // undefined, но не ошибка
    var data = 'важные данные';
  }
}

// Хорошо - ошибка заставляет писать правильно
function goodCode() {
  if (condition) {
    console.log(data); // ReferenceError - сразу видно проблему
    let data = 'важные данные';
  }
}
```

**🎯 Зачем нужна TDZ?**
1. **Предотвращает ошибки** — нельзя использовать переменную до инициализации
2. **Делает код понятнее** — переменная доступна только после объявления
3. **Упрощает отладку** — сразу видно проблемы с порядком кода

**Вывод:**
TDZ — это период, когда переменная `let`/`const` уже объявлена, но еще не инициализирована и недоступна.

12. Как работает boxing/unboxing в JavaScript?

**Ответ:**

**Boxing** — JavaScript **автоматически превращает** примитив в объект, чтобы вызвать метод.  
**Unboxing** — **извлекает** примитивное значение из объекта-обёртки.

```javascript
// 🔍 АВТОМАТИЧЕСКИЙ BOXING - магия JavaScript

let str = "hello";              // Примитив
console.log(str.toUpperCase()); // "HELLO"

// Что происходит под капотом:
// 1. JS видит: примитив.метод()  
// 2. Создает: new String("hello")
// 3. Вызывает: объект.toUpperCase()
// 4. Удаляет объект
// 5. Возвращает результат

// 🔍 РУЧНОЙ BOXING (не рекомендуется)

let num = 42;                    // Примитив
let numObj = new Number(42);     // Объект-обёртка

console.log(typeof num);         // "number"
console.log(typeof numObj);      // "object"

// ⚠️ Проблемы с ручным boxing
let str1 = "hello";              // Примитив  
let str2 = new String("hello");  // Объект

console.log(str1 === str2);      // false! (разные типы)
console.log(str1 == str2);       // true (приведение типов)

if (new Boolean(false)) {
  console.log("Выполнится!");    // Объект всегда truthy!
}

// 🔍 UNBOXING - получение примитива из объекта

let boolObj = new Boolean(false);
console.log(boolObj);            // [Boolean: false] - объект
console.log(boolObj.valueOf());  // false - примитив
console.log(+boolObj);           // 0 - принудительный unboxing
```

**🎯 Практическое применение:**
```javascript
// Добавляем методы к примитивам
Number.prototype.times = function(callback) {
  for (let i = 0; i < this.valueOf(); i++) {
    callback(i);
  }
};

(3).times(i => console.log(i)); // 0, 1, 2

String.prototype.reverse = function() {
  return this.split('').reverse().join('');
};

console.log("hello".reverse()); // "olleh"
```

**🧠 Как запомнить?**
- **Boxing** = **упаковка** примитива в объект (для вызова методов)
- **Unboxing** = **распаковка** объекта обратно в примитив

**⚡ Главные правила:**
1. **Автоматический boxing** — хорошо (JS делает сам)
2. **Ручной boxing** — плохо (создает объекты вместо примитивов)
3. **Всегда используйте** примитивы, а не объекты-обёртки

**🚨 Частые ошибки:**
```javascript
// ❌ Плохо
let num = new Number(5);  // Объект
let str = new String("hi"); // Объект

// ✅ Хорошо  
let num = 5;              // Примитив
let str = "hi";           // Примитив
```

**Вывод:**
Boxing позволяет примитивам иметь методы. JavaScript делает это автоматически — не создавайте объекты-обёртки вручную!

13. В чем разница между оператором in и методом hasOwnProperty?

**Ответ:**

**Оператор `in`** — ищет свойство **везде** (в объекте + во всех прототипах).  
**hasOwnProperty** — ищет свойство **только в самом объекте**.

```javascript
// 🔍 Создаем объект с наследованием

const animal = { type: 'animal' };
const dog = { name: 'Бобик' };

// Устанавливаем наследование
Object.setPrototypeOf(dog, animal);

// 🔍 Тестируем разницу:

// ✅ ОПЕРАТОР IN - ищет везде
console.log('name' in dog);    // true (собственное свойство)
console.log('type' in dog);    // true (из прототипа animal)
console.log('toString' in dog); // true (из встроенного Object)

// ✅ hasOwnProperty - только собственные
console.log(dog.hasOwnProperty('name'));    // true (есть в dog)
console.log(dog.hasOwnProperty('type'));    // false (есть только в animal)
console.log(dog.hasOwnProperty('toString')); // false (встроенное)

// 🎯 Практический пример - перебор свойств

const user = { name: 'Иван', age: 25 };
Object.prototype.globalProp = 'глобальное'; // Добавили в прототип

// ❌ Плохо - получим лишние свойства
for (let key in user) {
  console.log(key); // name, age, globalProp (лишнее!)
}

// ✅ Хорошо - только собственные свойства
for (let key in user) {
  if (user.hasOwnProperty(key)) {
    console.log(key); // name, age (только нужные)
  }
}

// 🚀 Современный способ (ES2022)
console.log(Object.hasOwn(user, 'name')); // true - аналог hasOwnProperty

// 🔍 Визуальное сравнение:
const obj = { own: 'собственное' };
Object.prototype.inherited = 'наследованное';

console.log('own' in obj);              // true
console.log('inherited' in obj);        // true  ← ищет в прототипе!
console.log(obj.hasOwnProperty('own')); // true
console.log(obj.hasOwnProperty('inherited')); // false ← только свои!
```

**🧠 Как запомнить?**
- **`in`** = **включая** прототипы (ищет везде)
- **hasOwnProperty** = **has own** = только **свои** свойства

**📊 Когда что использовать?**

```javascript
// ✅ Используйте IN когда:
// - Проверяете наличие метода (может быть в прототипе)
if ('push' in someArray) {
  someArray.push(newItem);
}

// ✅ Используйте hasOwnProperty когда:
// - Перебираете только собственные свойства
// - Сериализуете объект
// - Клонируете объект

function cloneOwnProperties(obj) {
  const clone = {};
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      clone[key] = obj[key];
    }
  }
  return clone;
}
```

**⚡ Полезные методы:**
```javascript
const obj = { a: 1, b: 2 };

// Все ключи (только собственные)
Object.keys(obj);           // ['a', 'b']

// Все ключи + прототипы (через for...in с проверкой)
Object.getOwnPropertyNames(obj); // ['a', 'b'] - только собственные

// Проверка существования
Object.hasOwn(obj, 'a');    // true (современный способ)
obj.hasOwnProperty('a');    // true (классический способ)
```

**Вывод:**
`in` ищет везде, `hasOwnProperty` — только в самом объекте. Для перебора используйте `hasOwnProperty`!

14. Опишите, с помощью чего в JS реализуются такие ООП-парадигмы, как инкапсуляция, полиморфизм, абстракция?

**Ответ:**

JavaScript реализует ООП-принципы несколькими способами:

## 🔒 **ИНКАПСУЛЯЦИЯ** — скрытие внутренней реализации

```javascript
// 🔍 СПОСОБ 1: Замыкания (классический)
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Приватная переменная - никто не достанет!
  
  return {
    deposit(amount) {
      balance += amount;
      return balance;
    },
    
    withdraw(amount) {
      if (amount <= balance) {
        balance -= amount;
        return balance;
      }
      throw new Error('Недостаточно средств');
    },
    
    getBalance() {
      return balance; // Только через этот метод можно узнать баланс
    }
  };
}

const account = createBankAccount(1000);
console.log(account.getBalance()); // 1000
// account.balance - undefined! Нет прямого доступа

// 🔍 СПОСОБ 2: Приватные поля (ES2022)
class ModernBankAccount {
  #balance = 0;        // Приватное поле
  #pin;                // Приватный PIN
  
  constructor(initialBalance, pin) {
    this.#balance = initialBalance;
    this.#pin = pin;
  }
  
  #validatePin(inputPin) { // Приватный метод
    return this.#pin === inputPin;
  }
  
  withdraw(amount, pin) {
    if (!this.#validatePin(pin)) {
      throw new Error('Неверный PIN');
    }
    this.#balance -= amount;
  }
  
  getBalance() {
    return this.#balance;
  }
}
```

## 🎭 **ПОЛИМОРФИЗМ** — один интерфейс, разные реализации

```javascript
// 🔍 Duck Typing - "если выглядит как утка и крякает как утка..."

class Dog {
  makeSound() { return "Гав!"; }
  move() { return "Бегу на лапах"; }
}

class Bird {
  makeSound() { return "Чирик!"; }
  move() { return "Лечу на крыльях"; }
}

class Fish {
  makeSound() { return "Буль!"; }
  move() { return "Плыву плавниками"; }
}

// Полиморфная функция - работает с любым животным
function describeAnimal(animal) {
  return `Звук: ${animal.makeSound()}, Движение: ${animal.move()}`;
}

const animals = [new Dog(), new Bird(), new Fish()];
animals.forEach(animal => {
  console.log(describeAnimal(animal)); // Разное поведение для каждого!
});

// 🔍 Переопределение методов
class Shape {
  area() {
    throw new Error('Метод area() должен быть реализован');
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }
  
  area() {
    return Math.PI * this.radius ** 2; // Своя реализация
  }
}

class Square extends Shape {
  constructor(side) {
    super();
    this.side = side;
  }
  
  area() {
    return this.side ** 2; // Другая реализация
  }
}
```

## 🎨 **АБСТРАКЦИЯ** — упрощение сложности

```javascript
// 🔍 Абстрактные классы (имитация)
class AbstractVehicle {
  constructor() {
    if (this.constructor === AbstractVehicle) {
      throw new Error('Нельзя создать экземпляр абстрактного класса');
    }
  }
  
  // Абстрактные методы - должны быть реализованы в наследниках
  start() {
    throw new Error('Метод start() должен быть реализован');
  }
  
  stop() {
    throw new Error('Метод stop() должен быть реализован');
  }
  
  // Конкретный метод - общий для всех
  getInfo() {
    return `Транспорт типа: ${this.constructor.name}`;
  }
}

class Car extends AbstractVehicle {
  start() {
    return "Завожу двигатель автомобиля";
  }
  
  stop() {
    return "Глушу двигатель автомобиля";
  }
}

class Bicycle extends AbstractVehicle {
  start() {
    return "Начинаю крутить педали";
  }
  
  stop() {
    return "Останавливаюсь и слезаю";
  }
}

// 🔍 Миксины - имитация интерфейсов
const CanFly = {
  fly() { return "Летаю"; }
};

const CanSwim = {
  swim() { return "Плаваю"; }
};

class Duck {
  constructor() {
    Object.assign(this, CanFly, CanSwim); // "Подмешиваем" способности
  }
  
  fly() { return "Утка летит"; }
  swim() { return "Утка плывет"; }
}
```

**🧠 Как запомнить принципы?**
- **Инкапсуляция** = **капсула** (скрываем внутренности)
- **Полиморфизм** = **много форм** (один метод, разные реализации)
- **Абстракция** = **абстрактно** (скрываем сложность, показываем суть)

**⚡ Современные инструменты JS:**
- **Приватные поля (#)** — для инкапсуляции
- **Duck typing** — для полиморфизма
- **Абстрактные классы** — для абстракции
- **Миксины** — для множественного наследования

**Вывод:**
JS поддерживает все ООП-принципы: замыкания и # для инкапсуляции, duck typing для полиморфизма, абстрактные классы для абстракции.

15. Что такое прототип? Как работает прототипное наследование в JS? Объясните работу кода.

```javascript
function Main () {}
Main.prototype = { protected: true };
const obj = new Main();
Main.prototype = { protected: false };
console.log('Object protection: ', obj.protected);
```

**Ответ:**

**Прототип** — это объект-шаблон, от которого другие объекты **наследуют** свойства и методы. В JS каждый объект имеет скрытую ссылку на свой прототип.

```javascript
// 🔍 Простой пример наследования

const animal = {
  type: 'живое существо',
  breathe() { return 'дышу'; }
};

const dog = Object.create(animal); // dog наследует от animal
dog.breed = 'лабрадор';            // Собственное свойство

console.log(dog.type);     // 'живое существо' (из прототипа animal)
console.log(dog.breed);    // 'лабрадор' (собственное свойство)
console.log(dog.breathe()); // 'дышу' (метод из прототипа)

// 🔍 Цепочка прототипов
console.log(dog.__proto__ === animal);           // true
console.log(animal.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__);         // null (конец цепочки)

// 🔍 Конструкторы и прототипы

function Person(name) {
  this.name = name;  // Собственное свойство каждого экземпляра
}

// Добавляем метод в прототип (общий для всех экземпляров)
Person.prototype.greet = function() {
  return `Привет, я ${this.name}`;
};

Person.prototype.species = 'человек'; // Общее свойство

const ivan = new Person('Иван');
const maria = new Person('Мария');

console.log(ivan.greet());    // "Привет, я Иван"
console.log(maria.greet());   // "Привет, я Мария"
console.log(ivan.species);    // "человек" (из прототипа)

// Оба объекта делят один прототип
console.log(ivan.__proto__ === maria.__proto__); // true
```

**🔍 Разбор кода из вопроса:**

```javascript
function Main() {}                    // 1. Создаем конструктор
Main.prototype = { protected: true }; // 2. Устанавливаем прототип

const obj = new Main();               // 3. Создаем объект
// obj.__proto__ теперь ссылается на { protected: true }

Main.prototype = { protected: false }; // 4. Меняем Main.prototype на НОВЫЙ объект
// Но obj.__proto__ все еще ссылается на СТАРЫЙ объект!

console.log('Object protection: ', obj.protected); // true
```

**Результат: `Object protection: true`**

**🧠 Почему именно `true`?**

```javascript
// Пошаговое объяснение:

// Шаг 1: Main.prototype указывает на объект A
const objectA = { protected: true };
Main.prototype = objectA;

// Шаг 2: obj создается и его __proto__ указывает на объект A
const obj = new Main();
// obj.__proto__ === objectA (true)

// Шаг 3: Main.prototype теперь указывает на НОВЫЙ объект B
const objectB = { protected: false };
Main.prototype = objectB;

// Шаг 4: obj все еще связан со старым объектом A!
console.log(obj.protected); // true (из объекта A)

// 🔍 Визуализация связей:
// obj.__proto__ → { protected: true }  (объект A)
// Main.prototype → { protected: false } (объект B)
```

**⚡ Важные принципы прототипов:**

```javascript
// 1. Поиск свойств идет по цепочке
const obj = { a: 1 };
obj.__proto__ = { b: 2 };
obj.__proto__.__proto__ = { c: 3 };

console.log(obj.a); // 1 (собственное)
console.log(obj.b); // 2 (из первого прототипа)
console.log(obj.c); // 3 (из второго прототипа)
console.log(obj.d); // undefined (не найдено)

// 2. Изменение прототипа влияет на наследников
const parent = { value: 'старое' };
const child = Object.create(parent);

console.log(child.value); // 'старое'

parent.value = 'новое';   // Меняем в прототипе
console.log(child.value); // 'новое' (изменилось!)

// 3. Но переназначение prototype не влияет на существующие объекты
function Constructor() {}
Constructor.prototype = { version: 1 };

const instance = new Constructor();
console.log(instance.version); // 1

Constructor.prototype = { version: 2 }; // Новый прототип
console.log(instance.version); // 1 (старое значение!)
```

**🧠 Как запомнить?**
Прототип = **генетический код**. Потомки наследуют гены, но смена генов родителя не меняет уже рожденных детей.

**Вывод:**
Объект сохраняет ссылку на прототип, который был на момент создания. Переназначение `Constructor.prototype` не влияет на уже созданные объекты.

16. В чем разница между композицией и наследованием?

**Ответ:**

**Наследование** — объект **является** чем-то (is-a). Берет поведение от родителя.  
**Композиция** — объект **содержит** что-то (has-a). Объединяет поведение разных частей.

```javascript
// 🔍 НАСЛЕДОВАНИЕ - "собака ЯВЛЯЕТСЯ животным"

class Animal {
  constructor(name) {
    this.name = name;
  }
  
  eat() { return `${this.name} ест`; }
  sleep() { return `${this.name} спит`; }
}

class Dog extends Animal {  // Dog ЯВЛЯЕТСЯ Animal
  bark() { return `${this.name} лает`; }
}

const rex = new Dog('Рекс');
console.log(rex.eat());   // "Рекс ест" (унаследовано)
console.log(rex.bark());  // "Рекс лает" (собственное)

// ❌ Проблемы наследования:
class FlyingAnimal extends Animal {
  fly() { return `${this.name} летает`; }
}

class SwimmingAnimal extends Animal {
  swim() { return `${this.name} плавает`; }
}

// А что если нужна утка, которая И летает И плавает? 
// Множественное наследование в JS невозможно!
```

```javascript
// 🔍 КОМПОЗИЦИЯ - "машина СОДЕРЖИТ двигатель и колеса"

class Engine {
  start() { return "Двигатель запущен"; }
  stop() { return "Двигатель заглушен"; }
}

class Wheels {
  rotate() { return "Колеса крутятся"; }
}

class Brakes {
  apply() { return "Тормоза сработали"; }
}

class Car {
  constructor() {
    this.engine = new Engine();   // СОДЕРЖИТ двигатель
    this.wheels = new Wheels();   // СОДЕРЖИТ колеса  
    this.brakes = new Brakes();   // СОДЕРЖИТ тормоза
  }
  
  start() {
    return this.engine.start();
  }
  
  drive() {
    return `${this.engine.start()}, ${this.wheels.rotate()}`;
  }
  
  stop() {
    return `${this.brakes.apply()}, ${this.engine.stop()}`;
  }
}

const car = new Car();
console.log(car.drive()); // "Двигатель запущен, Колеса крутятся"
```

**🎯 Сравнение на примере утки:**

```javascript
// ❌ НАСЛЕДОВАНИЕ - проблема с иерархией
class Bird {
  fly() { return "Летаю"; }
}

class WaterBird extends Bird {
  swim() { return "Плаваю"; }
  // Но пингвин не может летать! ❌
}

// ✅ КОМПОЗИЦИЯ - гибкие способности
const FlyingAbility = {
  fly() { return "Летаю"; }
};

const SwimmingAbility = {
  swim() { return "Плаваю"; }
};

const WalkingAbility = {
  walk() { return "Хожу"; }
};

// Фабрика для создания животных с нужными способностями
function createAnimal(name, abilities = []) {
  const animal = {
    name,
    introduce() { return `Я ${this.name}`; }
  };
  
  // Добавляем способности
  abilities.forEach(ability => {
    Object.assign(animal, ability);
  });
  
  return animal;
}

// Создаем разных животных
const duck = createAnimal('утка', [FlyingAbility, SwimmingAbility, WalkingAbility]);
const penguin = createAnimal('пингвин', [SwimmingAbility, WalkingAbility]);
const eagle = createAnimal('орел', [FlyingAbility, WalkingAbility]);

console.log(duck.introduce());    // "Я утка"
console.log(duck.fly());          // "Летаю"
console.log(duck.swim());         // "Плаваю"

console.log(penguin.introduce()); // "Я пингвин"
console.log(penguin.swim());       // "Плаваю"
// penguin.fly(); // undefined - пингвин не умеет летать
```

**🧠 Как запомнить разницу?**

| Принцип | Наследование | Композиция |
|---------|-------------|------------|
| **Отношение** | **IS-A** (является) | **HAS-A** (содержит) |
| **Связь** | Жесткая | Гибкая |
| **Изменения** | Сложно | Легко |
| **Переиспользование** | Ограниченное | Высокое |

```javascript
// 🔍 Миксины - компромисс между наследованием и композицией

const Flyable = {
  fly() { return `${this.name} летает`; }
};

const Swimmable = {
  swim() { return `${this.name} плавает`; }
};

class Duck {
  constructor(name) {
    this.name = name;
    // "Подмешиваем" способности
    Object.assign(this, Flyable, Swimmable);
  }
}

const donald = new Duck('Дональд');
console.log(donald.fly());   // "Дональд летает"
console.log(donald.swim());  // "Дональд плавает"
```

**⚡ Практические правила:**

✅ **Используйте наследование когда:**
- Четкая иерархия "is-a" (автомобиль является транспортом)
- Нужно переопределить поведение родителя

✅ **Используйте композицию когда:**
- Нужна гибкость (разные комбинации поведений)
- Объект может иметь несколько ролей
- Поведение может меняться в runtime

**🎯 Золотое правило:**
> **"Предпочитайте композицию наследованию"** — принцип из книги "Паттерны проектирования"

**Вывод:**
Наследование = "является", Композиция = "содержит". Композиция более гибкая, наследование проще для понимания.

17. Почему не стоит использовать конструкторы типа new String?

**Ответ:**

Конструкторы `new String()`, `new Number()`, `new Boolean()` создают **объекты**, а не **примитивы**. Это приводит к неожиданному поведению.

```javascript
// 🔍 ОСНОВНАЯ ПРОБЛЕМА - разные типы

const str1 = "hello";             // Примитив string
const str2 = new String("hello"); // Объект String

console.log(typeof str1);         // "string" 
console.log(typeof str2);         // "object" ❌

console.log(str1 === str2);       // false! (разные типы)
console.log(str1 == str2);        // true (приведение типов)

// 🔍 ПРОБЛЕМА 1: Условия работают неожиданно

const falsyPrimitive = false;           // Примитив
const falsyObject = new Boolean(false); // Объект

if (falsyPrimitive) {
  console.log("Не выполнится"); // Логично
}

if (falsyObject) {
  console.log("Выполнится!"); // ❌ Объект всегда truthy!
}

console.log(Boolean(falsyObject)); // true (объект в boolean)
console.log(falsyObject.valueOf()); // false (значение внутри объекта)

// 🔍 ПРОБЛЕМА 2: JSON сериализация

const name1 = "Иван";              // Примитив
const name2 = new String("Иван");   // Объект

console.log(JSON.stringify(name1)); // "Иван"
console.log(JSON.stringify(name2)); // {} ❌ Пустой объект!

// 🔍 ПРОБЛЕМА 3: Производительность

console.time('Примитивы');
for (let i = 0; i < 1000000; i++) {
  let s = "test";  // Быстро
}
console.timeEnd('Примитивы');

console.time('Объекты');
for (let i = 0; i < 1000000; i++) {
  let s = new String("test"); // Медленно ❌
}
console.timeEnd('Объекты');

// 🔍 ПРОБЛЕМА 4: Неожиданное поведение в массивах

const arr1 = [1, 2, 3];
const arr2 = [new Number(1), new Number(2), new Number(3)];

console.log(arr1.includes(2));     // true
console.log(arr2.includes(2));     // false ❌ (ищет объект, а не число)
console.log(arr2.includes(arr2[1])); // true (тот же объект)
```

**✅ ПРАВИЛЬНОЕ ИСПОЛЬЗОВАНИЕ (без new):**

```javascript
// Преобразование типов БЕЗ new
const num = Number("123");     // 123 (примитив number)
const bool = Boolean("true");  // true (примитив boolean)  
const str = String(123);       // "123" (примитив string)

// Проверка типов
console.log(typeof num);       // "number"
console.log(typeof bool);      // "boolean"
console.log(typeof str);       // "string"

// Полезные статические методы
console.log(Number.isInteger(42));      // true
console.log(Number.parseFloat("3.14")); // 3.14
console.log(String.fromCharCode(65));   // "A"
console.log(Array.isArray([]));         // true

// Безопасные преобразования
function toNumber(value) {
  const num = Number(value);
  return isNaN(num) ? 0 : num;
}

function toString(value) {
  return String(value);
}
```

**🧠 Как запомнить?**
- **БЕЗ new** = примитив (хорошо) 
- **С new** = объект (плохо)

**⚡ Исключения (когда объекты могут пригодиться):**

```javascript
// Очень редкие случаи - когда нужен именно объект
const numberObj = new Number(42);
numberObj.customProperty = 'дополнительные данные';

// Но лучше использовать обычные объекты
const betterApproach = {
  value: 42,
  customProperty: 'дополнительные данные'
};
```

**🚨 Частые ошибки:**
```javascript
// ❌ Плохо
const id = new Number(123);
const name = new String("Иван");
const isActive = new Boolean(true);

// ✅ Хорошо
const id = 123;              // или Number("123")
const name = "Иван";         // или String(123)
const isActive = true;       // или Boolean("true")
```

**Вывод:**
Используйте `Number()`, `String()`, `Boolean()` БЕЗ new для создания примитивов. Объекты-обёртки создают проблемы!

18. Что такое записи (records) и кортежи (tuples)? Чем они отличаются от обычных объектов?

**Ответ:**

**Records и Tuples** — это **будущие** неизменяемые структуры данных для JavaScript (пока proposal stage 2). Это как `Object.freeze()`, но лучше.

```javascript
// 🔍 RECORDS - неизменяемые объекты (БУДУЩЕЕ)

// Синтаксис будущего (пока не работает):
const record = #{
  name: "Иван",
  age: 30,
  address: #{
    city: "Москва",
    zip: "101000"
  }
};

// record.age = 31; // TypeError - нельзя изменить!

// 🔍 TUPLES - неизменяемые массивы (БУДУЩЕЕ)  

const tuple = #[1, 2, 3];
// tuple[0] = 10; // TypeError - нельзя изменить!

// tuple.push(4); // TypeError - нет мутирующих методов!

// 🔍 Главные отличия от обычных объектов:

// 1. СРАВНЕНИЕ ПО ЗНАЧЕНИЮ
const obj1 = { name: "Иван" };
const obj2 = { name: "Иван" };
console.log(obj1 === obj2); // false ❌ (разные ссылки)

// С records (в будущем):
// const rec1 = #{ name: "Иван" };
// const rec2 = #{ name: "Иван" };
// console.log(rec1 === rec2); // true ✅ (одинаковые значения)

// 2. НЕИЗМЕНЯЕМОСТЬ ГЛУБОКАЯ
const normalObj = { 
  user: { name: "Иван" },
  items: [1, 2, 3]
};
normalObj.user.name = "Петр"; // Можно изменить ❌

// С records:
// const recordObj = #{ 
//   user: #{ name: "Иван" },
//   items: #[1, 2, 3]
// };
// recordObj.user.name = "Петр"; // TypeError ✅
```

**⚡ ТЕКУЩИЕ АЛЬТЕРНАТИВЫ:**

```javascript
// 🔍 Object.freeze() - поверхностная заморозка

const frozenObj = Object.freeze({
  name: "Иван",
  details: { age: 30 }
});

// frozenObj.name = "Петр"; // TypeError в strict mode
frozenObj.details.age = 31;   // Работает! ❌ (не глубокая заморозка)

// 🔍 Глубокая заморозка (самописная)

function deepFreeze(obj) {
  // Замораживаем все вложенные объекты
  Object.values(obj).forEach(value => {
    if (value && typeof value === 'object') {
      deepFreeze(value);
    }
  });
  
  return Object.freeze(obj);
}

const deepFrozenObj = deepFreeze({
  name: "Иван",
  details: { age: 30 },
  hobbies: ["футбол", "чтение"]
});

// deepFrozenObj.details.age = 31; // TypeError ✅

// 🔍 Иммутабельные операции с массивами

const numbers = [1, 2, 3];

// ❌ Мутирующие операции
// numbers.push(4);      // Изменяет исходный массив
// numbers[0] = 10;      // Изменяет исходный массив

// ✅ Иммутабельные операции
const withAdded = [...numbers, 4];           // [1, 2, 3, 4]
const withChanged = numbers.map((n, i) => i === 0 ? 10 : n); // [10, 2, 3]
const filtered = numbers.filter(n => n > 1); // [2, 3]

console.log(numbers);    // [1, 2, 3] - не изменился!
console.log(withAdded);  // [1, 2, 3, 4]

// 🔍 Использование Map/Set как ключи (будет с records)

const map = new Map();

// С обычными объектами
const key1 = { id: 1 };
const key2 = { id: 1 };
map.set(key1, "значение1");
map.set(key2, "значение2");
console.log(map.size); // 2 ❌ (разные ключи из-за ссылок)

// С records (в будущем):
// const recKey1 = #{ id: 1 };
// const recKey2 = #{ id: 1 };
// map.set(recKey1, "значение1");
// map.set(recKey2, "значение2"); // Перезапишет первое!
// console.log(map.size); // 1 ✅ (один ключ)
```

**📚 Популярные библиотеки для иммутабельности:**

```javascript
// Immer - простая иммутабельность
import { produce } from 'immer';

const state = {
  todos: [
    { id: 1, text: 'Купить молоко', done: false }
  ],
  user: { name: 'Иван' }
};

const newState = produce(state, draft => {
  draft.todos.push({ id: 2, text: 'Погулять с собакой', done: false });
  draft.user.name = 'Петр';
});

console.log(state === newState); // false - новый объект
console.log(state.todos.length); // 1 - исходный не изменился
console.log(newState.todos.length); // 2 - новый содержит изменения
```

**🧠 Как запомнить?**
- **Records** = неизменяемые **объекты** (record = запись)
- **Tuples** = неизменяемые **массивы** (tuple = кортеж)
- **Структурное равенство** = сравнение по содержимому

**📊 Сравнительная таблица:**

| Особенность | Обычные объекты | Records/Tuples |
|-------------|-----------------|----------------|
| **Изменяемость** | Да | Нет |
| **Сравнение** | По ссылке | По значению |
| **Ключи Map/Set** | Разные ссылки | Одинаковые значения |
| **Производительность** | Стандартная | Оптимизированная |

**Вывод:**
Records/Tuples — это будущие неизменяемые структуры с сравнением по значению. Сейчас используйте `Object.freeze()` и библиотеки типа Immer.

20. Каковы различия в поведении ES5 функции-конструктора и ES2015 класса?

**Ответ:**

ES6 классы — это **синтаксический сахар** над функциями-конструкторами, но с важными отличиями в поведении.

```javascript
// 🔍 СИНТАКСИС - одно и то же, но по-разному

// ES5 функция-конструктор
function PersonES5(name) {
  this.name = name;
}

PersonES5.prototype.greet = function() {
  return `Привет, я ${this.name}`;
};

// ES6 класс
class PersonES6 {
  constructor(name) {
    this.name = name;
  }
  
  greet() {
    return `Привет, я ${this.name}`;
  }
}

// Оба создают одинаковые объекты:
const john1 = new PersonES5('Иван');
const john2 = new PersonES6('Иван');

console.log(john1.greet()); // "Привет, я Иван"
console.log(john2.greet()); // "Привет, я Иван"
```

**🔍 КЛЮЧЕВЫЕ РАЗЛИЧИЯ:**

```javascript
// 1️⃣ HOISTING (поднятие)

// ✅ Функция-конструктор поднимается
const person1 = new PersonFunction('Иван'); // Работает!

function PersonFunction(name) {
  this.name = name;
}

// ❌ Класс НЕ поднимается (TDZ)
// const person2 = new PersonClass('Иван'); // ReferenceError!

class PersonClass {
  constructor(name) {
    this.name = name;
  }
}

// 2️⃣ ВЫЗОВ БЕЗ new

// ✅ Функция работает без new (но this = window)
PersonFunction('Иван'); // this.name устанавливается в window.name

// ❌ Класс ТРЕБУЕТ new
// PersonClass('Иван'); // TypeError: Class constructor cannot be invoked without 'new'

// 3️⃣ STRICT MODE

function FunctionConstructor() {
  console.log(this); // window/global (в non-strict) или undefined (в strict)
}

class ClassConstructor {
  constructor() {
    console.log(this); // всегда undefined без new, всегда экземпляр с new
  }
}

// 4️⃣ ПЕРЕЧИСЛЯЕМОСТЬ МЕТОДОВ

function PersonFunc() {}
PersonFunc.prototype.method = function() {};

class PersonClass2 {
  method() {}
}

// Метод функции-конструктора перечисляем
console.log(Object.getOwnPropertyDescriptor(PersonFunc.prototype, 'method').enumerable); // true

// Метод класса НЕ перечисляем  
console.log(Object.getOwnPropertyDescriptor(PersonClass2.prototype, 'method').enumerable); // false

// Это влияет на for...in
for (let key in new PersonFunc()) {
  console.log(key); // Покажет 'method'
}

for (let key in new PersonClass2()) {
  console.log(key); // НЕ покажет 'method'
}
```

**🎯 НАСЛЕДОВАНИЕ - ГЛАВНАЯ РАЗНИЦА:**

```javascript
// 🔍 ES5 - ручное наследование (сложно)

function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function() {
  return `${this.name} издает звук`;
};

function Dog(name, breed) {
  Animal.call(this, name);  // Ручной вызов родительского конструктора
  this.breed = breed;
}

// Ручная настройка цепочки прототипов
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function() {
  return `${this.name} лает`;
};

// 🔍 ES6 - автоматическое наследование (просто)

class AnimalES6 {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    return `${this.name} издает звук`;
  }
}

class DogES6 extends AnimalES6 {
  constructor(name, breed) {
    super(name);     // Автоматический вызов родительского конструктора
    this.breed = breed;
  }
  
  bark() {
    return `${this.name} лает`;
  }
}

// Использование одинаковое:
const dog1 = new Dog('Рекс', 'лабрадор');
const dog2 = new DogES6('Рекс', 'лабрадор');

console.log(dog1.speak()); // "Рекс издает звук"
console.log(dog2.speak()); // "Рекс издает звук"
```

**🔍 ДРУГИЕ ТОНКОСТИ:**

```javascript
// 5️⃣ ПРОВЕРКА ТИПА
console.log(typeof PersonES5);    // "function"
console.log(typeof PersonES6);    // "function" (но с внутренним флагом [[IsClassConstructor]])

// 6️⃣ BABEL TRANSPILATION
// ES6 класс компилируется примерно в ES5 функцию-конструктор

// 7️⃣ SUPER В МЕТОДАХ
class Parent {
  greet() { return "Родитель"; }
}

class Child extends Parent {
  greet() {
    return super.greet() + " и ребенок"; // super работает только в классах
  }
}

// В ES5 super надо эмулировать:
function ChildES5() {}
ChildES5.prototype = Object.create(ParentES5.prototype);
ChildES5.prototype.greet = function() {
  return ParentES5.prototype.greet.call(this) + " и ребенок";
};
```

**🧠 Как запомнить?**
- **ES5 функции** = **гибкие** (можно вызывать без new), но **сложное наследование**
- **ES6 классы** = **строгие** (требуют new), но **простое наследование**

**📊 Сравнительная таблица:**

| Особенность | ES5 Функции | ES6 Классы |
|-------------|-------------|------------|
| **Hoisting** | Да | Нет (TDZ) |
| **Вызов без new** | Работает | TypeError |
| **Strict mode** | Зависит от контекста | Всегда |
| **Перечисляемость методов** | Да | Нет |
| **Наследование** | Ручное | Автоматическое |
| **super** | Эмуляция | Встроенная поддержка |

**Вывод:**
Классы ES6 — это функции-конструкторы с дополнительными ограничениями и удобным синтаксисом наследования.

21. Как реализовать паттерн «Модуль»?

**Ответ:**

🎯 **Паттерн Модуль** — это способ создать **приватную область видимости** и **публичный API**. Как коробка с секретами: снаружи видно только то, что разрешено.

```javascript
// 🔍 ОСНОВА - IIFE (немедленно вызываемая функция)

const Calculator = (function() {
  // ❌ ПРИВАТНОЕ - никто снаружи не видит
  let result = 0;
  let history = [];
  
  function log(operation, value) {
    history.push(`${operation}: ${value}`);
  }
  
  function validate(num) {
    return typeof num === 'number' && !isNaN(num);
  }
  
  // ✅ ПУБЛИЧНОЕ - возвращаем наружу
  return {
    add(num) {
      if (!validate(num)) throw new Error('Некорректное число');
      result += num;
      log('add', num);
      return this; // Для цепочки вызовов
    },
    
    subtract(num) {
      if (!validate(num)) throw new Error('Некорректное число');
      result -= num;
      log('subtract', num);
      return this;
    },
    
    multiply(num) {
      if (!validate(num)) throw new Error('Некорректное число');
      result *= num;
      log('multiply', num);
      return this;
    },
    
    getResult() {
      return result;
    },
    
    getHistory() {
      return [...history]; // Возвращаем копию
    },
    
    reset() {
      result = 0;
      history = [];
      return this;
    }
  };
})();

// Использование:
Calculator
  .add(10)
  .subtract(3)
  .multiply(2);

console.log(Calculator.getResult()); // 14
console.log(Calculator.getHistory()); // ['add: 10', 'subtract: 3', 'multiply: 2']

// ❌ Приватные данные недоступны:
// console.log(Calculator.result); // undefined
// console.log(Calculator.history); // undefined
```

**🔍 РАЗНЫЕ ВАРИАНТЫ МОДУЛЯ:**

```javascript
// 1️⃣ МОДУЛЬ С ПАРАМЕТРАМИ

const CounterFactory = function(startValue = 0, step = 1) {
  return (function(start, increment) {
    let count = start;
    
    return {
      next() {
        count += increment;
        return count;
      },
      
      prev() {
        count -= increment;
        return count;
      },
      
      get current() {
        return count;
      },
      
      reset() {
        count = start;
      }
    };
  })(startValue, step);
};

const counter1 = CounterFactory(0, 1);    // Считает 0, 1, 2, 3...
const counter2 = CounterFactory(10, 5);   // Считает 10, 15, 20, 25...

console.log(counter1.next()); // 1
console.log(counter2.next()); // 15

// 2️⃣ REVEALING MODULE PATTERN (показать только нужное)

const UserManager = (function() {
  // Приватные данные
  const users = [];
  const config = { maxUsers: 100 };
  
  // Приватные функции
  function generateId() {
    return Date.now() + Math.random();
  }
  
  function validateUser(userData) {
    return userData && typeof userData.name === 'string';
  }
  
  function addUser(userData) {
    if (users.length >= config.maxUsers) {
      throw new Error('Превышен лимит пользователей');
    }
    
    if (!validateUser(userData)) {
      throw new Error('Некорректные данные пользователя');
    }
    
    const user = {
      id: generateId(),
      createdAt: new Date(),
      ...userData
    };
    
    users.push(user);
    return user;
  }
  
  function removeUser(id) {
    const index = users.findIndex(user => user.id === id);
    if (index === -1) return false;
    
    users.splice(index, 1);
    return true;
  }
  
  function findUser(id) {
    return users.find(user => user.id === id) || null;
  }
  
  function getAllUsers() {
    return users.map(user => ({ ...user })); // Глубокая копия
  }
  
  // Показываем только то, что нужно
  return {
    create: addUser,
    remove: removeUser,
    find: findUser,
    getAll: getAllUsers,
    getCount: () => users.length,
    getLimit: () => config.maxUsers
  };
})();

// Использование:
const user1 = UserManager.create({ name: 'Иван', email: 'ivan@test.com' });
const user2 = UserManager.create({ name: 'Мария', email: 'maria@test.com' });

console.log(UserManager.getCount()); // 2
console.log(UserManager.find(user1.id)); // Объект пользователя
```

**🔍 СОВРЕМЕННЫЕ ВАРИАНТЫ:**

```javascript
// 3️⃣ ES6 МОДУЛИ (файловые модули)

// 📄 mathUtils.js
const PI = 3.14159;
let operationsCount = 0;

function logOperation(op) {
  operationsCount++;
  console.log(`Операция ${operationsCount}: ${op}`);
}

export function add(a, b) {
  logOperation(`${a} + ${b}`);
  return a + b;
}

export function multiply(a, b) {
  logOperation(`${a} * ${b}`);
  return a * b;
}

export function calculateCircleArea(radius) {
  logOperation(`Площадь круга r=${radius}`);
  return PI * radius * radius;
}

export default {
  version: '1.0.0',
  author: 'Разработчик'
};

// 📄 main.js
import { add, multiply, calculateCircleArea } from './mathUtils.js';
import info from './mathUtils.js';

console.log(add(5, 3));                    // 8
console.log(calculateCircleArea(10));      // 314.159
console.log(info.version);                 // "1.0.0"

// 4️⃣ МОДУЛЬ-СИНГЛТОН (одна единственная инстанция)

const DatabaseConnection = (function() {
  let instance = null;
  let connectionCount = 0;
  
  function createConnection() {
    connectionCount++;
    console.log(`Создано подключений: ${connectionCount}`);
    
    return {
      id: generateId(),
      host: 'localhost',
      port: 5432,
      isConnected: false,
      
      connect() {
        if (!this.isConnected) {
          this.isConnected = true;
          console.log(`Подключен к базе ${this.host}:${this.port}`);
        }
        return this;
      },
      
      disconnect() {
        if (this.isConnected) {
          this.isConnected = false;
          console.log('Отключен от базы');
        }
        return this;
      },
      
      query(sql) {
        if (!this.isConnected) {
          throw new Error('Сначала подключитесь к базе');
        }
        console.log(`Выполняется запрос: ${sql}`);
        return { result: 'mock data' };
      }
    };
  }
  
  function generateId() {
    return Math.random().toString(36).substr(2, 9);
  }
  
  return {
    getInstance() {
      if (!instance) {
        instance = createConnection();
      }
      return instance;
    },
    
    getConnectionCount() {
      return connectionCount;
    }
  };
})();

// Использование синглтона:
const db1 = DatabaseConnection.getInstance();
const db2 = DatabaseConnection.getInstance();

console.log(db1 === db2); // true - тот же объект!
console.log(DatabaseConnection.getConnectionCount()); // 1

db1.connect().query('SELECT * FROM users');
db2.query('SELECT * FROM products'); // Тот же объект - уже подключен
```

**🧠 Как запомнить паттерны?**

- **IIFE** = **Коробка с секретами** (приватное внутри, публичное снаружи)
- **Revealing** = **Витрина магазина** (показываем только товар, склад скрыт)  
- **Синглтон** = **Президент страны** (может быть только один)

**⚡ Практические советы:**

```javascript
// ✅ Хорошие практики модулей:

// 1. Всегда возвращайте новые объекты/массивы
getUsers() {
  return users.map(user => ({ ...user })); // Копия ✅
  // return users; // Прямая ссылка ❌
}

// 2. Используйте геттеры для только чтения
get count() { return items.length; }

// 3. Валидируйте входные данные
add(item) {
  if (!item || typeof item !== 'object') {
    throw new Error('Некорректный элемент');
  }
  // ...
}

// 4. Логируйте важные операции
function logAction(action) {
  console.log(`[${new Date().toISOString()}] ${action}`);
}
```

**📊 Когда использовать какой модуль:**

| Ситуация | Паттерн | Причина |
|----------|---------|---------|
| **Утилиты** | IIFE/ES6 модули | Простота |
| **Управление состоянием** | Revealing Module | Контроль доступа |
| **Одно подключение** | Синглтон | Экономия ресурсов |
| **Фабрика объектов** | Модуль с параметрами | Настройка |

**Вывод:**
Модули создают приватную область видимости и контролируют доступ к данным. Выбирайте паттерн в зависимости от задачи!
```

22. Почему typeof null возвращает object?

**Ответ:**

🎯 Это **древний баг** в JavaScript, который **нельзя исправить** из-за обратной совместимости. Один из самых известных "сюрпризов" языка!

```javascript
// 🔍 ПРОБЛЕМА

console.log(typeof null); // "object" ❌ - это НЕ объект!

console.log(typeof undefined); // "undefined" ✅
console.log(typeof true);       // "boolean" ✅  
console.log(typeof 42);         // "number" ✅
console.log(typeof "hello");    // "string" ✅
console.log(typeof {});         // "object" ✅
console.log(typeof []);         // "object" ✅ (массивы тоже объекты)
console.log(typeof function(){}); // "function" ✅

// Но null не объект!
console.log(null instanceof Object); // false ✅
console.log(null === null);           // true ✅
```

**🔍 ИСТОРИЧЕСКАЯ ПРИЧИНА:**

В первых версиях JavaScript (1995 год) значения представлялись как **32-битные слова**:

- Первые **3 бита** = тег типа  
- Остальные **29 бит** = значение

```javascript
// 🧠 Таблица тегов в оригинальном JavaScript:

// 000 - object    (объект)
// 001 - int       (целое число)  
// 010 - double    (число с плавающей точкой)
// 100 - string    (строка)
// 110 - boolean   (логическое значение)

// null представлялся как 0x00000000 (все нули)
// Первые 3 бита = 000 = object ❌
```

**⚡ ПРАВИЛЬНЫЕ СПОСОБЫ ПРОВЕРКИ:**

```javascript
// 🔍 ПРОВЕРКА НА NULL

// ❌ Неправильно:
if (typeof value === 'object') {
  // Сюда попадет и null! 
}

// ✅ Правильно:
if (value === null) {
  console.log('Это null');
}

if (value !== null && typeof value === 'object') {
  console.log('Это реальный объект');
}

// 🔍 УНИВЕРСАЛЬНАЯ ФУНКЦИЯ ОПРЕДЕЛЕНИЯ ТИПА

function getType(value) {
  // Специальная обработка null
  if (value === null) return 'null';
  if (value === undefined) return 'undefined';
  
  // Для остальных используем typeof
  return typeof value;
}

// Тестируем:
console.log(getType(null));           // "null" ✅
console.log(getType(undefined));      // "undefined" ✅
console.log(getType(42));             // "number" ✅
console.log(getType({}));             // "object" ✅
console.log(getType([]));             // "object" ✅
console.log(getType(function(){}));   // "function" ✅

// 🔍 ТОЧНОЕ ОПРЕДЕЛЕНИЕ ТИПА

function preciseType(value) {
  return Object.prototype.toString.call(value).slice(8, -1).toLowerCase();
}

// Тестируем все типы:
console.log(preciseType(null));           // "null" ✅
console.log(preciseType(undefined));      // "undefined" ✅
console.log(preciseType(42));             // "number" ✅
console.log(preciseType("hello"));        // "string" ✅
console.log(preciseType(true));           // "boolean" ✅
console.log(preciseType({}));             // "object" ✅
console.log(preciseType([]));             // "array" ✅
console.log(preciseType(new Date()));     // "date" ✅
console.log(preciseType(/regex/));        // "regexp" ✅
console.log(preciseType(function(){}));   // "function" ✅
console.log(preciseType(new Map()));      // "map" ✅
console.log(preciseType(new Set()));      // "set" ✅
```

**🔍 ПРАКТИЧЕСКИЕ ПРОВЕРКИ:**

```javascript
// 🚨 ЧАСТЫЕ ОШИБКИ С NULL

// ❌ Ошибка 1: проверка на объект
function processObject(obj) {
  if (typeof obj === 'object') {
    obj.method(); // TypeError если obj = null!
  }
}

// ✅ Исправление:
function processObject(obj) {
  if (obj !== null && typeof obj === 'object') {
    obj.method(); // Безопасно!
  }
}

// ❌ Ошибка 2: проверка на "falsy" объект
const data = null;
if (data && typeof data === 'object') {
  // Безопасно, но можно лучше
}

// ✅ Лучше:
function isRealObject(value) {
  return (
    value !== null &&
    typeof value === 'object' &&
    !Array.isArray(value)
  );
}

console.log(isRealObject({}));     // true ✅
console.log(isRealObject([]));     // false ✅
console.log(isRealObject(null));   // false ✅
console.log(isRealObject('str'));  // false ✅

// 🔍 ПРОВЕРКА НА PLAIN OBJECT (простой объект)

function isPlainObject(value) {
  if (value === null || typeof value !== 'object') {
    return false;
  }
  
  // Исключаем массивы, даты, регулярки и т.д.
  if (Array.isArray(value)) return false;
  if (value instanceof Date) return false;
  if (value instanceof RegExp) return false;
  
  // Проверяем, что это именно Object
  return Object.prototype.toString.call(value) === '[object Object]';
}

// Тестируем:
console.log(isPlainObject({}));              // true ✅
console.log(isPlainObject({ a: 1 }));        // true ✅
console.log(isPlainObject([]));              // false ✅
console.log(isPlainObject(null));            // false ✅
console.log(isPlainObject(new Date()));      // false ✅
console.log(isPlainObject(/regex/));         // false ✅
console.log(isPlainObject(function(){}));    // false ✅

// 🔍 БЕЗОПАСНАЯ РАБОТА С ОБЪЕКТАМИ

function safeObjectAccess(obj, key) {
  // Проверяем, что obj действительно объект
  if (obj === null || typeof obj !== 'object') {
    return undefined;
  }
  
  return obj[key];
}

// Примеры использования:
console.log(safeObjectAccess(null, 'name'));        // undefined (не упадет)
console.log(safeObjectAccess({ name: 'Иван' }, 'name')); // "Иван"
console.log(safeObjectAccess([], 'length'));        // 0 (массив тоже объект)

// 🔍 ПОЛЕЗНАЯ УТИЛИТА ДЛЯ ОТЛАДКИ

function debugType(value, label = 'value') {
  console.log(`${label}:`, {
    value: value,
    typeof: typeof value,
    precise: preciseType(value),
    isNull: value === null,
    isUndefined: value === undefined,
    isArray: Array.isArray(value),
    constructor: value?.constructor?.name
  });
}

// Тестируем:
debugType(null, 'null');
debugType({}, 'object');
debugType([], 'array');
debugType(function(){}, 'function');

// Выведет:
// null: { value: null, typeof: "object", precise: "null", isNull: true, ... }
// object: { value: {}, typeof: "object", precise: "object", isNull: false, ... }
```

**🧠 Как запомнить?**
- **typeof null** = **"object"** (ошибка истории) 
- **Всегда проверяйте** `value === null` **отдельно**
- **null ≠ object**, это **отсутствие значения**

**⚡ Современные альтернативы:**

```javascript
// Nullish coalescing (ES2020)
const name = user?.name ?? 'Неизвестно';

// Optional chaining (ES2020)  
const city = user?.address?.city;

// Сравнение с null
if (user == null) {  // null ИЛИ undefined
  console.log('Пользователь не определен');
}

if (user === null) {  // только null
  console.log('Пользователь явно null');
}
```

**📊 Сравнительная таблица проверок:**

| Значение | `typeof` | `=== null` | `== null` | `Boolean()` |
|----------|----------|------------|-----------|-------------|
| `null` | "object" ❌ | true ✅ | true ✅ | false ✅ |
| `undefined` | "undefined" ✅ | false ✅ | true ❌ | false ✅ |
| `{}` | "object" ✅ | false ✅ | false ✅ | true ✅ |
| `[]` | "object" ✅ | false ✅ | false ✅ | true ✅ |
| `0` | "number" ✅ | false ✅ | false ✅ | false ✅ |

**Вывод:**
`typeof null === "object"` — это исторический баг. Используйте `value === null` для точной проверки на null!
console.log(isPlainObject([]));           // false
console.log(isPlainObject(null));         // false
console.log(isPlainObject(new Date()));   // false
```

23. Что такое явное и неявное приведение (преобразование) типов данных в JS? Как происходит преобразование типов в следующих примерах:

```javascript
{}+[]+{}+[1]
!!"false" == !!"true"
['x'] == 'x'
```

**Ответ:**
Разберем каждый пример пошагово:

```javascript
// 1. {}+[]+{}+[1]
// Важно: {} в начале строки интерпретируется как пустой блок кода!

{}          // Пустой блок (игнорируется)
+[]         // Унарный плюс к массиву: +[] → +"" → 0
+{}         // Унарный плюс к объекту: +{} → +"[object Object]" → NaN
+[1]        // Унарный плюс к массиву: +[1] → +"1" → 1

// Итак: 0 + NaN + 1 = NaN

// Чтобы это работало как выражение, нужны скобки:
({} + [] + {} + [1])
// {} → "[object Object]"
// [] → ""
// {} → "[object Object]" 
// [1] → "1"
// Результат: "[object Object][object Object]1"

// 2. !!"false" == !!"true"
!!"false"   // !"false" → !true → false
           // !false → true

!!"true"    // !"true" → !true → false
           // !false → true

// true == true → true

// 3. ['x'] == 'x'
['x']       // При сравнении массив преобразуется в строку
           // ['x'].toString() → "x"
// "x" == "x" → true

// Подробный анализ преобразований:

// Объект в примитив (ToPrimitive)
const obj = {
  valueOf() { return 42; },
  toString() { return "hello"; }
};

console.log(+obj);      // 42 (числовой контекст - valueOf)
console.log(String(obj)); // "hello" (строковый контекст - toString)

// Массив в примитив
console.log([].toString());      // ""
console.log([1,2,3].toString()); // "1,2,3"
console.log([].valueOf());       // [] (не примитив, используется toString)

// Объект в примитив
console.log({}.toString());      // "[object Object]"
console.log({}.valueOf());       // {} (не примитив, используется toString)

// Сложные примеры
console.log([] + []);           // "" ([] toString → "", "" + "" → "")
console.log([] + {});           // "[object Object]" 
console.log({} + []);           // 0 (если {} блок) или "[object Object]" (если выражение)
console.log([1,2] + [3,4]);     // "1,23,4"

// Операторы сравнения
console.log([0] == false);      // true ([0] → "0" → 0, false → 0)
console.log([1] == true);       // true ([1] → "1" → 1, true → 1)
console.log([2] == true);       // false ([2] → "2" → 2, true → 1)

// Особые случаи
console.log(null + 1);          // 1 (null → 0)
console.log(undefined + 1);     // NaN (undefined → NaN)
console.log("5" * "2");         // 10 (обе строки → числа)
console.log("5" * "a");         // NaN
```

24. Опишите основные принципы работы «сборщика мусора» в JS-движках (engines).

**Ответ:**

🎯 **Сборщик мусора (GC)** — это **дворник памяти**, который автоматически находит и удаляет объекты, которые больше не используются. Работает незаметно, но знать его принципы важно!

```javascript
// 🔍 ОСНОВНАЯ ИДЕЯ - достижимость объектов

// ✅ ДОСТИЖИМЫЕ объекты (НЕ удаляются):
let user = { name: 'Иван' };        // Достижим через переменную user
const users = [user];               // Достижим через массив users
globalThis.currentUser = user;      // Достижим через глобальную переменную

// ❌ НЕДОСТИЖИМЫЕ объекты (УДАЛЯЮТСЯ):
function createTempObject() {
  const temp = { data: 'большие данные' };
  // temp недостижим после выхода из функции
  return temp.id; // undefined, но temp уже не нужен
}

createTempObject(); // После выполнения temp удаляется GC

// 🔍 АЛГОРИТМ "MARK & SWEEP" (пометить и подмести)

// 1️⃣ MARK - помечает все достижимые объекты
//    Начинает с "корней" (глобальные переменные, стек вызовов)
//    Проходит по всем ссылкам и помечает объекты

// 2️⃣ SWEEP - удаляет все непомеченные объекты  
//    Освобождает память и возвращает её системе

// 🧠 Представьте комнату с проводами:
// Корни = розетки в стене (всегда есть ток)
// Объекты = устройства  
// Ссылки = провода между устройствами
// GC находит все подключенные устройства и выбрасывает остальные
```

**🔍 СОВРЕМЕННЫЕ ОПТИМИЗАЦИИ:**

```javascript
// 1️⃣ ПОКОЛЕНЧЕСКАЯ СБОРКА (Generational GC)

// Принцип: "молодые объекты умирают быстро, старые живут долго"

// 👶 МОЛОДЫЕ ОБЪЕКТЫ (Young Generation) - проверяются часто
function processData() {
  const temp1 = { step: 1 };          // Создан
  const temp2 = transform(temp1);     // temp1 больше не нужен
  const temp3 = validate(temp2);      // temp2 больше не нужен  
  return temp3.result;                // temp3 тоже скоро не нужен
}

// Большинство temp объектов станут мусором быстро
// GC проверяет молодые объекты каждые ~10мс

// 👴 СТАРЫЕ ОБЪЕКТЫ (Old Generation) - проверяются редко  
const appConfig = { theme: 'dark' };  // Живет долго
const userCache = new Map();          // Растет, но не удаляется
const eventBus = new EventEmitter();  // Используется постоянно

// Старые объекты проверяются каждые ~100мс или реже

// 2️⃣ ИНКРЕМЕНТАЛЬНАЯ СБОРКА

// Проблема: GC может заблокировать UI
function heavyCalculation() {
  // Если GC запустится здесь на 50мс, UI зависнет!
  for (let i = 0; i < 1000000; i++) {
    // вычисления...
  }
}

// Решение: работа GC разбивается на мелкие куски по 5-10мс
// Между кусками выполняется JavaScript код
// Итого: нет заметных пауз, UI отзывчивый

// 3️⃣ ТРЁХЦВЕТНАЯ РАЗМЕТКА  

// Представьте раскрашивание карты:
// ⚪ БЕЛЫЙ - не посещен (потенциальный мусор)
// 🔘 СЕРЫЙ - найден, но не обработан (в очереди)
// ⚫ ЧЕРНЫЙ - найден и обработан (точно живой)

const objectStates = {
  white: 'Потенциальный мусор',
  gray: 'В процессе проверки', 
  black: 'Точно нужен'
};

// Алгоритм:
// 1. Все объекты = белые
// 2. Корни = серые  
// 3. Берем серый объект, все его ссылки = серые, сам = черный
// 4. Повторяем пока есть серые
// 5. Удаляем все белые
```

**🔍 СПЕЦИФИКА V8 (Chrome, Node.js):**

```javascript
// 🚀 V8 использует несколько сборщиков:

// 1️⃣ SCAVENGER - для молодых объектов
//    Очень быстрый (~1-5мс)
//    Использует copying GC (копирует живые объекты)

// 2️⃣ MARK-COMPACT - для старых объектов  
//    Медленнее (~10-100мс)
//    Сжимает память, убирает фрагментацию

// 3️⃣ CONCURRENT MARKING - параллельная разметка
//    Работает в фоновом потоке
//    Не блокирует JavaScript

// 4️⃣ INCREMENTAL MARKING - пошаговая разметка
//    Разбивает работу на мелкие части
//    Чередуется с выполнением JS

// 🔍 Мониторинг памяти в V8:
if (typeof performance !== 'undefined' && performance.memory) {
  const memory = performance.memory;
  
  console.log('📊 Статистика памяти:');
  console.log(`Использовано: ${Math.round(memory.usedJSHeapSize / 1024 / 1024)}MB`);
  console.log(`Выделено: ${Math.round(memory.totalJSHeapSize / 1024 / 1024)}MB`);
  console.log(`Лимит: ${Math.round(memory.jsHeapSizeLimit / 1024 / 1024)}MB`);
  
  const usage = (memory.usedJSHeapSize / memory.jsHeapSizeLimit * 100).toFixed(1);
  console.log(`Заполнено: ${usage}%`);
}

// 🔍 Принудительный запуск GC (только в Node.js с флагом --expose-gc)
if (typeof global !== 'undefined' && global.gc) {
  console.log('Память до:', process.memoryUsage().heapUsed);
  global.gc(); // Принудительная сборка мусора
  console.log('Память после:', process.memoryUsage().heapUsed);
}
```

**⚡ ПРАКТИЧЕСКИЕ СОВЕТЫ:**

```javascript
// 🔍 КАК ПОМОЧЬ СБОРЩИКУ МУСОРА:

// 1️⃣ Очищайте таймеры и обработчики
class Component {
  constructor() {
    this.timers = new Set();
    this.listeners = new Set();
  }
  
  setTimeout(fn, ms) {
    const id = setTimeout(() => {
      this.timers.delete(id);
      fn();
    }, ms);
    this.timers.add(id);
    return id;
  }
  
  addEventListener(element, event, handler) {
    element.addEventListener(event, handler);
    const cleanup = () => element.removeEventListener(event, handler);
    this.listeners.add(cleanup);
    return cleanup;
  }
  
  destroy() {
    // Очищаем все таймеры
    this.timers.forEach(id => clearTimeout(id));
    this.timers.clear();
    
    // Удаляем все обработчики
    this.listeners.forEach(cleanup => cleanup());
    this.listeners.clear();
  }
}

// 2️⃣ Избегайте циклических ссылок
class Parent {
  constructor() {
    this.children = [];
  }
  
  addChild(child) {
    this.children.push(child);
    child.parent = this; // ❌ Циклическая ссылка!
  }
}

// ✅ Лучше:
class BetterParent {
  constructor() {
    this.children = [];
  }
  
  addChild(child) {
    this.children.push(child);
    // Используем WeakMap для обратных ссылок
    parentMap.set(child, this);
  }
}

const parentMap = new WeakMap(); // Слабые ссылки не мешают GC

// 3️⃣ Используйте WeakMap/WeakSet
const objectMetadata = new WeakMap();

function setMetadata(obj, data) {
  objectMetadata.set(obj, data);
  // Когда obj удалится, metadata тоже удалится автоматически!
}

// 4️⃣ Освобождайте большие объекты
let bigData = new Array(1000000).fill('data');
processData(bigData);
bigData = null; // Явно освобождаем ссылку

// 5️⃣ Используйте Object pooling для частых создания объектов
class ObjectPool {
  constructor(createFn, resetFn) {
    this.createFn = createFn;
    this.resetFn = resetFn;
    this.pool = [];
  }
  
  acquire() {
    return this.pool.pop() || this.createFn();
  }
  
  release(obj) {
    this.resetFn(obj);
    this.pool.push(obj);
  }
}

const vectorPool = new ObjectPool(
  () => ({ x: 0, y: 0 }),
  (obj) => { obj.x = 0; obj.y = 0; }
);

// Вместо создания миллионов векторов:
function animate() {
  const vector = vectorPool.acquire(); // Переиспользуем
  vector.x = Math.random();
  vector.y = Math.random();
  
  // ... используем vector ...
  
  vectorPool.release(vector); // Возвращаем в пул
}
```

**🚨 ЧАСТЫЕ ПРОБЛЕМЫ:**

```javascript
// 1️⃣ Забытые таймеры
const intervalId = setInterval(() => {
  console.log('Работаю вечно!');
}, 1000);
// ❌ Забыли clearInterval(intervalId) - утечка памяти!

// 2️⃣ Замыкания с большими данными
function createHandler(bigArray) {
  return function(event) {
    // bigArray недоступен для GC пока жив handler!
    console.log('Handler called');
  };
}

// ✅ Лучше:
function createHandler(bigArray) {
  const needed = bigArray.length; // Берем только нужное
  return function(event) {
    console.log('Array size was:', needed);
    // bigArray теперь может быть удален
  };
}

// 3️⃣ DOM ссылки
const elements = [];
document.querySelectorAll('.item').forEach(el => {
  elements.push(el); // ❌ Элементы не удалятся даже после removeChild!
});

// ✅ Лучше:
const elements = new WeakSet();
```

**🧠 Как запомнить?**
- **GC = дворник памяти**, убирает мусор автоматически
- **Mark & Sweep** = пометить нужное + выбросить ненужное  
- **Поколения** = молодые умирают быстро, старые живут долго
- **Помогайте GC** = очищайте ссылки, таймеры, обработчики

**Вывод:**
Сборщик мусора работает автоматически, но понимание его принципов помогает писать эффективный код без утечек памяти!
```

27. Опишите назначение и принципы работы с коллекциями WeakMap и WeakSet. Чем они отличаются от коллекций Map и Set соответственно?

**Ответ:**

🎯 **WeakMap и WeakSet** — это **слабые коллекции**. Они не мешают сборщику мусора удалять объекты. Как временные стикеры — исчезают вместе с объектом!

```javascript
// 🔍 ОСНОВНАЯ РАЗНИЦА - сильные vs слабые ссылки

// ❌ MAP - СИЛЬНЫЕ ссылки (объект не удаляется)
const strongMap = new Map();
let user = { name: 'Иван', id: 1 };

strongMap.set(user, 'важные данные');
user = null; // Обнуляем переменную

// Но объект { name: 'Иван', id: 1 } всё ещё жив в памяти!
// strongMap продолжает держать ссылку на него
console.log(strongMap.size); // 1 - объект не удален

// ✅ WEAKMAP - СЛАБЫЕ ссылки (объект может удалиться)
const weakMap = new WeakMap();
let user2 = { name: 'Мария', id: 2 };

weakMap.set(user2, 'временные данные');
user2 = null; // Обнуляем переменную

// Объект { name: 'Мария', id: 2 } может быть удален GC
// weakMap не держит его в памяти принудительно
// console.log(weakMap.size); // ❌ Ошибка! У WeakMap нет size
```

**🔍 ДЕТАЛЬНОЕ СРАВНЕНИЕ:**

```javascript
// 🆚 MAP vs WEAKMAP

// 1️⃣ ТИПЫ КЛЮЧЕЙ
const map = new Map();
const weakMap = new WeakMap();

// Map принимает любые ключи:
map.set('строка', 'значение');
map.set(123, 'значение');
map.set(true, 'значение');
map.set(Symbol('id'), 'значение');
map.set({ id: 1 }, 'значение'); // ✅ Объект тоже можно

// WeakMap принимает ТОЛЬКО объекты:
// weakMap.set('строка', 'значение');    // ❌ TypeError!
// weakMap.set(123, 'значение');         // ❌ TypeError!
// weakMap.set(true, 'значение');        // ❌ TypeError!
weakMap.set({ id: 1 }, 'значение');     // ✅ Только объекты!

// 2️⃣ ИТЕРАЦИЯ
// Map можно перебирать:
for (let [key, value] of map) {
  console.log(key, value);
}

map.forEach((value, key) => console.log(key, value));
console.log([...map.keys()]);     // Все ключи
console.log([...map.values()]);   // Все значения
console.log([...map.entries()]);  // Все пары

// WeakMap НЕЛЬЗЯ перебирать:
// for (let [key, value] of weakMap) { } // ❌ TypeError!
// weakMap.forEach(...);                 // ❌ Метода нет!
// [...weakMap.keys()];                  // ❌ Метода нет!

// 3️⃣ РАЗМЕР И ОЧИСТКА
console.log(map.size);        // ✅ Количество элементов
// console.log(weakMap.size); // ❌ Свойства нет!

map.clear();        // ✅ Очистить всё
// weakMap.clear(); // ❌ Метода нет!

// 4️⃣ ДОСТУПНЫЕ МЕТОДЫ
console.log('MAP методы:', ['set', 'get', 'has', 'delete', 'clear', 'size', 'forEach', 'keys', 'values', 'entries']);
console.log('WEAKMAP методы:', ['set', 'get', 'has', 'delete']); // Только 4 метода!
```

**🔍 ПРАКТИЧЕСКИЕ ПРИМЕНЕНИЯ:**

```javascript
// 1️⃣ ПРИВАТНЫЕ ДАННЫЕ КЛАССОВ

const privateData = new WeakMap();

class BankAccount {
  constructor(accountNumber) {
    this.accountNumber = accountNumber; // Публичные данные
    
    // Приватные данные в WeakMap
    privateData.set(this, {
      balance: 0,
      pin: null,
      transactionHistory: []
    });
  }
  
  setPin(pin) {
    if (pin.length !== 4) throw new Error('PIN должен быть 4 цифры');
    privateData.get(this).pin = pin;
  }
  
  deposit(amount, pin) {
    const data = privateData.get(this);
    if (data.pin !== pin) throw new Error('Неверный PIN');
    
    data.balance += amount;
    data.transactionHistory.push({ type: 'deposit', amount, date: new Date() });
    
    return `Баланс: ${data.balance}`;
  }
  
  getBalance(pin) {
    const data = privateData.get(this);
    if (data.pin !== pin) throw new Error('Неверный PIN');
    return data.balance;
  }
  
  // ✨ Когда объект BankAccount удалится, 
  //    приватные данные тоже удалятся автоматически!
}

const account = new BankAccount('123456789');
account.setPin('1234');
account.deposit(1000, '1234');

console.log(account.getBalance('1234')); // 1000

// Приватные данные недоступны снаружи:
console.log(account.balance); // undefined ✅
console.log(account.pin);     // undefined ✅

// 2️⃣ КЭШИРОВАНИЕ РЕЗУЛЬТАТОВ ДЛЯ ОБЪЕКТОВ

const expensiveResultsCache = new WeakMap();

function calculateExpensiveResult(obj) {
  // Проверяем кэш
  if (expensiveResultsCache.has(obj)) {
    console.log('🚀 Взяли из кэша!');
    return expensiveResultsCache.get(obj);
  }
  
  // Дорогие вычисления
  console.log('⏳ Выполняем дорогие вычисления...');
  const result = {
    processed: true,
    hash: obj.toString().length * 42,
    timestamp: Date.now()
  };
  
  // Сохраняем в кэш
  expensiveResultsCache.set(obj, result);
  return result;
}

// Использование:
const data1 = { important: 'data' };
const data2 = { other: 'data' };

console.log(calculateExpensiveResult(data1)); // Вычисляем
console.log(calculateExpensiveResult(data1)); // Из кэша ✅
console.log(calculateExpensiveResult(data2)); // Вычисляем

// Когда data1 удалится, его кэш тоже удалится автоматически!

// 3️⃣ DOM МЕТАДАННЫЕ

const elementMetadata = new WeakMap();

function enhanceElement(element) {
  elementMetadata.set(element, {
    clicks: 0,
    created: Date.now(),
    listeners: [],
    animations: []
  });
  
  const clickHandler = () => {
    const meta = elementMetadata.get(element);
    meta.clicks++;
    console.log(`Кликов: ${meta.clicks}`);
  };
  
  element.addEventListener('click', clickHandler);
  elementMetadata.get(element).listeners.push(clickHandler);
}

// Когда DOM элемент удалится, его метаданные тоже удалятся!
```

**🔍 WEAKSET - СЛАБЫЕ МНОЖЕСТВА:**

```javascript
// SET vs WEAKSET - такая же логика

const regularSet = new Set();
const weakSet = new WeakSet();

// Set принимает любые значения:
regularSet.add('строка');
regularSet.add(123);
regularSet.add({ id: 1 });

// WeakSet принимает ТОЛЬКО объекты:
// weakSet.add('строка');  // ❌ TypeError!
// weakSet.add(123);       // ❌ TypeError!
weakSet.add({ id: 1 });   // ✅ Только объекты!

// 🔍 Практическое применение WeakSet:

// 1️⃣ ОТСЛЕЖИВАНИЕ АКТИВНЫХ ОБЪЕКТОВ
const activeUsers = new WeakSet();
const processedFiles = new WeakSet();

class User {
  constructor(name) {
    this.name = name;
    activeUsers.add(this); // Добавляем в активные
  }
  
  logout() {
    activeUsers.delete(this); // Убираем из активных
  }
}

function isUserActive(user) {
  return activeUsers.has(user);
}

const user1 = new User('Иван');
const user2 = new User('Мария');

console.log(isUserActive(user1)); // true
user1.logout();
console.log(isUserActive(user1)); // false

// 2️⃣ ПРЕДОТВРАЩЕНИЕ ПОВТОРНОЙ ОБРАБОТКИ
function processFile(file) {
  if (processedFiles.has(file)) {
    console.log('Файл уже обработан');
    return;
  }
  
  // Обрабатываем файл
  console.log(`Обрабатываем ${file.name}`);
  processedFiles.add(file);
}

const file1 = { name: 'document.pdf', size: 1024 };
processFile(file1); // Обрабатываем document.pdf
processFile(file1); // Файл уже обработан
```

**🧠 Как запомнить разницу?**

| Особенность | **Map/Set** | **WeakMap/WeakSet** |
|-------------|-------------|---------------------|
| **Ключи/значения** | Любые | Только объекты |
| **Ссылки** | Сильные | Слабые |
| **Итерация** | Да | Нет |
| **Размер** | `.size` | Нет |
| **Очистка** | `.clear()` | Нет |
| **GC** | Мешает | Не мешает |

**⚡ Практические правила:**

✅ **Используйте WeakMap когда:**
- Нужны приватные данные для объектов
- Кэширование результатов для объектов  
- Временные метаданные DOM элементов
- Не нужна итерация по коллекции

✅ **Используйте WeakSet когда:**
- Отслеживание состояния объектов
- Предотвращение повторной обработки
- Временная группировка объектов

❌ **НЕ используйте Weak* когда:**
- Нужна итерация по коллекции
- Нужно знать размер коллекции
- Ключи/значения — примитивы
- Нужна полная очистка `.clear()`

**Вывод:**
WeakMap/WeakSet — это "умные" коллекции, которые автоматически очищаются вместе с объектами. Идеальны для временных данных!
```

28. Чем Observable отличается от Promise?

**Ответ:**

🎯 **Promise** — это **одноразовая коробка** с одним подарком. **Observable** — это **конвейерная лента** с множеством подарков. Promise для разового результата, Observable для потоков данных!

```javascript
// 🔍 ОСНОВНАЯ РАЗНИЦА - одно значение vs поток значений

// 📦 PROMISE - одноразовый результат
const userPromise = fetch('/api/user')
  .then(response => response.json())
  .then(user => {
    console.log('Получили пользователя:', user);
    return user;
  });

// userPromise выдаст результат ОДИН раз и всё!

// 🌊 OBSERVABLE - поток значений (концептуально)
class SimpleObservable {
  constructor(subscriberFn) {
    this.subscriberFn = subscriberFn;
  }
  
  subscribe(observer) {
    return this.subscriberFn(observer);
  }
}

// Observable для кликов мыши
const clickStream = new SimpleObservable(observer => {
  const handleClick = (event) => {
    observer.next(event); // Много событий!
  };
  
  document.addEventListener('click', handleClick);
  
  // Возвращаем функцию отписки
  return () => {
    document.removeEventListener('click', handleClick);
  };
});

// Подписываемся на поток
const unsubscribe = clickStream.subscribe({
  next: (event) => console.log('Клик!', event.clientX, event.clientY),
  error: (error) => console.error('Ошибка:', error),
  complete: () => console.log('Поток завершен')
});

// Можем отписаться в любой момент
setTimeout(() => unsubscribe(), 5000); // Через 5 сек отписка
```

**🔍 ДЕТАЛЬНОЕ СРАВНЕНИЕ:**

```javascript
// 1️⃣ КОЛИЧЕСТВО ЗНАЧЕНИЙ

// Promise: 0 или 1 значение максимум
const getUserData = () => {
  return fetch('/api/user') // Один HTTP запрос
    .then(res => res.json()) // Один результат
    .then(user => user);     // Одно значение
};

getUserData().then(user => {
  console.log('Пользователь:', user); // Сработает ОДИН раз
});

// Observable: 0, 1 или бесконечно много значений
const mousePosition = new SimpleObservable(observer => {
  const handleMove = (event) => {
    observer.next({ x: event.clientX, y: event.clientY }); // Много значений!
  };
  
  document.addEventListener('mousemove', handleMove);
  return () => document.removeEventListener('mousemove', handleMove);
});

mousePosition.subscribe({
  next: (pos) => console.log(`Позиция: ${pos.x}, ${pos.y}`), // Сработает МНОГО раз
});

// 2️⃣ ЗАПУСК ВЫПОЛНЕНИЯ: EAGER vs LAZY

// Promise - EAGER (запускается сразу)
console.log('Создаем Promise...');
const eagerPromise = new Promise(resolve => {
  console.log('Promise запустился немедленно!'); // Выполнится сразу!
  setTimeout(() => resolve('Готово'), 1000);
});

console.log('Promise создан');
// Уже выполняется! Даже если .then() не вызвали

setTimeout(() => {
  eagerPromise.then(result => console.log('Результат:', result));
}, 2000); // Promise уже завершился к этому моменту

// Observable - LAZY (запускается при подписке)
console.log('Создаем Observable...');
const lazyObservable = new SimpleObservable(observer => {
  console.log('Observable запустился при подписке!'); // Выполнится только при subscribe!
  setTimeout(() => observer.next('Готово'), 1000);
});

console.log('Observable создан, но НЕ запущен');

setTimeout(() => {
  lazyObservable.subscribe({
    next: result => console.log('Результат:', result)
  });
}, 2000); // Только тогда запустится!

// 3️⃣ ОТМЕНА ВЫПОЛНЕНИЯ

// Promise - НЕЛЬЗЯ отменить
const longRunningPromise = new Promise(resolve => {
  const timeoutId = setTimeout(() => {
    resolve('Готово после долгого ожидания');
  }, 10000); // 10 секунд
  
  // Нет способа отменить этот timeout извне!
});

// longRunningPromise.cancel(); // ❌ Такого метода нет!

// Observable - МОЖНО отменить
const longRunningObservable = new SimpleObservable(observer => {
  const timeoutId = setTimeout(() => {
    observer.next('Готово после долгого ожидания');
  }, 10000);
  
  // Возвращаем функцию очистки
  return () => {
    clearTimeout(timeoutId);
    console.log('Observable отменен!');
  };
});

const subscription = longRunningObservable.subscribe({
  next: result => console.log(result)
});

// Отменяем через 2 секунды
setTimeout(() => {
  subscription(); // ✅ Отмена работает!
}, 2000);

// 4️⃣ ОПЕРАТОРЫ И ТРАНСФОРМАЦИИ

// Promise - базовые операторы
const promiseChain = fetch('/api/users')
  .then(response => response.json())          // Парсинг
  .then(users => users.filter(u => u.active)) // Фильтрация
  .then(active => active.map(u => u.name))    // Маппинг
  .catch(error => console.error(error));      // Обработка ошибок

// Observable - богатые операторы (в RxJS)
// const userStream = fromFetch('/api/users').pipe(
//   map(response => response.json()),
//   switchMap(users => from(users)),
//   filter(user => user.active),
//   map(user => user.name),
//   debounceTime(300),           // Дебаунс
//   distinctUntilChanged(),      // Только уникальные
//   retry(3),                    // Повтор при ошибке
//   catchError(err => of(null))  // Обработка ошибок
// );
```

**🔍 ПРАКТИЧЕСКИЕ ПРИМЕРЫ:**

```javascript
// 1️⃣ PROMISE - для разовых операций

// HTTP запросы
async function getUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);
    const user = await response.json();
    return user; // Один результат
  } catch (error) {
    throw new Error(`Не удалось получить пользователя: ${error.message}`);
  }
}

// Чтение файла
function readFile(path) {
  return new Promise((resolve, reject) => {
    // fs.readFile(path, (err, data) => {
    //   if (err) reject(err);
    //   else resolve(data); // Один результат
    // });
  });
}

// 2️⃣ OBSERVABLE - для потоков событий

// Поток веб-сокет сообщений  
const websocketStream = new SimpleObservable(observer => {
  const socket = new WebSocket('ws://localhost:8080');
  
  socket.onmessage = (event) => {
    observer.next(JSON.parse(event.data)); // Много сообщений
  };
  
  socket.onerror = (error) => {
    observer.error(error);
  };
  
  socket.onclose = () => {
    observer.complete();
  };
  
  return () => {
    socket.close(); // Отписка = закрытие соединения
  };
});

// Поток интервальных значений
const timerStream = new SimpleObservable(observer => {
  let count = 0;
  const intervalId = setInterval(() => {
    observer.next(count++); // Бесконечный поток чисел
  }, 1000);
  
  return () => {
    clearInterval(intervalId);
  };
});

// Комбинированный поток
const searchStream = new SimpleObservable(observer => {
  const input = document.querySelector('#search');
  
  const handleInput = (event) => {
    const query = event.target.value;
    if (query.length > 2) {
      observer.next(query); // Поток поисковых запросов
    }
  };
  
  input.addEventListener('input', handleInput);
  return () => input.removeEventListener('input', handleInput);
});

// 3️⃣ ОБЪЕДИНЕНИЕ PROMISE + OBSERVABLE

// Observable для HTTP запросов с отменой
function createCancellableRequest(url) {
  return new SimpleObservable(observer => {
    const controller = new AbortController();
    
    fetch(url, { signal: controller.signal })
      .then(response => response.json())
      .then(data => {
        observer.next(data);
        observer.complete();
      })
      .catch(error => {
        if (!controller.signal.aborted) {
          observer.error(error);
        }
      });
    
    // Функция отмены
    return () => {
      controller.abort();
    };
  });
}

// Использование:
const request = createCancellableRequest('/api/data');
const unsubscribe = request.subscribe({
  next: data => console.log('Данные:', data),
  error: err => console.error('Ошибка:', err),
  complete: () => console.log('Запрос завершен')
});

// Отменяем через 2 секунды
setTimeout(unsubscribe, 2000);
```

**🧠 Как запомнить?**

| Аспект | **Promise** | **Observable** |
|---------|-------------|----------------|
| **Аналогия** | 📦 Коробка | 🌊 Река |
| **Значений** | 1 раз | Много раз |
| **Запуск** | Сразу (Eager) | При подписке (Lazy) |
| **Отмена** | ❌ Нельзя | ✅ Можно |
| **Использование** | HTTP, файлы | События, стримы |

**⚡ Практические правила:**

✅ **Используйте Promise для:**
- HTTP запросов
- Чтения/записи файлов  
- Одноразовых асинхронных операций
- Простых цепочек обработки

✅ **Используйте Observable для:**
- Событий DOM (клики, ввод)
- WebSocket соединений
- Интервальных данных
- Сложных потоков с фильтрацией/трансформацией

**Вывод:**
Promise = одноразовое значение, Observable = поток значений. Promise проще, Observable мощнее для сложных сценариев!
```

29. Что такое Promise? Назовите порядок выполнения then и catch в цепочке.

```javascript
Promise.resolve(10)
  .then(e => console.log(e)) // ??
  .then(e => Promise.resolve(e))
  .then(console.log) // ??
  .then(e => {
    if (!e) {
      throw 'Error caught';
    }
  })
  .catch(e => {
    console.log(e); // ??
    return new Error('New error');
  })
  .then(e => {
    console.log(e.message); // ??
  })
  .catch(e => {
    console.log(e.message); // ??
  });
```

**Ответ:**
Promise — объект для работы с асинхронными операциями.

Разбор цепочки:
1. `console.log(e)` выводит: **10**, возвращает `undefined`
2. `e` равно `undefined`, возвращаем `Promise.resolve(undefined)`
3. `console.log` выводит: **undefined**, возвращает `undefined`
4. `!e` равно `!undefined` = `true`, бросаем ошибку `'Error caught'`
5. Catch ловит ошибку, выводит: **Error caught**, возвращает объект `Error('New error')`
6. Получаем объект Error, выводит: **New error**
7. Catch не выполняется (нет ошибки)

**Результат:**
```
10
undefined
Error caught
New error
```

30. Расскажите о последовательном и параллельном выполнении асинхронных функций. В чем разница между Promise.all ) и Promise.allSettled()?

**Ответ:**

```javascript
// ПОСЛЕДОВАТЕЛЬНОЕ выполнение
async function sequential() {
  const result1 = await fetch('/api/1'); // Ждём
  const result2 = await fetch('/api/2'); // Затем ждём
  const result3 = await fetch('/api/3'); // Затем ждём
  return [result1, result2, result3];
  // Время: t1 + t2 + t3
}

// ПАРАЛЛЕЛЬНОЕ выполнение
async function parallel() {
  const [result1, result2, result3] = await Promise.all([
    fetch('/api/1'), // Стартуют одновременно
    fetch('/api/2'),
    fetch('/api/3')
  ]);
  return [result1, result2, result3];
  // Время: max(t1, t2, t3)
}

// Promise.all - "всё или ничего"
try {
  const results = await Promise.all([
    Promise.resolve('OK'),
    Promise.reject('ERROR'), // Одна ошибка
    Promise.resolve('OK')
  ]);
  // Не выполнится
} catch (error) {
  console.log(error); // "ERROR" - весь Promise.all отклонён
}

// Promise.allSettled - получаем все результаты
const results = await Promise.allSettled([
  Promise.resolve('OK'),
  Promise.reject('ERROR'),
  Promise.resolve('OK')
]);

console.log(results);
// [
//   { status: 'fulfilled', value: 'OK' },
//   { status: 'rejected', reason: 'ERROR' },
//   { status: 'fulfilled', value: 'OK' }
// ]
```

31. **Дескрипторы свойств объектов**
```javascript
// Дескриптор - метаданные свойства
const obj = {};

Object.defineProperty(obj, 'name', {
  value: 'John',
  writable: false,    // Нельзя изменить
  enumerable: true,   // Видно в for...in
  configurable: false // Нельзя удалить/переконфигурировать
});

// Геттеры и сеттеры
Object.defineProperty(obj, 'fullName', {
  get() { return this.firstName + ' ' + this.lastName; },
  set(value) {
    [this.firstName, this.lastName] = value.split(' ');
  },
  enumerable: true,
  configurable: true
});
```

32. **Создание неизменяемых объектов**
```javascript
// 1. Object.freeze() - поверхностная заморозка
const frozen = Object.freeze({ a: 1, b: { c: 2 } });

// 2. Object.seal() - запрет добавления/удаления свойств
const sealed = Object.seal({ a: 1 });

// 3. Object.preventExtensions() - запрет добавления
const nonExtensible = Object.preventExtensions({ a: 1 });

// 4. Глубокая заморозка
function deepFreeze(obj) {
  Object.getOwnPropertyNames(obj).forEach(name => {
    const value = obj[name];
    if (value && typeof value === 'object') {
      deepFreeze(value);
    }
  });
  return Object.freeze(obj);
}
```

33. **Создание изменяемого свойства**
```javascript
const obj = {};

// Дескриптор данных
Object.defineProperty(obj, 'mutableProp', {
  value: 'initial',
  writable: true,     // Можно изменять
  enumerable: true,   // Видно при перечислении
  configurable: true  // Можно переконфигурировать
});

// Дескриптор доступа
let _value = 'initial';
Object.defineProperty(obj, 'accessorProp', {
  get() { return _value; },
  set(newValue) { _value = newValue; },
  enumerable: true,
  configurable: true
});
```

34. **Proxy - зачем нужен**
```javascript
// Proxy перехватывает операции с объектом
const target = { name: 'John', age: 30 };

const proxy = new Proxy(target, {
  get(target, prop) {
    console.log(`Получение ${prop}`);
    return target[prop];
  },
  
  set(target, prop, value) {
    console.log(`Установка ${prop} = ${value}`);
    target[prop] = value;
    return true;
  },
  
  has(target, prop) {
    console.log(`Проверка наличия ${prop}`);
    return prop in target;
  }
});

// Применения:
// 1. Валидация
// 2. Логирование
// 3. Виртуальные свойства
// 4. Негативная индексация массивов
```

35. **ArrayBuffer**
```javascript
const uint32Array = new Uint32Array();
console.log(Array.isArray(uint32Array)); // false

// ArrayBuffer - сырые бинарные данные
const buffer = new ArrayBuffer(16); // 16 байт

// Uint32Array - 32-битные беззнаковые целые
const uint32 = new Uint32Array(buffer); // 4 элемента по 4 байта

// Float32Array - 32-битные числа с плавающей точкой
const float32 = new Float32Array(buffer); // 4 элемента по 4 байта

// Различия:
// - Uint32Array: целые от 0 до 4,294,967,295
// - Float32Array: числа с плавающей точкой
```

36. **encodeURI vs encodeURIComponent**
```javascript
const url = "HTTPs://xyz.com/path<to>page.html";

console.log(encodeURI(url));
// "HTTPs://xyz.com/path%3Cto%3Epage.html"

console.log(encodeURIComponent(url));
// "HTTPs%3A%2F%2Fxyz.com%2Fpath%3Cto%3Epage.html"

console.log(encodeURI(url) === encodeURIComponent(url)); // false

// encodeURI - кодирует только недопустимые символы в URI
// encodeURIComponent - кодирует все специальные символы
```

37. **Генераторы и итераторы**
```javascript
// Генератор
function* numberGenerator() {
  let i = 0;
  while (i < 3) {
    yield i++;
  }
}

const gen = numberGenerator();
console.log(gen.next()); // { value: 0, done: false }
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: undefined, done: true }

// Итератор
const iterable = {
  [Symbol.iterator]() {
    let count = 0;
    return {
      next() {
        return count < 3 
          ? { value: count++, done: false }
          : { done: true };
      }
    };
  }
};
```

38. **Анализ кода генератора**
```javascript
function * fn(num) {
  for (let i = 0; i < num; i += 1) {
    yield console.log(i);
  }
}
const loop = fn(5);
loop.next(); // Выводит: 0, возвращает { value: undefined, done: false }
loop.next(); // Выводит: 1, возвращает { value: undefined, done: false }

// yield возвращает результат console.log(i), который равен undefined
```

37. **Symbol и системы счисления**
```javascript
// Symbol - уникальный примитивный тип
const sym1 = Symbol('description');
const sym2 = Symbol('description');
console.log(sym1 === sym2); // false - всегда уникальны

// Применения Symbol:
// 1. Уникальные ключи объектов
const PRIVATE_KEY = Symbol('private');
const obj = {
  [PRIVATE_KEY]: 'secret data'
};

// 2. Well-known symbols
const iterable = {
  [Symbol.iterator]() { /* итератор */ }
};

// Системы счисления:
const decimal = 255;
console.log(decimal.toString(16)); // "ff" (16-ричная)
console.log(decimal.toString(2));  // "11111111" (2-ичная)
console.log(decimal.toString(8));  // "377" (8-ричная)

// Обратно:
console.log(parseInt('ff', 16));   // 255
console.log(parseInt('11111111', 2)); // 255
console.log(parseInt('377', 8));   // 255
```

## Проверочные вопросы - JS Core

1. **Какой результат выполнения: `let a; console.log(a); let a = 5;`?**
   - ReferenceError из-за Temporal Dead Zone

2. **В чем разница между `'name' in obj` и `obj.hasOwnProperty('name')`?**
   - `in` проверяет всю цепочку прототипов, `hasOwnProperty` только собственные свойства

3. **Что выведет: `console.log(typeof null, null instanceof Object);`?**
   - `"object" false` - typeof null это историческая ошибка

4. **Какой алгоритм GC используется в современных браузерах?**
   - Mark-and-Sweep с поколенческой оптимизацией

5. **Чем WeakMap отличается от Map?**
   - WeakMap: ключи только объекты, не итерируемый, слабые ссылки на ключи

6. **Что произойдет: `console.log(1 + '2' + 3);`?**
   - Выведет "123" - строка имеет приоритет при конкатенации

## Функции

38. Объясните, что означает currying. Приведите пример использования на практике.

**Ответ:**
Каррирование (currying) — это преобразование функции с несколькими аргументами в последовательность функций, каждая из которых принимает один аргумент.

```javascript
// Обычная функция
function add(a, b, c) {
  return a + b + c;
}

// Каррированная версия
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

// Более современный синтаксис
const curriedAddArrow = a => b => c => a + b + c;

// Использование
const add5 = curriedAdd(5);
const add5and3 = add5(3);
const result = add5and3(2); // 10

// Или сразу
const result2 = curriedAdd(5)(3)(2); // 10

// Универсальная функция каррирования
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    } else {
      return function(...args2) {
        return curried.apply(this, args.concat(args2));
      };
    }
  };
}

// Пример использования
const multiply = (a, b, c) => a * b * c;
const curriedMultiply = curry(multiply);

console.log(curriedMultiply(2)(3)(4)); // 24
console.log(curriedMultiply(2, 3)(4)); // 24
console.log(curriedMultiply(2)(3, 4)); // 24

// Практические применения:

// 1. Конфигурирование функций
const log = curry((level, message, data) => {
  console.log(`[${level}] ${message}`, data);
});

const logError = log('ERROR');
const logInfo = log('INFO');

logError('Database connection failed', { host: 'localhost' });
logInfo('User logged in', { userId: 123 });

// 2. Функциональное программирование
const users = [
  { name: 'John', age: 25, active: true },
  { name: 'Jane', age: 30, active: false },
  { name: 'Bob', age: 35, active: true }
];

// Каррированные функции для фильтрации
const prop = curry((property, obj) => obj[property]);
const equals = curry((value, item) => item === value);
const filter = curry((predicate, array) => array.filter(predicate));

const getAge = prop('age');
const isActive = equals(true);
const filterBy = filter;

// Композиция функций
const getActiveUsers = filterBy(user => isActive(prop('active')(user)));
const activeUsers = getActiveUsers(users);

// 3. Частичное применение для API запросов
const request = curry((method, url, data) => {
  return fetch(url, {
    method,
    headers: { 'Content-Type': 'application/json' },
    body: data ? JSON.stringify(data) : undefined
  });
});

const get = request('GET');
const post = request('POST');
const put = request('PUT');

// Использование
const getUser = get('/api/users/');
const createUser = post('/api/users');

getUser('123').then(response => response.json());
createUser({ name: 'John', age: 25 });
```

39. Приведите пример функции с мемоизацией. Когда следует применять эту технику?

**Ответ:**
Мемоизация — это техника кэширования результатов выполнения функции для избежания повторных вычислений.

```javascript
// Простая мемоизация
function memoize(fn) {
  const cache = new Map();
  
  return function(...args) {
    const key = JSON.stringify(args);
    
    if (cache.has(key)) {
      console.log('Из кэша:', key);
      return cache.get(key);
    }
    
    console.log('Вычисляем:', key);
    const result = fn.apply(this, args);
    cache.set(key, result);
    
    return result;
  };
}

// Пример 1: Числа Фибоначчи
const fibonacci = memoize(function(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
});

console.log(fibonacci(40)); // Быстро благодаря мемоизации

// Пример 2: Факториал
const factorial = memoize(function(n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);
});

// Пример 3: Дорогие API запросы
const memoizedFetch = memoize(async function(url) {
  console.log('Делаем запрос к:', url);
  const response = await fetch(url);
  return response.json();
});

// Первый вызов сделает запрос, последующие вернут кэшированный результат
memoizedFetch('/api/users/1');
memoizedFetch('/api/users/1'); // Из кэша

// Мемоизация с ограниченным размером кэша (LRU)
function memoizeLRU(fn, maxSize = 100) {
  const cache = new Map();
  
  return function(...args) {
    const key = JSON.stringify(args);
    
    if (cache.has(key)) {
      // Перемещаем в конец (most recently used)
      const value = cache.get(key);
      cache.delete(key);
      cache.set(key, value);
      return value;
    }
    
    const result = fn.apply(this, args);
    
    // Если кэш переполнен, удаляем самый старый элемент
    if (cache.size >= maxSize) {
      const firstKey = cache.keys().next().value;
      cache.delete(firstKey);
    }
    
    cache.set(key, result);
    return result;
  };
}

// Мемоизация с временным истечением
function memoizeWithTTL(fn, ttl = 60000) { // TTL в миллисекундах
  const cache = new Map();
  
  return function(...args) {
    const key = JSON.stringify(args);
    const now = Date.now();
    
    if (cache.has(key)) {
      const cached = cache.get(key);
      if (now - cached.timestamp < ttl) {
        return cached.value;
      } else {
        cache.delete(key); // Удаляем устаревший
      }
    }
    
    const result = fn.apply(this, args);
    cache.set(key, {
      value: result,
      timestamp: now
    });
    
    return result;
  };
}

// Пример с объектными аргументами
function expensiveCalculation(config) {
  // Симуляция дорогой операции
  let result = 0;
  for (let i = 0; i < config.iterations; i++) {
    result += Math.sqrt(i) * config.multiplier;
  }
  return result;
}

const memoizedCalculation = memoize(expensiveCalculation);

const config = { iterations: 1000000, multiplier: 2 };
console.time('First call');
memoizedCalculation(config);
console.timeEnd('First call');

console.time('Second call');
memoizedCalculation(config); // Из кэша
console.timeEnd('Second call');

// WeakMap для мемоизации с объектными ключами
function memoizeWeakMap(fn) {
  const cache = new WeakMap();
  
  return function(obj, ...args) {
    if (typeof obj !== 'object') {
      throw new Error('First argument must be an object');
    }
    
    if (cache.has(obj)) {
      return cache.get(obj);
    }
    
    const result = fn.call(this, obj, ...args);
    cache.set(obj, result);
    
    return result;
  };
}

// Когда использовать мемоизацию:

// ✅ ХОРОШИЕ СЛУЧАИ:
// 1. Чистые функции (без побочных эффектов)
// 2. Дорогие вычисления
// 3. Функции с повторяющимися аргументами
// 4. Рекурсивные алгоритмы
// 5. API запросы с одинаковыми параметрами

// ❌ ПЛОХИЕ СЛУЧАИ:
// 1. Функции с побочными эффектами
// 2. Функции, которые всегда возвращают разные результаты
// 3. Функции с большим количеством уникальных аргументов
// 4. Память ограничена

// Пример класса с мемоизацией
class MathUtils {
  constructor() {
    this.primeCache = new Map();
  }
  
  isPrime = memoize((n) => {
    if (n <= 1) return false;
    if (n <= 3) return true;
    if (n % 2 === 0 || n % 3 === 0) return false;
    
    for (let i = 5; i * i <= n; i += 6) {
      if (n % i === 0 || n % (i + 2) === 0) {
        return false;
      }
    }
    return true;
  });
}

const mathUtils = new MathUtils();
console.log(mathUtils.isPrime(97)); // Вычисляется
console.log(mathUtils.isPrime(97)); // Из кэша
```

40. Что такое чейнинг функций? Напишите пример с использованием этого подхода.

**Ответ:**
Чейнинг (цепочка вызовов) — это паттерн, позволяющий вызывать несколько методов объекта подряд, когда каждый метод возвращает сам объект.

```javascript
// Простой пример чейнинга
class Calculator {
  constructor(value = 0) {
    this.value = value;
  }
  
  add(num) {
    this.value += num;
    return this; // Возвращаем this для чейнинга
  }
  
  subtract(num) {
    this.value -= num;
    return this;
  }
  
  multiply(num) {
    this.value *= num;
    return this;
  }
  
  divide(num) {
    if (num !== 0) {
      this.value /= num;
    }
    return this;
  }
  
  pow(num) {
    this.value = Math.pow(this.value, num);
    return this;
  }
  
  // Метод для получения результата
  result() {
    return this.value;
  }
  
  // Метод для сброса
  reset() {
    this.value = 0;
    return this;
  }
}

// Использование
const calc = new Calculator(10);
const result = calc
  .add(5)        // 15
  .multiply(2)   // 30
  .subtract(10)  // 20
  .divide(4)     // 5
  .pow(2)        // 25
  .result();     // 25

console.log(result); // 25

// Пример с строками
class StringBuilder {
  constructor() {
    this.parts = [];
  }
  
  append(str) {
    this.parts.push(str);
    return this;
  }
  
  prepend(str) {
    this.parts.unshift(str);
    return this;
  }
  
  insert(index, str) {
    this.parts.splice(index, 0, str);
    return this;
  }
  
  remove(index) {
    this.parts.splice(index, 1);
    return this;
  }
  
  replace(oldStr, newStr) {
    const index = this.parts.indexOf(oldStr);
    if (index !== -1) {
      this.parts[index] = newStr;
    }
    return this;
  }
  
  clear() {
    this.parts = [];
    return this;
  }
  
  toString() {
    return this.parts.join('');
  }
}

// Использование
const text = new StringBuilder()
  .append('Hello')
  .append(' ')
  .append('World')
  .prepend('Say: ')
  .insert(2, 'Beautiful ')
  .toString();

console.log(text); // "Say: Hello Beautiful World"

// Функциональный подход к чейнингу
class FunctionalChain {
  constructor(value) {
    this.value = value;
  }
  
  map(fn) {
    this.value = fn(this.value);
    return this;
  }
  
  filter(predicate) {
    if (Array.isArray(this.value)) {
      this.value = this.value.filter(predicate);
    }
    return this;
  }
  
  sort(compareFn) {
    if (Array.isArray(this.value)) {
      this.value = this.value.sort(compareFn);
    }
    return this;
  }
  
  tap(fn) {
    fn(this.value); // Выполняем функцию, но не изменяем значение
    return this;
  }
  
  get() {
    return this.value;
  }
}

// Создание удобной функции для начала цепочки
function chain(value) {
  return new FunctionalChain(value);
}

// Использование
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

const result2 = chain(numbers)
  .filter(x => x % 2 === 0)     // [2, 4, 6, 8, 10]
  .map(x => x * x)              // [4, 16, 36, 64, 100]
  .filter(x => x > 20)          // [36, 64, 100]
  .tap(console.log)             // Логируем промежуточный результат
  .sort((a, b) => b - a)        // [100, 64, 36]
  .get();

// Пример с HTTP запросами
class HttpClient {
  constructor() {
    this.config = {
      headers: {},
      params: {},
      timeout: 5000
    };
  }
  
  setHeader(key, value) {
    this.config.headers[key] = value;
    return this;
  }
  
  setParam(key, value) {
    this.config.params[key] = value;
    return this;
  }
  
  setTimeout(ms) {
    this.config.timeout = ms;
    return this;
  }
  
  auth(token) {
    return this.setHeader('Authorization', `Bearer ${token}`);
  }
  
  contentType(type) {
    return this.setHeader('Content-Type', type);
  }
  
  async get(url) {
    const queryString = new URLSearchParams(this.config.params);
    const fullUrl = `${url}?${queryString}`;
    
    return fetch(fullUrl, {
      method: 'GET',
      headers: this.config.headers,
      signal: AbortSignal.timeout(this.config.timeout)
    });
  }
  
  async post(url, data) {
    return fetch(url, {
      method: 'POST',
      headers: this.config.headers,
      body: JSON.stringify(data),
      signal: AbortSignal.timeout(this.config.timeout)
    });
  }
}

// Использование
const client = new HttpClient()
  .auth('your-token-here')
  .contentType('application/json')
  .setTimeout(10000)
  .setParam('page', 1)
  .setParam('limit', 10);

// client.get('/api/users').then(response => response.json());

// Async чейнинг
class AsyncChain {
  constructor(promise) {
    this.promise = promise || Promise.resolve();
  }
  
  then(onFulfilled, onRejected) {
    this.promise = this.promise.then(onFulfilled, onRejected);
    return this;
  }
  
  catch(onRejected) {
    this.promise = this.promise.catch(onRejected);
    return this;
  }
  
  delay(ms) {
    this.promise = this.promise.then(value => 
      new Promise(resolve => setTimeout(() => resolve(value), ms))
    );
    return this;
  }
  
  log(message) {
    this.promise = this.promise.then(value => {
      console.log(message, value);
      return value;
    });
    return this;
  }
  
  async execute() {
    return this.promise;
  }
}

// Использование
async function example() {
  const result = await new AsyncChain(Promise.resolve(1))
    .then(x => x + 1)
    .log('After increment:')
    .delay(1000)
    .then(x => x * 2)
    .log('After multiply:')
    .catch(err => console.error('Error:', err))
    .execute();
  
  console.log('Final result:', result);
}

// Чейнинг в стиле jQuery
function $(selector) {
  return new DOMChain(document.querySelectorAll(selector));
}

class DOMChain {
  constructor(elements) {
    this.elements = Array.from(elements);
  }
  
  addClass(className) {
    this.elements.forEach(el => el.classList.add(className));
    return this;
  }
  
  removeClass(className) {
    this.elements.forEach(el => el.classList.remove(className));
    return this;
  }
  
  text(content) {
    if (content === undefined) {
      return this.elements.map(el => el.textContent);
    }
    this.elements.forEach(el => el.textContent = content);
    return this;
  }
  
  hide() {
    this.elements.forEach(el => el.style.display = 'none');
    return this;
  }
  
  show() {
    this.elements.forEach(el => el.style.display = '');
    return this;
  }
  
  on(event, handler) {
    this.elements.forEach(el => el.addEventListener(event, handler));
    return this;
  }
}

// Использование (если бы были DOM элементы)
// $('.my-elements')
//   .addClass('active')
//   .text('Updated text')
//   .on('click', () => console.log('Clicked!'))
//   .show();
```

41. В чем разница между function и arrow function? Каким будет результат выполнения кода?

```javascript
const pluckDeep = key => obj => key.split('.').reduce((accum, key) => accum[key], obj)
const compose = (...fns) => res => fns.reduce((accum, next) => next(accum), res)
const unfold = (f, seed) => {
  const go = (f, seed, acc) => {
    const res = f(seed)
    return res ? go(f, res[1], acc.concat([res[0]])) : acc
  }
  return go(f, seed, [])
}
```

**Ответ:**
Основные различия между обычными функциями и стрелочными функциями:

```javascript
// 1. СИНТАКСИС
// Обычная функция
function regularFunction(a, b) {
  return a + b;
}

// Стрелочная функция
const arrowFunction = (a, b) => a + b;

// 2. THIS CONTEXT
const obj = {
  name: 'Test',
  
  // Обычная функция - this зависит от контекста вызова
  regularMethod: function() {
    console.log(this.name); // 'Test'
    
    setTimeout(function() {
      console.log(this.name); // undefined (this = window/global)
    }, 100);
  },
  
  // Стрелочная функция - this берётся из лексического окружения
  arrowMethod: () => {
    console.log(this.name); // undefined (this = window/global)
  },
  
  mixedMethod: function() {
    console.log(this.name); // 'Test'
    
    setTimeout(() => {
      console.log(this.name); // 'Test' (this сохраняется)
    }, 100);
  }
};

// 3. ARGUMENTS
function regularWithArgs() {
  console.log(arguments); // Псевдомассив аргументов
}

const arrowWithArgs = () => {
  // console.log(arguments); // ReferenceError!
};

// Для стрелочных функций используйте rest parameters
const arrowWithRest = (...args) => {
  console.log(args); // Настоящий массив
};

// 4. КОНСТРУКТОРЫ
function RegularConstructor(name) {
  this.name = name;
}

const instance1 = new RegularConstructor('Test'); // OK

const ArrowConstructor = (name) => {
  this.name = name;
};

// const instance2 = new ArrowConstructor('Test'); // TypeError!

// 5. HOISTING
console.log(hoistedFunction); // [Function: hoistedFunction]
// console.log(arrowVar); // ReferenceError!

function hoistedFunction() {
  return 'I am hoisted';
}

const arrowVar = () => 'I am not hoisted';

// 6. ПРОТОТИП
console.log(RegularConstructor.prototype); // {}
console.log(ArrowConstructor.prototype); // undefined

// АНАЛИЗ КОДА:

// pluckDeep - извлекает вложенные свойства объекта
const pluckDeep = key => obj => key.split('.').reduce((accum, key) => accum[key], obj);

const user = {
  profile: {
    personal: {
      name: 'John',
      age: 30
    }
  }
};

const getName = pluckDeep('profile.personal.name');
console.log(getName(user)); // 'John'

// compose - композиция функций (справа налево)
const compose = (...fns) => res => fns.reduce((accum, next) => next(accum), res);

const add1 = x => x + 1;
const multiply2 = x => x * 2;
const subtract3 = x => x - 3;

const composed = compose(subtract3, multiply2, add1);
console.log(composed(5)); // (5 + 1) * 2 - 3 = 9

// unfold - разворачивает значение в массив
const unfold = (f, seed) => {
  const go = (f, seed, acc) => {
    const res = f(seed);
    return res ? go(f, res[1], acc.concat([res[0]])) : acc;
  };
  return go(f, seed, []);
};

// Пример: генерация чисел от 1 до 5
const range = unfold(n => n <= 5 ? [n, n + 1] : null, 1);
console.log(range); // [1, 2, 3, 4, 5]

// ПРАКТИЧЕСКИЕ ПРИМЕРЫ РАЗЛИЧИЙ:

// Event handlers - this context
class Button {
  constructor(element) {
    this.element = element;
    this.clickCount = 0;
  }
  
  // Проблема с обычной функцией
  bindRegular() {
    this.element.addEventListener('click', function() {
      this.clickCount++; // Error: this не указывает на Button
    });
  }
  
  // Решение со стрелочной функцией
  bindArrow() {
    this.element.addEventListener('click', () => {
      this.clickCount++; // OK: this указывает на Button
    });
  }
  
  // Альтернативное решение с bind
  bindWithBind() {
    this.element.addEventListener('click', function() {
      this.clickCount++;
    }.bind(this));
  }
}

// Методы массивов
const numbers = [1, 2, 3, 4, 5];

// Стрелочные функции отлично подходят для коллбэков
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);

// Curry и partial application
const multiply = (a, b) => a * b;
const multiplyBy2 = multiply.bind(null, 2);

// Или со стрелочными функциями
const curriedMultiply = a => b => a * b;
const multiplyBy3 = curriedMultiply(3);

// Когда использовать что:

// ✅ СТРЕЛОЧНЫЕ ФУНКЦИИ:
// - Коллбэки в map, filter, reduce
// - Event handlers в классах
// - Короткие функции
// - Когда нужно сохранить this

// ✅ ОБЫЧНЫЕ ФУНКЦИИ:
// - Методы объектов
// - Конструкторы
// - Когда нужен dynamic this
// - Когда нужны arguments
// - Функции-генераторы
```

// ... existing code ...

## Проверочные вопросы - Функции

1. **Что такое каррирование и чем оно полезно?**
   - Преобразование функции с множественными аргументами в цепочку функций с одним аргументом; полезно для частичного применения

2. **Когда следует использовать мемоизацию?**
   - Для чистых функций с дорогими вычислениями и повторяющимися аргументами

3. **Что такое чейнинг функций?**
   - Паттерн вызова методов подряд, когда каждый метод возвращает объект (обычно `this`)

4. **Главное отличие стрелочных функций от обычных?**
   - Стрелочные функции не имеют собственного `this`, используют лексический `this`

5. **Можно ли использовать стрелочную функцию как конструктор?**
   - Нет, стрелочные функции нельзя вызывать с `new`

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