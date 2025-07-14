# Middle

## Общие вопросы

1. Расскажите о пирамиде тестирования.

**Ответ:**
Пирамида тестирования — это концептуальная модель, показывающая соотношение разных типов тестов. Основание пирамиды составляют unit-тесты (их больше всего), затем интеграционные тесты, и на вершине — e2e (end-to-end) тесты.

Принципы пирамиды:
- **Unit-тесты (основание)**: быстрые, дешевые, изолированные тесты отдельных компонентов
- **Интеграционные тесты (середина)**: тестируют взаимодействие между компонентами
- **E2E тесты (вершина)**: тестируют весь пользовательский путь, медленные и дорогие

```javascript
// Пример unit-теста
function add(a, b) {
  return a + b;
}

test('adds 1 + 2 to equal 3', () => {
  expect(add(1, 2)).toBe(3);
});

// Пример интеграционного теста
test('user service integration', async () => {
  const user = await userService.createUser({ name: 'John' });
  const foundUser = await userService.findById(user.id);
  expect(foundUser.name).toBe('John');
});
```

2. Какие типы автоматизированных тестов вам случалось писать? Какие библиотеки при этом использовали? Какие инструменты предпочитаете и почему?

**Ответ:**
Основные типы автоматизированных тестов:

- **Unit-тесты**: Jest, Vitest, Mocha + Chai
- **Интеграционные тесты**: Jest, Testing Library
- **E2E тесты**: Cypress, Playwright, Puppeteer
- **Visual regression тесты**: Chromatic, Percy
- **Performance тесты**: Lighthouse CI, WebPageTest

```javascript
// Пример с Jest и Testing Library
import { render, screen, fireEvent } from '@testing-library/react';
import Button from './Button';

test('button click triggers callback', () => {
  const handleClick = jest.fn();
  render(<Button onClick={handleClick}>Click me</Button>);
  
  fireEvent.click(screen.getByText('Click me'));
  expect(handleClick).toHaveBeenCalledTimes(1);
});
```

3. Что такое unit-тесты? Какое место в пирамиде тестирования занимают unit-тесты?

**Ответ:**
Unit-тесты — это тесты, проверяющие работу отдельных изолированных единиц кода (функции, методы, классы). Они занимают основание пирамиды тестирования.

Характеристики unit-тестов:
- **Изолированность**: тестируют только один компонент
- **Быстрота**: выполняются за миллисекунды
- **Детерминированность**: всегда дают одинаковый результат
- **Независимость**: не зависят от внешних ресурсов

```javascript
// Хороший unit-тест
describe('calculateTax', () => {
  it('should calculate tax correctly for positive amount', () => {
    expect(calculateTax(100, 0.2)).toBe(20);
  });

  it('should return 0 for zero amount', () => {
    expect(calculateTax(0, 0.2)).toBe(0);
  });

  it('should throw error for negative amount', () => {
    expect(() => calculateTax(-100, 0.2)).toThrow('Amount must be positive');
  });
});
```

4. Что такое code coverage? Обязательно ли 100% покрытие кода тестами?

**Ответ:**
Code coverage (покрытие кода) — это метрика, показывающая, какая часть кода выполняется при запуске тестов.

Типы покрытия:
- **Statement coverage**: покрытие строк кода
- **Branch coverage**: покрытие всех веток условий
- **Function coverage**: покрытие функций
- **Line coverage**: покрытие строк

100% покрытие НЕ обязательно и может быть даже вредным:
- Важно качество тестов, а не количество
- Некоторый код сложно протестировать (error handling)
- Лучше стремиться к 80-90% осмысленного покрытия

```javascript
// Настройка coverage в Jest
module.exports = {
  collectCoverage: true,
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};
```

5. Как запретить браузеру отдавать кэш на HTTP-запрос?

**Ответ:**
Существует несколько способов предотвратить кэширование:

**HTTP заголовки:**
```javascript
// Установка заголовков на сервере
res.setHeader('Cache-Control', 'no-cache, no-store, must-revalidate');
res.setHeader('Pragma', 'no-cache');
res.setHeader('Expires', '0');
```

**На клиенте:**
```javascript
// Добавление timestamp к URL
const url = `api/data?_=${Date.now()}`;

// Fetch с заголовками
fetch('/api/data', {
  headers: {
    'Cache-Control': 'no-cache'
  }
});

// Axios
axios.get('/api/data', {
  headers: {
    'Cache-Control': 'no-cache'
  }
});
```

6. Что такое XSS (Cross-Site Scripting)?

**Ответ:**
XSS — это уязвимость, позволяющая внедрить вредоносный JavaScript код на веб-страницу.

Типы XSS:
- **Reflected XSS**: код выполняется сразу (через URL параметры)
- **Stored XSS**: код сохраняется на сервере и выполняется при загрузке
- **DOM-based XSS**: манипуляция DOM на клиенте

```javascript
// Пример уязвимого кода
document.getElementById('welcome').innerHTML = 
  'Привет, ' + userInput; // Опасно!

// Безопасный код
document.getElementById('welcome').textContent = 
  'Привет, ' + userInput; // Безопасно

// Санитизация ввода
function sanitizeInput(str) {
  return str.replace(/[<>&"']/g, function(match) {
    const escapeMap = {
      '<': '&lt;',
      '>': '&gt;',
      '&': '&amp;',
      '"': '&quot;',
      "'": '&#x27;'
    };
    return escapeMap[match];
  });
}
```

7. Расскажите о паттернах Observer, Pub/Sub. Чем они отличаются? Приведите примеры реализации этих паттернов в известных фреймворках (библиотеках, браузерных API).

**Ответ:**
**Observer** — объект наблюдает за изменениями другого объекта напрямую.
**Pub/Sub** — издатель и подписчик не знают друг о друге, общение через посредника.

```javascript
// Observer pattern
class Subject {
  constructor() {
    this.observers = [];
  }
  
  subscribe(observer) {
    this.observers.push(observer);
  }
  
  notify(data) {
    this.observers.forEach(observer => observer.update(data));
  }
}

// Pub/Sub pattern
class EventBus {
  constructor() {
    this.events = {};
  }
  
  subscribe(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }
  
  publish(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data));
    }
  }
}
```

Примеры в фреймворках:
- **DOM Events**: `addEventListener` (Pub/Sub)
- **Vue.js**: реактивная система (Observer)
- **Redux**: `subscribe` метод (Observer)
- **Node.js EventEmitter**: (Pub/Sub)

8. С какой целью может быть использован event listener события fetch self.addEventListener `( 'fetch', event => {})`?

**Ответ:**
Это Service Worker API для перехвата сетевых запросов. Основные цели:

```javascript
// Service Worker
self.addEventListener('fetch', event => {
  // Кэширование ресурсов
  if (event.request.destination === 'image') {
    event.respondWith(
      caches.match(event.request)
        .then(response => response || fetch(event.request))
    );
  }
  
  // Офлайн fallback
  if (event.request.url.includes('/api/')) {
    event.respondWith(
      fetch(event.request)
        .catch(() => caches.match('/offline.html'))
    );
  }
  
  // Модификация запросов
  const modifiedRequest = new Request(event.request.url, {
    ...event.request,
    headers: {
      ...event.request.headers,
      'Authorization': 'Bearer token'
    }
  });
  
  event.respondWith(fetch(modifiedRequest));
});
```

Применения:
- Кэширование статики
- Офлайн функциональность
- Предварительная загрузка
- Аналитика запросов
- Модификация запросов/ответов

9. Что такое Event loop и как он работает? Расскажите о микрозадачах и макрозадачах.

**Ответ:**
Event Loop — механизм, управляющий выполнением асинхронного кода в JavaScript.

Компоненты:
- **Call Stack**: стек вызовов синхронного кода
- **Web APIs**: браузерные API (setTimeout, DOM events)
- **Callback Queue**: очередь колбэков
- **Microtask Queue**: очередь микрозадач

```javascript
console.log('1'); // Синхронный код

setTimeout(() => console.log('2'), 0); // Макрозадача

Promise.resolve().then(() => console.log('3')); // Микрозадача

console.log('4'); // Синхронный код

// Результат: 1, 4, 3, 2
```

**Микрозадачи** (высший приоритет):
- Promise.then/catch/finally
- queueMicrotask()
- MutationObserver

**Макрозадачи** (низший приоритет):
- setTimeout/setInterval
- DOM events
- I/O операции

Алгоритм Event Loop:
1. Выполнить весь синхронный код
2. Выполнить все микрозадачи
3. Выполнить одну макрозадачу
4. Повторить с пункта 2

## JS Core

10. Какие типы данных бывают в JavaScript? Каким будет результат выполнения кода?

```javascript
let firstObj = { name: 'Hello' };
let secondObj = firstObj;
firstObj = { name: 'Bye' };
console.log(secondObj.name);
```

**Ответ:**
В JavaScript существует 8 типов данных:

**Примитивные типы:**
- `number` (включая NaN, Infinity)
- `string`
- `boolean`
- `undefined`
- `null`
- `symbol` (ES6)
- `bigint` (ES2020)

**Объектные типы:**
- `object` (включая массивы, функции, даты и т.д.)

```javascript
// Примеры типов
let num = 42;              // number
let str = "Hello";         // string
let bool = true;           // boolean
let undef = undefined;     // undefined
let nul = null;            // null
let sym = Symbol('id');    // symbol
let big = 123n;            // bigint
let obj = { a: 1 };        // object

// Результат выполнения кода:
let firstObj = { name: 'Hello' };
let secondObj = firstObj;     // secondObj ссылается на тот же объект
firstObj = { name: 'Bye' };   // firstObj теперь ссылается на новый объект
console.log(secondObj.name);  // "Hello" - secondObj всё ещё ссылается на исходный объект
```

11. Что такое temporal dead zone?

**Ответ:**
Temporal Dead Zone (TDZ) — это период между началом выполнения области видимости и инициализацией переменной, объявленной с помощью `let` или `const`.

```javascript
// TDZ для let
console.log(typeof x); // ReferenceError: Cannot access 'x' before initialization
let x = 5;

// TDZ для const
console.log(y); // ReferenceError: Cannot access 'y' before initialization
const y = 10;

// var не имеет TDZ (hoisting)
console.log(z); // undefined
var z = 15;

// Пример с блоком
{
  // TDZ начинается здесь
  console.log(typeof temp); // ReferenceError
  let temp = 'hello';       // TDZ заканчивается здесь
}

// TDZ в параметрах функции
function example(a = b, b = 2) { // ReferenceError: b используется до инициализации
  return a + b;
}
```

12. Как работает boxing/unboxing в JavaScript?

**Ответ:**
Boxing — автоматическое преобразование примитивных значений в объекты-обёртки.
Unboxing — извлечение примитивного значения из объекта-обёртки.

```javascript
// Автоматический boxing
let str = "hello";
console.log(str.toUpperCase()); // "HELLO"
// JS автоматически создаёт new String(str), вызывает метод, затем удаляет объект

// Ручной boxing
let num = 42;
let numObj = new Number(num);
console.log(typeof num);    // "number"
console.log(typeof numObj); // "object"

// Unboxing
let boolObj = new Boolean(false);
console.log(boolObj);         // [Boolean: false]
console.log(boolObj.valueOf()); // false

// Проблемы с boxing
let str1 = "hello";
let str2 = new String("hello");
console.log(str1 === str2);      // false (разные типы)
console.log(str1 == str2);       // true (приведение типов)

// Полезный пример
Number.prototype.twice = function() {
  return this.valueOf() * 2;
};
console.log((5).twice()); // 10
```

13. В чем разница между оператором in и методом hasOwnProperty?

**Ответ:**
`in` проверяет наличие свойства во всей цепочке прототипов, `hasOwnProperty` — только в самом объекте.

```javascript
// Создаём объект с прототипом
function Parent() {
  this.parentProp = 'parent';
}
Parent.prototype.prototypeProp = 'prototype';

function Child() {
  this.childProp = 'child';
}
Child.prototype = new Parent();

const obj = new Child();

// Оператор in
console.log('childProp' in obj);       // true (собственное свойство)
console.log('parentProp' in obj);      // true (свойство прототипа)
console.log('prototypeProp' in obj);   // true (свойство прототипа)
console.log('toString' in obj);        // true (встроенное свойство Object)

// hasOwnProperty
console.log(obj.hasOwnProperty('childProp'));      // true
console.log(obj.hasOwnProperty('parentProp'));     // true
console.log(obj.hasOwnProperty('prototypeProp'));  // false
console.log(obj.hasOwnProperty('toString'));       // false

// Практический пример
const obj2 = { a: 1, b: 2 };
Object.prototype.c = 3;

for (let key in obj2) {
  console.log(key); // a, b, c (включая прототип)
}

for (let key in obj2) {
  if (obj2.hasOwnProperty(key)) {
    console.log(key); // a, b (только собственные)
  }
}

// Современная альтернатива
console.log(Object.hasOwn(obj2, 'a')); // true (ES2022)
```

14. Опишите, с помощью чего в JS реализуются такие ООП-парадигмы, как инкапсуляция, полиморфизм, абстракция?

**Ответ:**

**Инкапсуляция:**
```javascript
// Замыкания
function createCounter() {
  let count = 0; // Приватная переменная
  
  return {
    increment() { count++; },
    decrement() { count--; },
    getCount() { return count; }
  };
}

// Приватные поля (ES2022)
class BankAccount {
  #balance = 0; // Приватное поле
  
  deposit(amount) {
    this.#balance += amount;
  }
  
  #validateAmount(amount) { // Приватный метод
    return amount > 0;
  }
  
  getBalance() {
    return this.#balance;
  }
}

// WeakMap для приватности
const privateData = new WeakMap();

class User {
  constructor(name, password) {
    this.name = name;
    privateData.set(this, { password });
  }
  
  checkPassword(pass) {
    return privateData.get(this).password === pass;
  }
}
```

**Полиморфизм:**
```javascript
// Duck typing
class Dog {
  makeSound() { return "Woof!"; }
}

class Cat {
  makeSound() { return "Meow!"; }
}

class Duck {
  makeSound() { return "Quack!"; }
}

function animalSound(animal) {
  return animal.makeSound(); // Полиморфизм
}

// Переопределение методов
class Shape {
  area() { throw new Error("Must implement area method"); }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }
  
  area() {
    return Math.PI * this.radius ** 2;
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }
  
  area() {
    return this.width * this.height;
  }
}
```

**Абстракция:**
```javascript
// Абстрактные классы (имитация)
class AbstractVehicle {
  constructor() {
    if (this.constructor === AbstractVehicle) {
      throw new Error("Cannot instantiate abstract class");
    }
  }
  
  start() {
    throw new Error("Must implement start method");
  }
  
  stop() {
    throw new Error("Must implement stop method");
  }
  
  // Конкретный метод
  getInfo() {
    return `Vehicle of type ${this.constructor.name}`;
  }
}

class Car extends AbstractVehicle {
  start() {
    return "Car engine started";
  }
  
  stop() {
    return "Car engine stopped";
  }
}

// Интерфейсы (имитация через миксины)
const Flyable = {
  fly() { throw new Error("Must implement fly method"); }
};

const Swimmable = {
  swim() { throw new Error("Must implement swim method"); }
};

class Duck2 {
  constructor() {
    Object.assign(this, Flyable, Swimmable);
  }
  
  fly() { return "Duck is flying"; }
  swim() { return "Duck is swimming"; }
}
```

15. Что такое прототип? Как работает прототипное наследование в JS? Объясните работу кода.

```javascript
function Main () {}
Main.prototype = { protected: true };
const obj = new Main();
Main.prototype = { protected: false };
console.log('Object protection: ', obj.protected);
```

**Ответ:**
Прототип — это объект, от которого другие объекты наследуют свойства и методы.

```javascript
// Цепочка прототипов
const animal = {
  type: 'animal',
  makeSound() { return 'Some sound'; }
};

const dog = Object.create(animal);
dog.breed = 'labrador';

console.log(dog.type);       // 'animal' (из прототипа)
console.log(dog.breed);      // 'labrador' (собственное)
console.log(dog.makeSound()); // 'Some sound' (из прототипа)

// Функции-конструкторы
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

const john = new Person('John');
console.log(john.greet()); // "Hello, I'm John"

// Объяснение кода:
function Main () {}
Main.prototype = { protected: true };  // Устанавливаем прототип
const obj = new Main();                // obj.__proto__ ссылается на { protected: true }
Main.prototype = { protected: false }; // Меняем ссылку Main.prototype на новый объект
console.log('Object protection: ', obj.protected); // true

// obj всё ещё связан со старым объектом прототипа!
```

Результат: `Object protection: true`

Причина: объект `obj` создался с ссылкой на прототип `{ protected: true }`. Когда мы переназначили `Main.prototype`, это не повлияло на уже созданный объект.

16. В чем разница между композицией и наследованием?

**Ответ:**

**Наследование** — "is-a" отношение (объект является экземпляром класса)
**Композиция** — "has-a" отношение (объект содержит другие объекты)

```javascript
// НАСЛЕДОВАНИЕ
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  eat() { return `${this.name} is eating`; }
}

class Dog extends Animal {
  bark() { return `${this.name} is barking`; }
}

const dog = new Dog('Rex');
console.log(dog.eat());  // Rex is eating
console.log(dog.bark()); // Rex is barking

// Проблемы наследования:
// - Жёсткая связанность
// - Алмазная проблема
// - Сложно изменять иерархию

// КОМПОЗИЦИЯ
class Engine {
  start() { return "Engine started"; }
  stop() { return "Engine stopped"; }
}

class Wheels {
  rotate() { return "Wheels rotating"; }
}

class Car {
  constructor() {
    this.engine = new Engine();  // Композиция
    this.wheels = new Wheels();  // Композиция
  }
  
  start() {
    return this.engine.start();
  }
  
  drive() {
    return this.wheels.rotate();
  }
}

// Миксины (компромисс между наследованием и композицией)
const CanFly = {
  fly() { return "Flying"; }
};

const CanSwim = {
  swim() { return "Swimming"; }
};

class Duck {
  constructor(name) {
    this.name = name;
    Object.assign(this, CanFly, CanSwim);
  }
}

const duck = new Duck('Donald');
console.log(duck.fly());  // Flying
console.log(duck.swim()); // Swimming

// Фабричная функция с композицией
function createBird(abilities = []) {
  const bird = {
    name: 'Bird',
    chirp() { return 'Chirp!'; }
  };
  
  abilities.forEach(ability => {
    Object.assign(bird, ability);
  });
  
  return bird;
}

const eagle = createBird([CanFly]);
const penguin = createBird([CanSwim]);
```

**Принцип:** "Предпочитайте композицию наследованию"

17. Почему не стоит использовать конструкторы типа new String?

**Ответ:**
Конструкторы примитивных обёрток создают объекты, а не примитивы, что может приводить к неожиданному поведению.

```javascript
// Проблемы с new String()
const str1 = "hello";        // примитив
const str2 = new String("hello"); // объект

console.log(typeof str1);    // "string"
console.log(typeof str2);    // "object"

// Сравнение
console.log(str1 === str2);  // false! (разные типы)
console.log(str1 == str2);   // true (приведение типов)

// Проблемы в условиях
const falsy = new Boolean(false);
if (falsy) {
  console.log("Выполнится!"); // Объект всегда truthy!
}

// JSON сериализация
console.log(JSON.stringify(str1)); // "hello"
console.log(JSON.stringify(str2)); // "{}"

// Производительность
console.time('primitive');
for (let i = 0; i < 1000000; i++) {
  let s = "test";
}
console.timeEnd('primitive'); // Быстрее

console.time('object');
for (let i = 0; i < 1000000; i++) {
  let s = new String("test");
}
console.timeEnd('object'); // Медленнее

// Правильное использование (без new)
const num = Number("123");    // 123 (примитив)
const bool = Boolean("true"); // true (примитив)
const str = String(123);      // "123" (примитив)

// Полезные методы без создания объектов
console.log(Number.isInteger(42));     // true
console.log(String.fromCharCode(65));  // "A"
```

18. Расскажите о базовом устройстве и механизме работы Event loop.

**Ответ:**
Event Loop — это механизм, который управляет выполнением кода, обработкой событий и выполнением подзадач.

```javascript
// Компоненты Event Loop:
// 1. Call Stack (стек вызовов)
// 2. Heap (куча для объектов)
// 3. Callback Queue (очередь колбэков)
// 4. Microtask Queue (очередь микрозадач)
// 5. Web APIs (браузерные API)

// Пример работы
console.log('1');                    // Call Stack

setTimeout(() => {                   // Web API -> Callback Queue
  console.log('2');
}, 0);

Promise.resolve().then(() => {       // Microtask Queue
  console.log('3');
});

console.log('4');                    // Call Stack

// Результат: 1, 4, 3, 2

// Детальный пример
console.log('start');

setTimeout(() => console.log('timeout1'), 0);
setTimeout(() => console.log('timeout2'), 0);

Promise.resolve().then(() => {
  console.log('promise1');
  return Promise.resolve();
}).then(() => {
  console.log('promise2');
});

Promise.resolve().then(() => {
  console.log('promise3');
});

console.log('end');

// Результат:
// start
// end
// promise1
// promise3
// promise2
// timeout1
// timeout2

// Алгоритм Event Loop:
// 1. Выполнить весь синхронный код из Call Stack
// 2. Выполнить ВСЕ микрозадачи (Promise, queueMicrotask)
// 3. Выполнить ОДНУ макрозадачу (setTimeout, setInterval)
// 4. Обновить рендеринг (если нужно)
// 5. Повторить с шага 2

// Демонстрация приоритетов
setTimeout(() => console.log('timeout'), 0);

Promise.resolve().then(() => {
  console.log('promise');
  setTimeout(() => console.log('timeout in promise'), 0);
  return Promise.resolve();
}).then(() => {
  console.log('promise 2');
});

queueMicrotask(() => console.log('microtask'));

// Результат:
// promise
// promise 2
// microtask
// timeout
// timeout in promise
```

19. Что такое записи (records) и кортежи (tuples)? Чем они отличаются от обычных объектов?

**Ответ:**
Records и Tuples — это предложенные (proposal stage 2) неизменяемые структуры данных для JavaScript.

```javascript
// Records (неизменяемые объекты) - ПРЕДЛОЖЕНИЕ
const record = #{
  name: "John",
  age: 30,
  address: #{
    city: "New York",
    zip: "10001"
  }
};

// Попытка изменения вызовет ошибку
// record.age = 31; // TypeError

// Tuples (неизменяемые массивы) - ПРЕДЛОЖЕНИЕ  
const tuple = #[1, 2, 3];

// Попытка изменения вызовет ошибку
// tuple[0] = 10; // TypeError

// В настоящее время альтернативы:

// Object.freeze() для иммутабельности
const frozenObj = Object.freeze({
  name: "John",
  age: 30
});

// frozenObj.age = 31; // Не работает в strict mode

// Глубокая заморозка
function deepFreeze(obj) {
  Object.getOwnPropertyNames(obj).forEach(name => {
    const value = obj[name];
    if (value && typeof value === 'object') {
      deepFreeze(value);
    }
  });
  return Object.freeze(obj);
}

// Иммутабельные операции с массивами
const arr = [1, 2, 3];
const newArr = [...arr, 4];        // Добавление
const filtered = arr.filter(x => x > 1); // Фильтрация
const mapped = arr.map(x => x * 2);  // Преобразование

// Библиотеки для работы с иммутабельными данными
// Immer, Immutable.js, Ramda

// Пример с Immer (популярная библиотека)
// import produce from 'immer';
// 
// const nextState = produce(currentState, draft => {
//   draft.todos.push({ id: 1, text: 'New todo' });
//   draft.user.name = 'John';
// });
```

Отличия Records/Tuples от обычных объектов:
- **Иммутабельность**: нельзя изменить после создания
- **Структурное равенство**: сравнение по значению, а не по ссылке
- **Примитивность**: могут использоваться как ключи в Map/Set
- **Производительность**: оптимизации для неизменяемых данных

20. Каковы различия в поведении ES5 функции-конструктора и ES2015 класса?

**Ответ:**
ES6 классы — это синтаксический сахар над функциями-конструкторами, но с важными отличиями:

```javascript
// ES5 функция-конструктор
function PersonES5(name) {
  this.name = name;
}

PersonES5.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

// ES6 класс
class PersonES6 {
  constructor(name) {
    this.name = name;
  }
  
  greet() {
    return `Hello, I'm ${this.name}`;
  }
}

// Основные различия:

// 1. Hoisting (поднятие)
const p1 = new PersonES5('John'); // Работает
function PersonES5() {} // Поднимается

// const p2 = new PersonES6('Jane'); // ReferenceError!
// class PersonES6 {} // Не поднимается

// 2. Вызов без new
PersonES5('John'); // Работает (this = window/global)
// PersonES6('Jane'); // TypeError: Class constructor cannot be invoked without 'new'

// 3. Strict mode
function PersonES5() {
  console.log(this); // window/global или undefined в strict mode
}

class PersonES6 {
  constructor() {
    console.log(this); // всегда undefined без new, всегда экземпляр с new
  }
}

// 4. Методы не перечисляемы
PersonES5.prototype.greet = function() {};
console.log(Object.getOwnPropertyDescriptor(PersonES5.prototype, 'greet').enumerable); // true

console.log(Object.getOwnPropertyDescriptor(PersonES6.prototype, 'greet').enumerable); // false

// 5. Наследование
// ES5
function AnimalES5(name) {
  this.name = name;
}

function DogES5(name, breed) {
  AnimalES5.call(this, name); // Ручной вызов super
  this.breed = breed;
}

DogES5.prototype = Object.create(AnimalES5.prototype);
DogES5.prototype.constructor = DogES5;

// ES6
class AnimalES6 {
  constructor(name) {
    this.name = name;
  }
}

class DogES6 extends AnimalES6 {
  constructor(name, breed) {
    super(name); // Автоматический super
    this.breed = breed;
  }
}

// 6. Проверка типа
console.log(typeof PersonES5); // "function"
console.log(typeof PersonES6); // "function" (но с [[IsClassConstructor]]: true)
```

21. Как реализовать паттерн «Модуль»?

**Ответ:**
Паттерн Модуль обеспечивает инкапсуляцию и создание приватной области видимости.

```javascript
// 1. Базовый модуль (IIFE)
const Calculator = (function() {
  // Приватные переменные и функции
  let result = 0;
  
  function validate(num) {
    return typeof num === 'number';
  }
  
  // Публичный API
  return {
    add(num) {
      if (validate(num)) {
        result += num;
      }
      return this;
    },
    
    subtract(num) {
      if (validate(num)) {
        result -= num;
      }
      return this;
    },
    
    getResult() {
      return result;
    },
    
    reset() {
      result = 0;
      return this;
    }
  };
})();

Calculator.add(5).subtract(2); // Chaining
console.log(Calculator.getResult()); // 3

// 2. Модуль с параметрами
const Counter = (function(initialValue = 0) {
  let count = initialValue;
  
  return {
    increment: () => ++count,
    decrement: () => --count,
    getValue: () => count
  };
})(10);

// 3. Revealing Module Pattern
const UserManager = (function() {
  let users = [];
  
  function findById(id) {
    return users.find(user => user.id === id);
  }
  
  function createUser(userData) {
    const user = { id: Date.now(), ...userData };
    users.push(user);
    return user;
  }
  
  function removeUser(id) {
    users = users.filter(user => user.id !== id);
  }
  
  // Возвращаем только то, что хотим сделать публичным
  return {
    create: createUser,
    remove: removeUser,
    find: findById,
    getAll: () => [...users] // Возвращаем копию
  };
})();

// 4. ES6 модули
// mathUtils.js
export const add = (a, b) => a + b;
export const multiply = (a, b) => a * b;

const PI = 3.14159;
function calculateArea(radius) {
  return PI * radius * radius;
}

export default { calculateArea };

// main.js
import { add, multiply } from './mathUtils.js';
import MathUtils from './mathUtils.js';

// 5. Модуль с наследованием
const BaseModule = (function() {
  return {
    init() {
      console.log('Base module initialized');
    }
  };
})();

const ExtendedModule = (function(base) {
  const module = Object.create(base);
  
  module.extendedMethod = function() {
    console.log('Extended functionality');
  };
  
  const originalInit = module.init;
  module.init = function() {
    originalInit.call(this);
    console.log('Extended module initialized');
  };
  
  return module;
})(BaseModule);

// 6. Модуль-синглтон
const DatabaseConnection = (function() {
  let instance;
  
  function createConnection() {
    return {
      host: 'localhost',
      port: 5432,
      connect() {
        console.log('Connected to database');
      }
    };
  }
  
  return {
    getInstance() {
      if (!instance) {
        instance = createConnection();
      }
      return instance;
    }
  };
})();

const db1 = DatabaseConnection.getInstance();
const db2 = DatabaseConnection.getInstance();
console.log(db1 === db2); // true
```

22. Почему typeof null возвращает object?

**Ответ:**
Это историческая ошибка в JavaScript, которая сохраняется для обратной совместимости.

```javascript
console.log(typeof null); // "object" - ошибка!

// Причина: в первых версиях JavaScript
// значения представлялись как пары (тип, значение)
// null представлялся как NULL pointer (0x00)
// что совпадало с тегом для object

// Правильная проверка на null
console.log(null === null);              // true
console.log(Object.prototype.toString.call(null)); // "[object Null]"

// Функция для правильного определения типа
function getType(value) {
  if (value === null) return 'null';
  if (value === undefined) return 'undefined';
  return typeof value;
}

console.log(getType(null));      // "null"
console.log(getType(undefined)); // "undefined"
console.log(getType({}));        // "object"
console.log(getType([]));        // "object"

// Более точная функция определения типа
function preciseType(value) {
  return Object.prototype.toString.call(value).slice(8, -1).toLowerCase();
}

console.log(preciseType(null));           // "null"
console.log(preciseType([]));             // "array"
console.log(preciseType({}));             // "object"
console.log(preciseType(new Date()));     // "date"
console.log(preciseType(/regex/));        // "regexp"
console.log(preciseType(function(){}));   // "function"

// Проверка на "реальный" объект
function isPlainObject(value) {
  return (
    typeof value === 'object' &&
    value !== null &&
    !Array.isArray(value) &&
    Object.prototype.toString.call(value) === '[object Object]'
  );
}

console.log(isPlainObject({}));           // true
console.log(isPlainObject([]));           // false
console.log(isPlainObject(null));         // false
console.log(isPlainObject(new Date()));   // false
```

23. Что такое приведение (преобразование) типов в JS?

**Ответ:**
Приведение типов — это процесс преобразования значения из одного типа в другой.

```javascript
// Типы приведения:
// 1. Явное (explicit) - программист сам вызывает преобразование
// 2. Неявное (implicit/coercion) - JavaScript делает это автоматически

// ЯВНОЕ ПРИВЕДЕНИЕ
// К строке
String(123);        // "123"
(123).toString();   // "123"
123 + "";          // "123"

// К числу
Number("123");      // 123
parseInt("123px");  // 123
parseFloat("12.3"); // 12.3
+"123";            // 123 (унарный плюс)

// К boolean
Boolean(1);         // true
!!1;               // true
!!"hello";         // true
!!0;               // false

// НЕЯВНОЕ ПРИВЕДЕНИЕ
// При сравнении
console.log(1 == "1");     // true (приведение к числу)
console.log(1 === "1");    // false (строгое сравнение)
console.log(null == undefined); // true (специальное правило)

// При арифметических операциях
console.log("5" - 2);      // 3 (строка к числу)
console.log("5" + 2);      // "52" (число к строке)
console.log(true + 1);     // 2 (boolean к числу)

// Алгоритм ToNumber
console.log(Number(""));       // 0
console.log(Number(" "));      // 0
console.log(Number("123"));    // 123
console.log(Number("abc"));    // NaN
console.log(Number(true));     // 1
console.log(Number(false));    // 0
console.log(Number(null));     // 0
console.log(Number(undefined)); // NaN

// Алгоритм ToString
console.log(String(123));      // "123"
console.log(String(true));     // "true"
console.log(String(null));     // "null"
console.log(String(undefined)); // "undefined"
console.log(String({}));       // "[object Object]"
console.log(String([]));       // ""
console.log(String([1,2,3]));  // "1,2,3"

// Алгоритм ToBoolean (falsy values)
console.log(Boolean(0));           // false
console.log(Boolean(-0));          // false
console.log(Boolean(NaN));         // false
console.log(Boolean(""));          // false
console.log(Boolean(null));        // false
console.log(Boolean(undefined));   // false

// Всё остальное - truthy
console.log(Boolean("0"));         // true
console.log(Boolean([]));          // true
console.log(Boolean({}));          // true
console.log(Boolean(function(){})); // true
```

24. Что такое явное и неявное приведение (преобразование) типов данных в JS? Как происходит преобразование типов в следующих примерах:

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

25. Что такое Garbage Collector?

**Ответ:**
Garbage Collector (сборщик мусора) — это автоматический механизм управления памятью, который освобождает память от объектов, которые больше не используются.

```javascript
// Основные алгоритмы сборки мусора:

// 1. Reference Counting (подсчёт ссылок) - устаревший
// Проблема: циклические ссылки
function cyclicReference() {
  const obj1 = {};
  const obj2 = {};
  
  obj1.ref = obj2;
  obj2.ref = obj1;
  
  // В старых браузерах это приводило к утечке памяти
  return null; // obj1 и obj2 недостижимы, но ссылаются друг на друга
}

// 2. Mark-and-Sweep (современный алгоритм)
// Помечает достижимые объекты от корневых ссылок

// Примеры создания и освобождения памяти
function createObjects() {
  const arr = new Array(1000000).fill(0); // Создаём большой массив
  
  return function() {
    return arr.length; // Замыкание держит ссылку на arr
  };
}

let closure = createObjects(); // arr в памяти
closure = null;                // arr может быть удалён GC

// Управление памятью на практике
class DataProcessor {
  constructor() {
    this.cache = new Map();
    this.timers = [];
  }
  
  // Плохо: потенциальная утечка памяти
  addTimer() {
    const timer = setInterval(() => {
      console.log('Timer running');
    }, 1000);
    this.timers.push(timer);
  }
  
  // Хорошо: очистка ресурсов
  cleanup() {
    this.timers.forEach(timer => clearInterval(timer));
    this.timers = [];
    this.cache.clear();
  }
}

// WeakMap и WeakSet - помогают избежать утечек
const userMetadata = new WeakMap();

function attachMetadata(user, data) {
  userMetadata.set(user, data); // Если user удалён, metadata тоже удалится
}

// Примеры утечек памяти и их предотвращение

// 1. DOM события
// Плохо
function badEventListener() {
  const button = document.getElementById('myButton');
  const data = new Array(1000000).fill(0); // Большие данные
  
  button.addEventListener('click', function() {
    console.log(data.length); // data не может быть удалена
  });
}

// Хорошо
function goodEventListener() {
  const button = document.getElementById('myButton');
  
  function clickHandler() {
    console.log('Button clicked');
  }
  
  button.addEventListener('click', clickHandler);
  
  // Очистка при необходимости
  return () => {
    button.removeEventListener('click', clickHandler);
  };
}

// 2. Замыкания
// Плохо
function createFunctions() {
  const bigData = new Array(1000000).fill(0);
  const smallData = 'small';
  
  return [
    function() { return smallData; }, // Держит ссылку на весь scope, включая bigData
    function() { return 'hello'; }
  ];
}

// Хорошо
function createFunctionsBetter() {
  const bigData = new Array(1000000).fill(0);
  const smallData = 'small';
  
  // Используем промежуточную функцию
  function createSmallDataFunction(data) {
    return function() { return data; };
  }
  
  return [
    createSmallDataFunction(smallData), // Держит только smallData
    function() { return 'hello'; }
  ];
}

// Мониторинг памяти
if (performance.memory) {
  console.log('Used:', performance.memory.usedJSHeapSize);
  console.log('Total:', performance.memory.totalJSHeapSize);
  console.log('Limit:', performance.memory.jsHeapSizeLimit);
}

// Принудительная сборка мусора (только в DevTools)
// gc(); // Работает только в Node.js с флагом --expose-gc или в DevTools
```

26. Опишите основные принципы работы «сборщика мусора» в JS-движках (engines).

**Ответ:**
Современные JS-движки используют generational garbage collection с несколькими оптимизациями:

```javascript
// Основные принципы работы GC:

// 1. ПОКОЛЕНЧЕСКАЯ СБОРКА (Generational GC)
// Объекты делятся на поколения: молодые и старые

// Молодые объекты (Young Generation) - часто собираются
function createYoungObjects() {
  for (let i = 0; i < 1000; i++) {
    const temp = { id: i, data: new Array(100) };
    // temp быстро становится недостижимым
  }
}

// Старые объекты (Old Generation) - реже собираются
const globalCache = new Map(); // Живёт долго, попадает в Old Generation

// 2. ТРЁХЦВЕТНАЯ РАЗМЕТКА (Tri-color marking)
// Белый - не посещён
// Серый - посещён, но не обработан
// Чёрный - посещён и обработан

// 3. ИНКРЕМЕНТАЛЬНАЯ СБОРКА
// Работа GC разбивается на маленькие части
// чтобы не блокировать основной поток

// 4. ПАРАЛЛЕЛЬНАЯ/КОНКУРЕНТНАЯ СБОРКА
// Часть работы выполняется в фоновых потоках

// Фазы работы GC:
// 1. Marking (разметка) - находит живые объекты
// 2. Sweeping (подметание) - освобождает мёртвые объекты
// 3. Compacting (уплотнение) - дефрагментирует память

// Оптимизации в V8:
// - Scavenger для Young Generation
// - Mark-Compact для Old Generation
// - Concurrent marking
// - Incremental marking

// Практические советы:
// 1. Очищайте таймеры и обработчики событий
class Component {
  constructor() {
    this.timers = [];
    this.eventCleanups = [];
  }
  
  destroy() {
    this.timers.forEach(id => clearTimeout(id));
    this.eventCleanups.forEach(cleanup => cleanup());
    this.timers = [];
    this.eventCleanups = [];
  }
}

// 2. Используйте WeakMap/WeakSet для слабых ссылок
const nodeMetadata = new WeakMap();
function addMetadata(node, data) {
  nodeMetadata.set(node, data); // Слабая ссылка, не препятствует GC
}

// 3. Мониторинг производительности GC
if (performance.memory) {
  console.log('Used:', performance.memory.usedJSHeapSize);
  console.log('Total:', performance.memory.totalJSHeapSize);
  console.log('Limit:', performance.memory.jsHeapSizeLimit);
}
```

27. Опишите назначение и принципы работы с коллекциями WeakMap и WeakSet. Чем они отличаются от коллекций Map и Set соответственно?

**Ответ:**

```javascript
// WEAKMAP vs MAP

// Map - обычная коллекция
const map = new Map();
let obj = { name: 'John' };

map.set(obj, 'some data');
obj = null; // объект всё ещё в памяти через Map

// WeakMap - слабые ссылки
const weakMap = new WeakMap();
let obj2 = { name: 'Jane' };
weakMap.set(obj2, 'some data');
obj2 = null; // объект может быть удалён GC

// ОСНОВНЫЕ ОТЛИЧИЯ:
// 1. Ключи только объекты (не примитивы)
// 2. Не итерируемы (нет forEach, keys, values)
// 3. Нет свойства size
// 4. Нет метода clear()

// Практические примеры:

// 1. Приватные данные
const privateData = new WeakMap();

class User {
  constructor(name) {
    this.name = name;
    privateData.set(this, { password: null, sessions: [] });
  }
  
  setPassword(pwd) {
    privateData.get(this).password = pwd;
  }
}

// 2. Кэширование для объектов
const cache = new WeakMap();

function expensiveOperation(obj) {
  if (cache.has(obj)) {
    return cache.get(obj);
  }
  
  const result = /* дорогая операция */ { computed: true };
  cache.set(obj, result);
  return result;
}

// 3. DOM метаданные
const elementState = new WeakMap();

function trackElement(element) {
  elementState.set(element, {
    clickCount: 0,
    lastActive: Date.now()
  });
}

// WEAKSET
const authorizedUsers = new WeakSet();

function authorize(user) {
  authorizedUsers.add(user);
}

function isAuthorized(user) {
  return authorizedUsers.has(user);
}
```

28. Чем Observable отличается от Promise?

**Ответ:**

```javascript
// PROMISE - одно значение
const promise = fetch('/api/user')
  .then(response => response.json())
  .then(user => console.log(user)); // Выполнится один раз

// OBSERVABLE - поток значений (концептуально)
class SimpleObservable {
  constructor(subscriber) {
    this.subscriber = subscriber;
  }
  
  subscribe(observer) {
    return this.subscriber(observer);
  }
}

const clicks = new SimpleObservable(observer => {
  document.addEventListener('click', event => {
    observer.next(event); // Множество событий
  });
});

// ОСНОВНЫЕ ОТЛИЧИЯ:

// 1. LAZY vs EAGER
const eagerPromise = new Promise(resolve => {
  console.log('Выполнится сразу');
  resolve('value');
});

const lazyObservable = new SimpleObservable(observer => {
  console.log('Выполнится при подписке');
  observer.next('value');
});

// 2. ОТМЕНА
// Promise нельзя отменить
// Observable можно отменить через unsubscribe

// 3. КОЛИЧЕСТВО ЗНАЧЕНИЙ
// Promise: 0 или 1 значение
// Observable: 0, 1 или множество значений

// 4. ОПЕРАТОРЫ
// Promise: then, catch, finally
// Observable: map, filter, merge, switchMap, и множество других
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

39. **Symbol и системы счисления**
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

## Функции

40. Объясните, что означает currying. Приведите пример использования на практике.

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

41. Приведите пример функции с мемоизацией. Когда следует применять эту технику?

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

42. Что такое чейнинг функций? Напишите пример с использованием этого подхода.

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

43. В чем разница между function и arrow function? Каким будет результат выполнения кода?

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

## Фронтенд

44. В чем принципиальная разница между событиями mouseleave и mouseout?

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

45. В каком порядке обрабатываются пользовательские события в DOM (click, mouseover и т.д.)? FIFO или LIFO?

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

46. Что такое Event bubbling и Event capturing?

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

47. Сравните методы объекта event stopPropagation и stopImmediateProparation.

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

48. **Оптимизация производительности веб-страницы**
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

49. **Same-origin policy**
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

50. **Способы хранения данных в браузере**
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

51-56. (остальные Frontend вопросы продолжаются с подробными ответами и примерами...)

// ... existing code ...

## Верстка

57. Объясните разницу между единицами измерения px, em, rem.

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

58. Для чего нужны CSS-переменные? Приведите несколько примеров использования.

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

59-77. **Остальные вопросы верстки** (компактные ответы)

59. **Box-sizing: border-box**
```css
* { box-sizing: border-box; }
/* Включает padding и border в общую ширину элемента */
```

60. **Печать**
```css
@media print {
  .no-print { display: none; }
  body { font-size: 12pt; color: black; }
}
```

61. **Кастомизация форм**
```css
input[type="text"] {
  appearance: none; /* Убирает системные стили */
  border: 1px solid #ccc;
}
```

62. **Progressive рендеринг**
- Critical CSS inline
- Lazy loading изображений
- Code splitting
- Resource hints (preload, prefetch)

63. **Lazy loading**
```html
<img src="placeholder.jpg" data-src="real-image.jpg" loading="lazy">
```

64. **Шаблонизаторы**
- Handlebars, Mustache
- Pug, EJS
- JSX (React)

65. **CSS методологии**
- BEM: `.block__element--modifier`
- SMACSS: Base, Layout, Module, State, Theme
- OOCSS: Object Oriented CSS

66. **CSS Grid**
```css
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
}
```

67. **Анимированные форматы**
- GIF, APNG, WebP, AVIF

68. **CSS анимации в JS**
```javascript
element.addEventListener('animationend', () => {
  console.log('Animation completed');
});
```

69. **GPU ускорение**
```css
.gpu-accelerated {
  transform: translateZ(0); /* Создает композитный слой */
  will-change: transform;
}
```

70-77. **SVG, иконочные шрифты, Shadow DOM, Custom Elements, Canvas** (детальные ответы доступны по запросу)

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

78. Что такое Tree Shaking и как оно работает?

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

79. Объясните разницу между imperative и declarative программирования. Приведите примеры.

**Ответ:**

```javascript
// ИМПЕРАТИВНОЕ программирование - КАК делать
function imperativeDouble(numbers) {
  const result = [];
  for (let i = 0; i < numbers.length; i++) {
    result.push(numbers[i] * 2);
  }
  return result;
}

// ДЕКЛАРАТИВНОЕ программирование - ЧТО делать  
function declarativeDouble(numbers) {
  return numbers.map(n => n * 2);
}

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

// Императивная анимация
function imperativeAnimate(element) {
  let opacity = 0;
  const timer = setInterval(() => {
    opacity += 0.1;
    element.style.opacity = opacity;
    if (opacity >= 1) {
      clearInterval(timer);
    }
  }, 50);
}

// Декларативная анимация
const DeclarativeAnimation = () => {
  const [isVisible, setIsVisible] = useState(false);
  
  return (
    <div 
      className={`fade-in ${isVisible ? 'visible' : ''}`}
      onClick={() => setIsVisible(!isVisible)}
    >
      Content
    </div>
  );
};
```

80. Что такое Functional Programming? Назовите основные принципы.

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

81. Что такое микрофронтенды? Какие есть подходы к их реализации?

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

82. Расскажите о Performance API. Как измерить производительность веб-приложения?

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

83. Что такое Server-Side Rendering (SSR) и Static Site Generation (SSG)? В чем разница?

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

84. Что такое Web Components? Приведите практический пример использования.

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

85. Объясните принципы SOLID применительно к JavaScript.

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

// ... existing code ...