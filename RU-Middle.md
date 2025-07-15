# Middle

## Общие вопросы

1. 

2. 

3. 

4. 

5. 

6. 

7. 

8. 

9. 

## JS Core

10.

11. 
12. 

13. 

14. 

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

🎯 **Promise** — это **"обещание" получить результат** в будущем. Как заказ в ресторане: вы получаете номерок и ждете, пока приготовят блюдо. Promise может **выполниться** (resolve) или **провалиться** (reject).

```javascript
// 🔍 ОСНОВЫ PROMISE

// Promise может быть в 3 состояниях:
// 1. PENDING (ожидание) - еще выполняется
// 2. FULFILLED (выполнен) - успешно завершен
// 3. REJECTED (отклонен) - завершен с ошибкой

const simplePromise = new Promise((resolve, reject) => {
  const success = Math.random() > 0.5;
  
  setTimeout(() => {
    if (success) {
      resolve('Успех!'); // Переходит в FULFILLED
    } else {
      reject('Ошибка!'); // Переходит в REJECTED
    }
  }, 1000);
});

// Обработка результата:
simplePromise
  .then(result => console.log('Получили:', result))   // Для успеха
  .catch(error => console.error('Ошибка:', error));   // Для провала
```

**🔍 ПОШАГОВЫЙ РАЗБОР ЦЕПОЧКИ:**

```javascript
// Анализируем каждый шаг цепочки:

Promise.resolve(10)                    // Начинаем с resolved Promise со значением 10
  
  .then(e => console.log(e))           // ШАГ 1️⃣
  // e = 10
  // Выводит: 10
  // Возвращает: undefined (результат console.log)
  
  .then(e => Promise.resolve(e))       // ШАГ 2️⃣  
  // e = undefined (результат предыдущего then)
  // Возвращает: Promise.resolve(undefined)
  // Promise автоматически распаковывается в undefined
  
  .then(console.log)                   // ШАГ 3️⃣
  // e = undefined
  // Выводит: undefined
  // Возвращает: undefined (результат console.log)
  
  .then(e => {                         // ШАГ 4️⃣
    if (!e) {                          // !undefined = true
      throw 'Error caught';            // Бросаем ошибку!
    }
    // Этот код не выполнится
  })
  
  .catch(e => {                        // ШАГ 5️⃣ - ловим ошибку
    console.log(e);                    // e = 'Error caught'
    // Выводит: Error caught
    return new Error('New error');     // Возвращаем новый Error объект
  })
  
  .then(e => {                         // ШАГ 6️⃣
    console.log(e.message);            // e = Error объект
    // Выводит: New error (свойство message объекта Error)
    // Возвращает: undefined
  })
  
  .catch(e => {                        // ШАГ 7️⃣
    console.log(e.message);            // Не выполнится!
    // Предыдущий then не бросил ошибку
  });

// 🎯 ФИНАЛЬНЫЙ РЕЗУЛЬТАТ В КОНСОЛИ:
// 10
// undefined  
// Error caught
// New error
```

**🔍 КЛЮЧЕВЫЕ ПРАВИЛА PROMISE ЦЕПОЧЕК:**

```javascript
// 1️⃣ ПРАВИЛО ВОЗВРАТА: что возвращает then, передается в следующий

Promise.resolve('start')
  .then(value => {
    console.log(value); // "start"
    return 'middle';    // Возвращаем строку
  })
  .then(value => {
    console.log(value); // "middle" ✅
    return Promise.resolve('end'); // Возвращаем Promise
  })
  .then(value => {
    console.log(value); // "end" ✅ (Promise автоматически разворачивается)
  });

// 2️⃣ ПРАВИЛО ОШИБОК: catch ловит любые ошибки выше по цепочке

Promise.resolve('ok')
  .then(() => {
    throw new Error('Упс!'); // Бросаем ошибку
  })
  .then(() => {
    console.log('Не выполнится'); // Пропускается из-за ошибки
  })
  .catch(error => {
    console.log('Поймали:', error.message); // "Поймали: Упс!"
    return 'recovered'; // Восстанавливаемся
  })
  .then(value => {
    console.log('Продолжаем:', value); // "Продолжаем: recovered" ✅
  });

// 3️⃣ ПРАВИЛО ПРОЗРАЧНОСТИ: undefined пропускается как есть

Promise.resolve(42)
  .then(value => {
    console.log(value); // 42
    // Не возвращаем ничего = возвращаем undefined
  })
  .then(value => {
    console.log(value); // undefined ✅
    return 'next';
  })
  .then(value => {
    console.log(value); // "next" ✅
  });

// 4️⃣ ПРАВИЛО CATCH: обработанная ошибка становится успешным значением

Promise.reject('error')
  .catch(error => {
    console.log('Обработали:', error); // "Обработали: error"
    return 'fixed'; // Возвращаем успешное значение
  })
  .then(value => {
    console.log('Теперь успех:', value); // "Теперь успех: fixed" ✅
  })
  .catch(() => {
    console.log('Не выполнится'); // Нет новых ошибок
  });
```

**🔍 ПРАКТИЧЕСКИЕ ПРИМЕНЕНИЯ:**

```javascript
// 🚀 АСИНХРОННЫЕ ОПЕРАЦИИ

// Загрузка данных пользователя
function fetchUserProfile(userId) {
  return fetch(`/api/users/${userId}`)
    .then(response => {
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }
      return response.json();
    })
    .then(userData => {
      console.log('Пользователь загружен:', userData.name);
      return userData;
    })
    .catch(error => {
      console.error('Ошибка загрузки пользователя:', error.message);
      return { name: 'Гость', id: null }; // Fallback данные
    });
}

// Цепочка зависимых запросов
function loadUserWithPosts(userId) {
  return fetchUserProfile(userId)
    .then(user => {
      if (!user.id) {
        throw new Error('Пользователь не найден');
      }
      
      // Загружаем посты пользователя
      return fetch(`/api/users/${user.id}/posts`)
        .then(response => response.json())
        .then(posts => ({
          user: user,
          posts: posts
        }));
    })
    .catch(error => {
      console.error('Ошибка:', error.message);
      return { user: null, posts: [] };
    });
}

// 🎯 СОЗДАНИЕ СОБСТВЕННЫХ PROMISE

function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}

function countdown(seconds) {
  return new Promise((resolve, reject) => {
    if (seconds < 0) {
      reject(new Error('Время не может быть отрицательным'));
      return;
    }
    
    const interval = setInterval(() => {
      console.log(`Осталось: ${seconds}`);
      seconds--;
      
      if (seconds < 0) {
        clearInterval(interval);
        resolve('Время вышло!');
      }
    }, 1000);
  });
}

// Использование:
countdown(3)
  .then(message => console.log(message))
  .catch(error => console.error(error.message));
```

**🧠 Как запомнить Promise цепочки?**

1. **then** = "если успех, то делай..."
2. **catch** = "если ошибка, то делай..."  
3. **Возврат** из then/catch → следующий then
4. **Ошибка** в then → ближайший catch
5. **catch** может "починить" цепочку

**⚡ Частые ошибки:**

```javascript
// ❌ Забыли return
promise.then(data => {
  processData(data); // Результат теряется!
});

// ✅ Правильно
promise.then(data => {
  return processData(data); // Передаем результат дальше
});

// ❌ Не обработали ошибки
promise.then(data => console.log(data)); // Unhandled rejection!

// ✅ Правильно
promise
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

**Вывод:**
Promise — это способ работы с асинхронным кодом через цепочки then/catch. Каждый then получает результат предыдущего, catch ловит ошибки!

30. Расскажите о последовательном и параллельном выполнении асинхронных функций. В чем разница между Promise.all и Promise.allSettled?

**Ответ:**

🎯 **Последовательное** выполнение = **очередь в банке** (один за другим). **Параллельное** = **несколько касс** (все одновременно). **Promise.all** = "всё или ничего", **Promise.allSettled** = "получаем все результаты".

```javascript
// 🔍 ПОСЛЕДОВАТЕЛЬНОЕ vs ПАРАЛЛЕЛЬНОЕ ВЫПОЛНЕНИЕ

// ⏳ ПОСЛЕДОВАТЕЛЬНОЕ - одно за другим (медленно)
async function sequentialExecution() {
  console.time('Sequential');
  
  const user = await fetch('/api/user');         // 1 секунда
  const posts = await fetch('/api/posts');       // + 1 секунда 
  const comments = await fetch('/api/comments');  // + 1 секунда
  
  console.timeEnd('Sequential'); // ~3 секунды
  
  return {
    user: await user.json(),
    posts: await posts.json(), 
    comments: await comments.json()
  };
}

// ⚡ ПАРАЛЛЕЛЬНОЕ - все одновременно (быстро)
async function parallelExecution() {
  console.time('Parallel');
  
  // Запускаем ВСЕ запросы одновременно
  const [user, posts, comments] = await Promise.all([
    fetch('/api/user'),       // Стартуют
    fetch('/api/posts'),      // одновременно
    fetch('/api/comments')    // в параллель
  ]);
  
  console.timeEnd('Parallel'); // ~1 секунда (самый медленный)
  
  return {
    user: await user.json(),
    posts: await posts.json(),
    comments: await comments.json()
  };
}

// 🎯 Разница во времени:
// Последовательно: 1с + 1с + 1с = 3 секунды
// Параллельно: max(1с, 1с, 1с) = 1 секунда
```

**🔍 PROMISE.ALL - "ВСЁ ИЛИ НИЧЕГО":**

```javascript
// 🎯 Promise.all = строгий учитель
// Если хоть один ученик не сдал экзамен - весь класс переписывает

async function strictApproach() {
  try {
    const results = await Promise.all([
      fetch('/api/users'),        // ✅ Успех
      fetch('/api/invalid-url'),  // ❌ 404 ошибка 
      fetch('/api/posts')         // ✅ Успех (но не важно)
    ]);
    
    console.log('Все запросы успешны:', results);
    // Этот код НЕ выполнится!
    
  } catch (error) {
    console.error('Хотя бы один запрос упал:', error);
    // Сюда попадем из-за одной ошибки
    // Теряем ВСЕ результаты, даже успешные!
  }
}

// 🔍 Детальный пример Promise.all
async function processUsers() {
  const userIds = [1, 2, 999, 4]; // 999 - несуществующий пользователь
  
  try {
    const users = await Promise.all(
      userIds.map(id => fetch(`/api/users/${id}`))
    );
    
    console.log('Загружены все пользователи:', users.length);
    // НЕ выполнится из-за пользователя 999
    
  } catch (error) {
    console.error('Не удалось загрузить пользователей:', error.message);
    // Потеряли даже пользователей 1, 2, 4 которые загрузились успешно!
  }
}

// ⚡ Promise.all идеален когда ВСЕ запросы критически важны
async function criticalData() {
  try {
    const [userProfile, userSettings, userPermissions] = await Promise.all([
      fetchUserProfile(),
      fetchUserSettings(), 
      fetchUserPermissions()
    ]);
    
    // Если нет хотя бы одной части - приложение не может работать
    return {
      profile: userProfile,
      settings: userSettings,
      permissions: userPermissions
    };
    
  } catch (error) {
    throw new Error('Не удалось загрузить критически важные данные');
  }
}
```

**🔍 PROMISE.ALLSETTLED - "ПОЛУЧАЕМ ВСЁ":**

```javascript
// 🎯 Promise.allSettled = понимающий учитель  
// Проверяет все работы и говорит результат по каждой

async function flexibleApproach() {
  const results = await Promise.allSettled([
    fetch('/api/users'),        // ✅ Успех
    fetch('/api/invalid-url'),  // ❌ 404 ошибка
    fetch('/api/posts')         // ✅ Успех
  ]);
  
  console.log('Результаты всех запросов:');
  results.forEach((result, index) => {
    if (result.status === 'fulfilled') {
      console.log(`Запрос ${index}: Успех!`, result.value.status);
    } else {
      console.log(`Запрос ${index}: Ошибка!`, result.reason.message);
    }
  });
  
  // Работаем с тем, что получилось
  const successfulResults = results
    .filter(result => result.status === 'fulfilled')
    .map(result => result.value);
    
  console.log('Успешных запросов:', successfulResults.length);
}

// 🔍 Практический пример с обработкой результатов
async function loadDashboardData() {
  const requests = [
    { name: 'user', promise: fetch('/api/user') },
    { name: 'notifications', promise: fetch('/api/notifications') },
    { name: 'analytics', promise: fetch('/api/analytics') },
    { name: 'settings', promise: fetch('/api/settings') }
  ];
  
  const results = await Promise.allSettled(
    requests.map(req => req.promise)
  );
  
  const dashboard = {};
  const errors = [];
  
  results.forEach((result, index) => {
    const requestName = requests[index].name;
    
    if (result.status === 'fulfilled') {
      dashboard[requestName] = result.value;
      console.log(`✅ ${requestName}: загружено`);
    } else {
      dashboard[requestName] = null;
      errors.push(`❌ ${requestName}: ${result.reason.message}`);
    }
  });
  
  if (errors.length > 0) {
    console.warn('Часть данных не загрузилась:', errors);
  }
  
  // Показываем dashboard с тем, что удалось загрузить
  return dashboard;
}

// 🎯 Promise.allSettled для загрузки изображений
async function loadImages(imageUrls) {
  const results = await Promise.allSettled(
    imageUrls.map(url => 
      new Promise((resolve, reject) => {
        const img = new Image();
        img.onload = () => resolve({ url, img, status: 'loaded' });
        img.onerror = () => reject(new Error(`Failed to load ${url}`));
        img.src = url;
      })
    )
  );
  
  const loadedImages = [];
  const failedImages = [];
  
  results.forEach((result, index) => {
    if (result.status === 'fulfilled') {
      loadedImages.push(result.value);
    } else {
      failedImages.push({
        url: imageUrls[index],
        error: result.reason.message
      });
    }
  });
  
  console.log(`Загружено изображений: ${loadedImages.length}/${imageUrls.length}`);
  
  if (failedImages.length > 0) {
    console.warn('Не удалось загрузить:', failedImages);
  }
  
  return { loaded: loadedImages, failed: failedImages };
}
```

**🔍 СРАВНЕНИЕ И ВЫБОР СТРАТЕГИИ:**

```javascript
// 📊 Сравнительная таблица

const comparisons = {
  'Promise.all': {
    behavior: 'Всё или ничего',
    onError: 'Отменяет все',
    result: 'Массив значений ИЛИ ошибка',
    useCase: 'Критически важные данные'
  },
  'Promise.allSettled': {
    behavior: 'Получает всё',
    onError: 'Продолжает выполнение',
    result: 'Массив {status, value/reason}',
    useCase: 'Опциональные данные'
  }
};

// 🎯 КОГДА ИСПОЛЬЗОВАТЬ КАКОЙ:

// ✅ Promise.all - когда ВСЕ результаты критично важны
async function loginUser(email, password) {
  try {
    const [user, permissions, settings] = await Promise.all([
      authenticateUser(email, password),    // ОБЯЗАТЕЛЬНО
      getUserPermissions(email),            // ОБЯЗАТЕЛЬНО  
      getUserSettings(email)                // ОБЯЗАТЕЛЬНО
    ]);
    
    // Без любой из этих частей - вход невозможен
    return { user, permissions, settings };
    
  } catch (error) {
    throw new Error('Ошибка входа в систему');
  }
}

// ✅ Promise.allSettled - когда часть данных может отсутствовать
async function loadUserDashboard(userId) {
  const results = await Promise.allSettled([
    fetchUserProfile(userId),       // Основное (важно)
    fetchUserNotifications(userId), // Опционально
    fetchUserStats(userId),         // Опционально
    fetchUserRecommendations(userId) // Опционально
  ]);
  
  const [profile, notifications, stats, recommendations] = results;
  
  return {
    profile: profile.status === 'fulfilled' ? profile.value : null,
    notifications: notifications.status === 'fulfilled' ? notifications.value : [],
    stats: stats.status === 'fulfilled' ? stats.value : null,
    recommendations: recommendations.status === 'fulfilled' ? recommendations.value : []
  };
  
  // Пользователь увидит dashboard даже если часть данных не загрузилась!
}

// 🚀 ПРОДВИНУТЫЕ ПАТТЕРНЫ

// Комбинирование подходов
async function smartDataLoading() {
  // Сначала критические данные (все или ничего)
  const critical = await Promise.all([
    fetchUserAuth(),
    fetchAppConfig()
  ]);
  
  // Затем опциональные данные (получаем что можем) 
  const optional = await Promise.allSettled([
    fetchUserPreferences(),
    fetchWeatherData(),
    fetchNewsData(),
    fetchAdsData()
  ]);
  
  return {
    critical,
    optional: optional
      .filter(result => result.status === 'fulfilled')
      .map(result => result.value)
  };
}

// Timeout для Promise.all
function withTimeout(promise, ms) {
  return Promise.race([
    promise,
    new Promise((_, reject) => 
      setTimeout(() => reject(new Error('Timeout')), ms)
    )
  ]);
}

async function fastCriticalLoad() {
  try {
    const results = await withTimeout(
      Promise.all([
        fetch('/api/critical1'),
        fetch('/api/critical2')
      ]),
      5000 // 5 секунд максимум
    );
    
    return results;
  } catch (error) {
    throw new Error('Критические данные не загрузились вовремя');
  }
}
```

**🧠 Как запомнить разницу?**

| Аспект | **Promise.all** | **Promise.allSettled** |
|---------|-----------------|------------------------|
| **Философия** | 🎯 Перфекционист | 🤝 Реалист |
| **При ошибке** | ❌ Всё отменяется | ✅ Продолжает работу |
| **Результат** | Массив значений | Массив объектов статусов |
| **Когда использовать** | Критические данные | Опциональные данные |

**⚡ Практические правила:**

- **Promise.all** → когда нужны ВСЕ результаты
- **Promise.allSettled** → когда можно работать с частичными результатами  
- **Последовательно** → когда результат зависит от предыдущего
- **Параллельно** → когда результаты независимы

**Вывод:**
Используйте параллельное выполнение для ускорения. Promise.all для критических данных, Promise.allSettled для гибкости!
```

31. Дескрипторы свойств объектов

**Ответ:**

🎯 **Дескрипторы свойств** — это **настройки** каждого свойства объекта. Как параметры файлов в операционной системе: можно ли читать, писать, видеть в списке, удалять. JavaScript позволяет тонко настраивать каждое свойство!

```javascript
// 🔍 ОСНОВЫ ДЕСКРИПТОРОВ

// Обычное свойство:
const user = { name: 'Иван' };

// Получаем дескриптор свойства:
const descriptor = Object.getOwnPropertyDescriptor(user, 'name');
console.log(descriptor);
// {
//   value: 'Иван',
//   writable: true,      // Можно изменять
//   enumerable: true,    // Видно в циклах
//   configurable: true   // Можно удалять/настраивать
// }

// 🎯 Создаем свойство с настройками:
const obj = {};

Object.defineProperty(obj, 'secret', {
  value: 'секретные данные',
  writable: false,      // ❌ Нельзя изменить
  enumerable: false,    // ❌ Невидимо в циклах
  configurable: false   // ❌ Нельзя удалить
});

console.log(obj.secret);        // "секретные данные"
obj.secret = 'новое значение';  // Ничего не произойдет
console.log(obj.secret);        // "секретные данные" - не изменилось!

for (let key in obj) {
  console.log(key); // secret НЕ появится в цикле
}

// delete obj.secret; // Не удалится
```

**🔍 ТИПЫ ДЕСКРИПТОРОВ:**

```javascript
// 1️⃣ ДЕСКРИПТОР ДАННЫХ (Data Descriptor)
const product = {};

Object.defineProperty(product, 'price', {
  value: 1000,
  writable: true,       // Можно менять значение
  enumerable: true,     // Видно в for...in
  configurable: true    // Можно переконфигурировать
});

console.log(product.price); // 1000
product.price = 1200;       // Можно изменить
console.log(product.price); // 1200

// 2️⃣ ДЕСКРИПТОР ДОСТУПА (Accessor Descriptor)
let _temperature = 0; // Приватная переменная

Object.defineProperty(product, 'temperature', {
  get() {
    console.log('📖 Читаем температуру');
    return _temperature;
  },
  
  set(value) {
    console.log(`✏️ Устанавливаем температуру: ${value}`);
    if (value < -273) {
      throw new Error('Температура не может быть ниже абсолютного нуля!');
    }
    _temperature = value;
  },
  
  enumerable: true,
  configurable: true
});

console.log(product.temperature); // "📖 Читаем температуру", 0
product.temperature = 25;          // "✏️ Устанавливаем температуру: 25"
console.log(product.temperature);  // "📖 Читаем температуру", 25

// product.temperature = -300; // Error: Температура не может быть ниже абсолютного нуля!
```

**🔍 ФЛАГИ ДЕСКРИПТОРОВ:**

```javascript
// 🚩 WRITABLE - можно ли изменять значение

const readOnlyObj = {};
Object.defineProperty(readOnlyObj, 'constant', {
  value: 'неизменяемое значение',
  writable: false, // ❌ Только для чтения
  enumerable: true,
  configurable: true
});

console.log(readOnlyObj.constant);           // "неизменяемое значение"
readOnlyObj.constant = 'новое значение';     // В strict mode - TypeError!
console.log(readOnlyObj.constant);           // "неизменяемое значение" (не изменилось)

// 🚩 ENUMERABLE - видно ли в циклах

const hiddenProps = { visible: 'всем видно' };

Object.defineProperty(hiddenProps, 'hidden', {
  value: 'скрытое значение',
  writable: true,
  enumerable: false, // ❌ Невидимо в циклах
  configurable: true
});

console.log('Все ключи:', Object.keys(hiddenProps));           // ['visible']
console.log('for...in:');
for (let key in hiddenProps) {
  console.log(key); // Только 'visible'
}

console.log('Но прямой доступ работает:', hiddenProps.hidden); // "скрытое значение"

// Чтобы увидеть ВСЕ свойства:
console.log('Все свойства:', Object.getOwnPropertyNames(hiddenProps)); 
// ['visible', 'hidden']

// 🚩 CONFIGURABLE - можно ли удалять/переконфигурировать

const permanentObj = {};
Object.defineProperty(permanentObj, 'permanent', {
  value: 'навсегда',
  writable: true,
  enumerable: true,
  configurable: false // ❌ Нельзя удалить или изменить дескриптор
});

// delete permanentObj.permanent; // В strict mode - TypeError!
console.log(permanentObj.permanent); // "навсегда" (не удалилось)

// Попытка изменить дескриптор:
try {
  Object.defineProperty(permanentObj, 'permanent', {
    enumerable: false // Пытаемся изменить
  });
} catch (error) {
  console.log('Ошибка:', error.message); // Cannot redefine property
}
```

**🔍 ПРАКТИЧЕСКИЕ ПРИМЕНЕНИЯ:**

```javascript
// 1️⃣ СОЗДАНИЕ КОНСТАНТ

function createConstants(obj) {
  for (let [key, value] of Object.entries(obj)) {
    Object.defineProperty(this, key, {
      value: value,
      writable: false,    // Константа
      enumerable: true,
      configurable: false // Нельзя удалить
    });
  }
}

const MATH_CONSTANTS = {};
createConstants.call(MATH_CONSTANTS, {
  PI: 3.14159,
  E: 2.71828,
  GOLDEN_RATIO: 1.618
});

console.log(MATH_CONSTANTS.PI); // 3.14159
// MATH_CONSTANTS.PI = 3.14; // Не изменится!

// 2️⃣ ВАЛИДАЦИЯ ЧЕРЕЗ СЕТТЕРЫ

class User {
  constructor(name) {
    this._name = name;
    this._age = 0;
    
    // Создаем свойство с валидацией
    Object.defineProperty(this, 'age', {
      get() {
        return this._age;
      },
      
      set(value) {
        if (typeof value !== 'number' || value < 0 || value > 150) {
          throw new Error('Возраст должен быть числом от 0 до 150');
        }
        this._age = value;
      },
      
      enumerable: true,
      configurable: false
    });
  }
}

const user = new User('Иван');
user.age = 25;     // ✅ Работает
console.log(user.age); // 25

// user.age = -5;     // ❌ Error: Возраст должен быть числом от 0 до 150
// user.age = '25';   // ❌ Error: Возраст должен быть числом от 0 до 150

// 3️⃣ ВЫЧИСЛЯЕМЫЕ СВОЙСТВА

class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
    
    // Вычисляемое свойство area
    Object.defineProperty(this, 'area', {
      get() {
        console.log('🧮 Вычисляем площадь...');
        return this.width * this.height;
      },
      
      enumerable: true,
      configurable: true
    });
    
    // Вычисляемое свойство perimeter
    Object.defineProperty(this, 'perimeter', {
      get() {
        console.log('📐 Вычисляем периметр...');
        return 2 * (this.width + this.height);
      },
      
      enumerable: true,
      configurable: true
    });
  }
}

const rect = new Rectangle(5, 3);
console.log(rect.area);      // "🧮 Вычисляем площадь...", 15
console.log(rect.perimeter); // "📐 Вычисляем периметр...", 16

rect.width = 10;
console.log(rect.area);      // "🧮 Вычисляем площадь...", 30 (пересчиталось!)

// 4️⃣ ЛЕНИВАЯ ИНИЦИАЛИЗАЦИЯ

class DataManager {
  constructor() {
    // Дорогие вычисления выполняются только при первом обращении
    Object.defineProperty(this, 'expensiveData', {
      get() {
        if (!this._expensiveData) {
          console.log('💰 Выполняем дорогие вычисления...');
          this._expensiveData = this._calculateExpensiveData();
        }
        return this._expensiveData;
      },
      
      configurable: true
    });
  }
  
  _calculateExpensiveData() {
    // Имитируем дорогие вычисления
    let result = 0;
    for (let i = 0; i < 1000000; i++) {
      result += Math.random();
    }
    return result;
  }
}

const manager = new DataManager();
console.log('Создали менеджер'); // Дорогие вычисления еще не выполнялись

console.log(manager.expensiveData); // "💰 Выполняем дорогие вычисления...", результат
console.log(manager.expensiveData); // Второй раз - берем из кэша, без вычислений
```

**🔍 МНОЖЕСТВЕННЫЕ ДЕСКРИПТОРЫ:**

```javascript
// Создаем несколько свойств сразу
const calculator = {};

Object.defineProperties(calculator, {
  // Константы
  PI: {
    value: 3.14159,
    writable: false,
    enumerable: true,
    configurable: false
  },
  
  E: {
    value: 2.71828,
    writable: false,
    enumerable: true,
    configurable: false
  },
  
  // Метод
  circleArea: {
    value: function(radius) {
      return this.PI * radius * radius;
    },
    writable: false,
    enumerable: true,
    configurable: false
  },
  
  // Приватное свойство
  _version: {
    value: '1.0.0',
    writable: false,
    enumerable: false, // Скрыто
    configurable: false
  }
});

console.log(calculator.circleArea(5)); // 78.53975
console.log(Object.keys(calculator));  // ['PI', 'E', 'circleArea'] - без _version
```

**🧠 Как запомнить дескрипторы?**

| Флаг | Значение | Аналогия |
|------|----------|----------|
| **writable** | Можно изменять | 📝 Карандаш vs 🖊️ ручка |
| **enumerable** | Видно в циклах | 👁️ Видимый vs 👻 невидимый |
| **configurable** | Можно удалять/настраивать | 🔧 Настраиваемый vs 🔒 заблокированный |

**⚡ Практические советы:**

✅ **Используйте дескрипторы для:**
- Создания констант
- Валидации данных
- Вычисляемых свойств
- Ленивой инициализации
- Скрытия служебных свойств

❌ **Не используйте когда:**
- Нужна простота и читаемость
- Свойства часто изменяются
- Работаете с внешними библиотеками

**Вывод:**
Дескрипторы дают полный контроль над свойствами объектов. Позволяют создавать константы, валидацию и вычисляемые свойства!

32. Создание неизменяемых объектов

**Ответ:**

🎯 **Неизменяемые объекты** — это **защита от случайного изменения**. Как защитная пленка на экране телефона: объект остается рабочим, но его нельзя поцарапать. JavaScript предлагает разные уровни защиты!

```javascript
// 🔍 УРОВНИ ЗАЩИТЫ ОБЪЕКТОВ

// Обычный объект - без защиты
const normalObj = { name: 'Иван', age: 25 };
normalObj.name = 'Петр';        // ✅ Можно изменить
normalObj.city = 'Москва';      // ✅ Можно добавить
delete normalObj.age;           // ✅ Можно удалить
console.log(normalObj);         // { name: 'Петр', city: 'Москва' }

// 🎯 Тестируем уровни защиты:
function testMutations(obj, name) {
  console.log(`\n🧪 Тестируем ${name}:`);
  
  try { obj.existingProp = 'изменено'; } catch (e) { console.log('❌ Изменение:', e.message); }
  try { obj.newProp = 'добавлено'; } catch (e) { console.log('❌ Добавление:', e.message); }
  try { delete obj.existingProp; } catch (e) { console.log('❌ Удаление:', e.message); }
  
  console.log('Результат:', obj);
}
```

**🔍 УРОВЕНЬ 1: OBJECT.PREVENTEXTENSIONS**

```javascript
// 🚧 Запрещает ДОБАВЛЕНИЕ новых свойств

const extensible = { name: 'Иван', age: 25 };
Object.preventExtensions(extensible);

// ✅ Можно изменять существующие
extensible.name = 'Петр';
console.log(extensible.name); // "Петр"

// ✅ Можно удалять
delete extensible.age;
console.log(extensible); // { name: 'Петр' }

// ❌ Нельзя добавлять новые
extensible.city = 'Москва'; // В strict mode - TypeError!
console.log(extensible.city); // undefined

console.log('Расширяемый?', Object.isExtensible(extensible)); // false

// 🎯 Практическое применение
class APIConfig {
  constructor() {
    this.baseURL = 'https://api.example.com';
    this.timeout = 5000;
    this.apiKey = 'secret-key';
    
    // Запрещаем добавление новых настроек
    Object.preventExtensions(this);
  }
  
  updateTimeout(newTimeout) {
    this.timeout = newTimeout; // ✅ Можно изменять существующие
  }
}

const config = new APIConfig();
config.updateTimeout(10000);   // ✅ Работает
// config.newSetting = 'value'; // ❌ Не сработает
```

**🔍 УРОВЕНЬ 2: OBJECT.SEAL**

```javascript
// 🔒 Запрещает ДОБАВЛЕНИЕ и УДАЛЕНИЕ свойств

const sealed = Object.seal({ 
  name: 'Иван', 
  age: 25,
  address: { city: 'Москва', street: 'Ленина' }
});

// ✅ Можно изменять значения
sealed.name = 'Петр';
sealed.age = 30;
console.log(sealed); // { name: 'Петр', age: 30, address: {...} }

// ❌ Нельзя добавлять
sealed.city = 'СПб'; // В strict mode - TypeError!

// ❌ Нельзя удалять  
delete sealed.age; // В strict mode - TypeError!

// ⚠️ Вложенные объекты НЕ защищены
sealed.address.city = 'Санкт-Петербург'; // ✅ Работает!
sealed.address.country = 'Россия';        // ✅ Работает!

console.log('Запечатан?', Object.isSealed(sealed)); // true
console.log('Заморожен?', Object.isFrozen(sealed)); // false

// 🎯 Практическое применение - настройки пользователя
function createUserSettings(initialSettings) {
  const settings = { ...initialSettings };
  
  // Запечатываем, чтобы случайно не добавили/удалили настройки
  return Object.seal(settings);
}

const userSettings = createUserSettings({
  theme: 'dark',
  language: 'ru',
  notifications: true
});

// Можно менять существующие настройки
userSettings.theme = 'light';              // ✅
userSettings.language = 'en';              // ✅

// Нельзя добавить новые или удалить существующие
// userSettings.newFeature = true;         // ❌
// delete userSettings.notifications;      // ❌
```

**🔍 УРОВЕНЬ 3: OBJECT.FREEZE**

```javascript
// ❄️ Полная заморозка - нельзя ничего менять

const frozen = Object.freeze({
  name: 'Иван',
  age: 25,
  hobbies: ['футбол', 'чтение'],
  address: { city: 'Москва' }
});

// ❌ Нельзя изменять
frozen.name = 'Петр'; // В strict mode - TypeError!

// ❌ Нельзя добавлять
frozen.city = 'СПб'; // В strict mode - TypeError!

// ❌ Нельзя удалять
delete frozen.age; // В strict mode - TypeError!

console.log('Заморожен?', Object.isFrozen(frozen)); // true

// ⚠️ НО! Вложенные объекты и массивы НЕ заморожены
frozen.hobbies.push('плавание');     // ✅ Работает!
frozen.address.street = 'Ленина';    // ✅ Работает!

console.log(frozen);
// {
//   name: 'Иван',
//   age: 25, 
//   hobbies: ['футбол', 'чтение', 'плавание'],
//   address: { city: 'Москва', street: 'Ленина' }
// }

// 🎯 Создание констант
const MATH_CONSTANTS = Object.freeze({
  PI: 3.14159,
  E: 2.71828,
  GOLDEN_RATIO: 1.618,
  SQRT_2: 1.41421
});

// MATH_CONSTANTS.PI = 3.14; // ❌ Не изменится

// 🎯 Конфигурация приложения
const APP_CONFIG = Object.freeze({
  API_URL: 'https://api.myapp.com',
  VERSION: '1.0.0',
  FEATURE_FLAGS: {
    NEW_UI: true,
    ANALYTICS: false
  }
});

// ❌ Нельзя случайно изменить критичные настройки
// APP_CONFIG.API_URL = 'https://malicious-site.com';
```

**🔍 ГЛУБОКАЯ ЗАМОРОЗКА:**

```javascript
// 🧊 Рекурсивная заморозка всех вложенных объектов

function deepFreeze(obj) {
  // Получаем все собственные свойства
  Object.getOwnPropertyNames(obj).forEach(prop => {
    const value = obj[prop];
    
    // Если значение - объект, рекурсивно замораживаем его
    if (value && typeof value === 'object' && !Object.isFrozen(value)) {
      deepFreeze(value);
    }
  });
  
  // Замораживаем сам объект
  return Object.freeze(obj);
}

// Тестируем глубокую заморозку
const deepFrozenUser = deepFreeze({
  name: 'Иван',
  age: 25,
  address: {
    city: 'Москва',
    coordinates: {
      lat: 55.7558,
      lng: 37.6176
    }
  },
  hobbies: ['футбол', 'чтение'],
  friends: [
    { name: 'Петр', age: 26 },
    { name: 'Мария', age: 24 }
  ]
});

// Теперь ВСЁ заморожено!
// deepFrozenUser.name = 'Петр';                    // ❌
// deepFrozenUser.address.city = 'СПб';             // ❌  
// deepFrozenUser.address.coordinates.lat = 60;     // ❌
// deepFrozenUser.hobbies.push('плавание');         // ❌
// deepFrozenUser.friends[0].name = 'Алексей';      // ❌

console.log('Глубоко заморожен?', Object.isFrozen(deepFrozenUser.address)); // true

// 🎯 Улучшенная версия с поддержкой всех типов
function advancedDeepFreeze(obj) {
  // Пропускаем примитивы
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }
  
  // Пропускаем уже замороженные
  if (Object.isFrozen(obj)) {
    return obj;
  }
  
  // Обрабатываем массивы
  if (Array.isArray(obj)) {
    obj.forEach(item => advancedDeepFreeze(item));
  } 
  // Обрабатываем обычные объекты
  else {
    Object.values(obj).forEach(value => advancedDeepFreeze(value));
  }
  
  return Object.freeze(obj);
}

// 🎯 Создание неизменяемой конфигурации
const IMMUTABLE_CONFIG = advancedDeepFreeze({
  database: {
    host: 'localhost',
    port: 5432,
    credentials: {
      username: 'admin',
      password: 'secret'
    }
  },
  features: ['auth', 'analytics', 'notifications'],
  limits: {
    maxUsers: 1000,
    rateLimit: 100
  }
});

// Полностью защищено от изменений!
```

**🔍 СРАВНЕНИЕ МЕТОДОВ:**

```javascript
// 📊 Таблица возможностей

const comparison = {
  'Обычный объект': {
    read: '✅',
    write: '✅', 
    add: '✅',
    delete: '✅'
  },
  'preventExtensions': {
    read: '✅',
    write: '✅',
    add: '❌',
    delete: '✅'
  },
  'seal': {
    read: '✅', 
    write: '✅',
    add: '❌',
    delete: '❌'
  },
  'freeze': {
    read: '✅',
    write: '❌',
    add: '❌', 
    delete: '❌'
  }
};

// 🔍 Функция для проверки уровня защиты
function getProtectionLevel(obj) {
  if (Object.isFrozen(obj)) return 'frozen (заморожен)';
  if (Object.isSealed(obj)) return 'sealed (запечатан)';
  if (!Object.isExtensible(obj)) return 'non-extensible (нерасширяемый)';
  return 'mutable (изменяемый)';
}

// Тестируем
const testObj1 = { a: 1 };
const testObj2 = Object.preventExtensions({ a: 1 });
const testObj3 = Object.seal({ a: 1 });
const testObj4 = Object.freeze({ a: 1 });

console.log('Обычный:', getProtectionLevel(testObj1));      // "mutable"
console.log('preventExtensions:', getProtectionLevel(testObj2)); // "non-extensible"
console.log('seal:', getProtectionLevel(testObj3));         // "sealed"
console.log('freeze:', getProtectionLevel(testObj4));       // "frozen"
```

**🔍 ПРАКТИЧЕСКИЕ ПРИМЕНЕНИЯ:**

```javascript
// 1️⃣ НЕИЗМЕНЯЕМЫЕ СОСТОЯНИЯ (для Redux-подобных паттернов)

function createImmutableState(initialState) {
  return deepFreeze({ ...initialState });
}

function updateState(currentState, changes) {
  // Создаем новое состояние вместо изменения существующего
  return deepFreeze({
    ...currentState,
    ...changes,
    updatedAt: new Date().toISOString()
  });
}

let state = createImmutableState({
  user: { name: 'Иван', age: 25 },
  theme: 'dark',
  isLoading: false
});

// Обновляем состояние (создаем новое)
state = updateState(state, { 
  user: { ...state.user, age: 26 },
  isLoading: true 
});

// 2️⃣ ЗАЩИТА API ОТВЕТОВ

function protectAPIResponse(response) {
  // Защищаем от случайного изменения данных API
  return Object.freeze({
    data: deepFreeze(response.data),
    status: response.status,
    headers: Object.freeze({ ...response.headers }),
    timestamp: new Date().toISOString()
  });
}

const apiResponse = protectAPIResponse({
  data: { users: [{ name: 'Иван' }] },
  status: 200,
  headers: { 'content-type': 'application/json' }
});

// Данные защищены от изменения
// apiResponse.data.users.push({ name: 'Новый' }); // ❌

// 3️⃣ СОЗДАНИЕ ENUM-ОВ

const OrderStatus = Object.freeze({
  PENDING: 'pending',
  PROCESSING: 'processing', 
  COMPLETED: 'completed',
  CANCELLED: 'cancelled'
});

// OrderStatus.PENDING = 'changed'; // ❌ Не изменится
```

**🧠 Как запомнить уровни защиты?**

| Метод | Аналогия | Что разрешено |
|-------|----------|---------------|
| **preventExtensions** | 🚪 Закрыли дверь | Изменять, удалять |
| **seal** | 📦 Запечатали коробку | Только изменять |
| **freeze** | ❄️ Заморозили | Только читать |

**⚡ Практические советы:**

✅ **Используйте неизменяемость для:**
- Констант и конфигураций
- API ответов
- Состояний в функциональном программировании
- Защиты критичных данных

❌ **Не используйте когда:**
- Объекты часто изменяются
- Нужна высокая производительность
- Работаете с большими объектами

**Вывод:**
Неизменяемость защищает от ошибок и делает код предсказуемым. Выбирайте уровень защиты в зависимости от потребностей!

33. Создание изменяемого свойства

**Ответ:**

🎯 **Изменяемое свойство** — это свойство с **максимальными правами**: можно читать, писать, видеть в циклах и настраивать. Все флаги дескриптора установлены в `true`. Это поведение **по умолчанию** для обычных свойств!

```javascript
// 🔍 СОЗДАНИЕ ПОЛНОСТЬЮ ИЗМЕНЯЕМОГО СВОЙСТВА

const obj = {};

// 1️⃣ ДЕСКРИПТОР ДАННЫХ (Data Descriptor)
Object.defineProperty(obj, 'mutableProp', {
  value: 'начальное значение',
  writable: true,       // ✅ Можно изменять значение
  enumerable: true,     // ✅ Видно в for...in, Object.keys()
  configurable: true    // ✅ Можно удалять и переконфигурировать
});

// Тестируем все возможности:
console.log(obj.mutableProp);               // "начальное значение"
obj.mutableProp = 'новое значение';         // ✅ Изменение работает
console.log(obj.mutableProp);               // "новое значение"

for (let key in obj) {
  console.log(key);                         // ✅ Видно: "mutableProp"
}

delete obj.mutableProp;                     // ✅ Удаление работает
console.log(obj.mutableProp);               // undefined

// 2️⃣ ДЕСКРИПТОР ДОСТУПА (Accessor Descriptor)
let _privateValue = 'начальное значение';

Object.defineProperty(obj, 'accessorProp', {
  get() { 
    console.log('📖 Читаем значение');
    return _privateValue; 
  },
  
  set(newValue) { 
    console.log(`✏️ Записываем: ${newValue}`);
    _privateValue = newValue; 
  },
  
  enumerable: true,     // ✅ Видно в циклах
  configurable: true    // ✅ Можно переконфигурировать
});

// Тестируем геттер/сеттер:
console.log(obj.accessorProp);              // "📖 Читаем значение", "начальное значение"
obj.accessorProp = 'через сеттер';          // "✏️ Записываем: через сеттер"
console.log(obj.accessorProp);              // "📖 Читаем значение", "через сеттер"

// 🎯 СРАВНЕНИЕ С ОБЫЧНЫМ СВОЙСТВОМ

// Обычное свойство (изменяемое по умолчанию):
obj.normalProp = 'обычное свойство';

// Проверяем дескрипторы:
console.log('Обычное свойство:', Object.getOwnPropertyDescriptor(obj, 'normalProp'));
// { value: "обычное свойство", writable: true, enumerable: true, configurable: true }

console.log('Созданное через defineProperty:', Object.getOwnPropertyDescriptor(obj, 'mutableProp'));
// { value: undefined, writable: true, enumerable: true, configurable: true } (уже удалено)
```

**🧠 Как запомнить?**
- **Изменяемое свойство** = все флаги `true`
- **По умолчанию** все свойства изменяемые
- **defineProperty** дает полный контроль

**Вывод:**
Изменяемое свойство — это стандартное поведение JavaScript. Все новые свойства создаются изменяемыми, если не указано иное!

34. Proxy - зачем нужен?

**Ответ:**

🎯 **Proxy** — это **"охранник" объекта**, который перехватывает **все операции** с ним. Как охранник в банке: контролирует доступ, проверяет документы, ведет журнал посещений. Позволяет кастомизировать поведение объектов!

```javascript
// 🔍 ОСНОВЫ PROXY

const target = { name: 'Иван', age: 25 };

// Создаем прокси с "охранником" (handler)
const proxy = new Proxy(target, {
  // Перехватываем чтение свойств
  get(target, property) {
    console.log(`📖 Читаем свойство: ${property}`);
    return target[property];
  },
  
  // Перехватываем запись свойств  
  set(target, property, value) {
    console.log(`✏️ Записываем ${property} = ${value}`);
    target[property] = value;
    return true; // Успешная запись
  },
  
  // Перехватываем проверку наличия (оператор in)
  has(target, property) {
    console.log(`🔍 Проверяем наличие: ${property}`);
    return property in target;
  },
  
  // Перехватываем удаление
  deleteProperty(target, property) {
    console.log(`🗑️ Удаляем свойство: ${property}`);
    delete target[property];
    return true;
  }
});

// Тестируем перехваты:
console.log(proxy.name);        // "📖 Читаем свойство: name", "Иван"
proxy.city = 'Москва';          // "✏️ Записываем city = Москва"
console.log('age' in proxy);    // "🔍 Проверяем наличие: age", true
delete proxy.age;               // "🗑️ Удаляем свойство: age"

console.log('Исходный объект:', target); // { name: 'Иван', city: 'Москва' }
```

**🔍 ПРАКТИЧЕСКИЕ ПРИМЕНЕНИЯ:**

```javascript
// 1️⃣ ВАЛИДАЦИЯ ДАННЫХ

function createValidatedUser(initialData = {}) {
  const userData = { ...initialData };
  
  return new Proxy(userData, {
    set(target, property, value) {
      // Валидация по типу свойства
      if (property === 'age') {
        if (typeof value !== 'number' || value < 0 || value > 150) {
          throw new Error('Возраст должен быть числом от 0 до 150');
        }
      }
      
      if (property === 'email') {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        if (!emailRegex.test(value)) {
          throw new Error('Некорректный email адрес');
        }
      }
      
      if (property === 'name') {
        if (typeof value !== 'string' || value.length < 2) {
          throw new Error('Имя должно быть строкой минимум 2 символа');
        }
      }
      
      console.log(`✅ Валидация пройдена: ${property} = ${value}`);
      target[property] = value;
      return true;
    }
  });
}

const user = createValidatedUser({ name: 'Иван' });

user.age = 25;                          // ✅ "Валидация пройдена: age = 25"
user.email = 'ivan@example.com';        // ✅ "Валидация пройдена: email = ivan@example.com"

// user.age = -5;                       // ❌ Error: Возраст должен быть числом от 0 до 150
// user.email = 'invalid-email';        // ❌ Error: Некорректный email адрес

// 2️⃣ ЛОГИРОВАНИЕ И АУДИТ

function createAuditedObject(obj, objectName = 'object') {
  const auditLog = [];
  
  const proxy = new Proxy(obj, {
    get(target, property) {
      const timestamp = new Date().toISOString();
      auditLog.push({
        action: 'READ',
        property,
        value: target[property],
        timestamp
      });
      
      console.log(`[${timestamp}] READ ${objectName}.${property}`);
      return target[property];
    },
    
    set(target, property, value) {
      const timestamp = new Date().toISOString();
      const oldValue = target[property];
      
      auditLog.push({
        action: 'WRITE',
        property,
        oldValue,
        newValue: value,
        timestamp
      });
      
      console.log(`[${timestamp}] WRITE ${objectName}.${property}: ${oldValue} → ${value}`);
      target[property] = value;
      return true;
    }
  });
  
  // Добавляем метод для получения лога
  proxy.getAuditLog = () => [...auditLog];
  
  return proxy;
}

const bankAccount = createAuditedObject(
  { balance: 1000, owner: 'Иван Петров' },
  'BankAccount'
);

console.log(bankAccount.balance);     // "[timestamp] READ BankAccount.balance"
bankAccount.balance = 1500;           // "[timestamp] WRITE BankAccount.balance: 1000 → 1500"

console.log('Audit Log:', bankAccount.getAuditLog());

// 3️⃣ ВИРТУАЛЬНЫЕ СВОЙСТВА

function createSmartObject(data = {}) {
  return new Proxy(data, {
    get(target, property) {
      // Обычные свойства
      if (property in target) {
        return target[property];
      }
      
      // Виртуальные вычисляемые свойства
      if (property === 'fullName' && target.firstName && target.lastName) {
        return `${target.firstName} ${target.lastName}`;
      }
      
      if (property === 'age' && target.birthYear) {
        return new Date().getFullYear() - target.birthYear;
      }
      
      if (property === 'isAdult' && target.birthYear) {
        const age = new Date().getFullYear() - target.birthYear;
        return age >= 18;
      }
      
      // Поиск по нечеткому совпадению
      const keys = Object.keys(target);
      const fuzzyMatch = keys.find(key => 
        key.toLowerCase().includes(property.toLowerCase())
      );
      
      if (fuzzyMatch) {
        console.log(`🔍 Нашли похожее свойство: ${fuzzyMatch}`);
        return target[fuzzyMatch];
      }
      
      return undefined;
    }
  });
}

const person = createSmartObject({
  firstName: 'Иван',
  lastName: 'Петров',
  birthYear: 1990,
  phoneNumber: '+7-999-123-45-67'
});

console.log(person.fullName);    // "Иван Петров" (виртуальное свойство)
console.log(person.age);         // 34 (вычисленное из birthYear)
console.log(person.isAdult);     // true (вычисленное)
console.log(person.phone);       // "🔍 Нашли похожее свойство: phoneNumber", "+7-999-123-45-67"

// 4️⃣ ОТРИЦАТЕЛЬНАЯ ИНДЕКСАЦИЯ МАССИВОВ

function createAdvancedArray(arr = []) {
  return new Proxy(arr, {
    get(target, property) {
      // Обычные свойства и методы
      if (property in target) {
        return target[property];
      }
      
      // Отрицательная индексация
      const index = Number(property);
      if (Number.isInteger(index) && index < 0) {
        return target[target.length + index];
      }
      
      return undefined;
    }
  });
}

const smartArray = createAdvancedArray(['a', 'b', 'c', 'd', 'e']);

console.log(smartArray[0]);     // 'a' (обычная индексация)
console.log(smartArray[-1]);    // 'e' (последний элемент)
console.log(smartArray[-2]);    // 'd' (предпоследний элемент)
console.log(smartArray[-5]);    // 'a' (первый элемент через отрицательный индекс)

// 5️⃣ ЗАЩИТА ОТ НЕСУЩЕСТВУЮЩИХ СВОЙСТВ

function createSafeObject(obj) {
  return new Proxy(obj, {
    get(target, property) {
      if (property in target) {
        return target[property];
      }
      
      // Предупреждаем о доступе к несуществующему свойству
      console.warn(`⚠️ Попытка доступа к несуществующему свойству: ${property}`);
      
      // Возвращаем Proxy для цепочечных вызовов
      return new Proxy(() => {}, {
        get() { return this; },
        apply() { return this; }
      });
    }
  });
}

const safeConfig = createSafeObject({
  database: { host: 'localhost' },
  api: { url: 'https://api.example.com' }
});

console.log(safeConfig.database.host);        // "localhost"
console.log(safeConfig.nonexistent.prop);     // "⚠️ Попытка доступа...", Proxy
safeConfig.missing.method().chain();          // Не падает, возвращает Proxy
```

**🔍 СПИСОК TRAP-ОВ (ЛОВУШЕК):**

```javascript
// Все доступные ловушки Proxy:

const fullProxy = new Proxy({}, {
  // Основные операции
  get(target, property, receiver) {
    console.log('get:', property);
  },
  
  set(target, property, value, receiver) {
    console.log('set:', property, '=', value);
    return true;
  },
  
  // Проверка наличия
  has(target, property) {
    console.log('has:', property);
    return true;
  },
  
  // Удаление
  deleteProperty(target, property) {
    console.log('delete:', property);
    return true;
  },
  
  // Перечисление ключей
  ownKeys(target) {
    console.log('ownKeys');
    return Object.keys(target);
  },
  
  // Получение дескриптора
  getOwnPropertyDescriptor(target, property) {
    console.log('getOwnPropertyDescriptor:', property);
    return Object.getOwnPropertyDescriptor(target, property);
  },
  
  // Определение свойства
  defineProperty(target, property, descriptor) {
    console.log('defineProperty:', property);
    return true;
  },
  
  // Проверка расширяемости
  isExtensible(target) {
    console.log('isExtensible');
    return true;
  },
  
  // Предотвращение расширений
  preventExtensions(target) {
    console.log('preventExtensions');
    return true;
  },
  
  // Получение прототипа
  getPrototypeOf(target) {
    console.log('getPrototypeOf');
    return Object.getPrototypeOf(target);
  },
  
  // Установка прототипа
  setPrototypeOf(target, prototype) {
    console.log('setPrototypeOf');
    return true;
  },
  
  // Вызов функции (если target - функция)
  apply(target, thisArg, argumentsList) {
    console.log('apply');
    return target.apply(thisArg, argumentsList);
  },
  
  // Конструктор (если target - функция)
  construct(target, argumentsList, newTarget) {
    console.log('construct');
    return new target(...argumentsList);
  }
});
```

**🧠 Как запомнить Proxy?**

| Аналогия | Что делает | Пример использования |
|----------|------------|---------------------|
| **🛡️ Охранник** | Контролирует доступ | Валидация, авторизация |
| **📊 Аудитор** | Ведет журнал действий | Логирование, аналитика |
| **🎭 Актер** | Притворяется объектом | Виртуальные свойства |
| **🔧 Переводчик** | Изменяет поведение | API адаптеры |

**⚡ Практические советы:**

✅ **Используйте Proxy для:**
- Валидации данных
- Логирования операций  
- Создания виртуальных свойств
- Адаптации API
- Отладки и профилирования

❌ **Не используйте когда:**
- Нужна максимальная производительность
- Простая логика без сложных перехватов
- Браузер не поддерживает Proxy

**Вывод:**
Proxy — это мощный инструмент для контроля поведения объектов. Позволяет создавать "умные" объекты с валидацией, логированием и виртуальными свойствами!

35. **ArrayBuffer - что это такое и зачем нужно?**

🎯 **Простое объяснение:** ArrayBuffer — это "ящик" для хранения сырых бинарных данных в памяти. Как контейнер с байтами, которые можно читать разными способами.

**🔍 Основные концепции:**

```javascript
// 🔍 1. ArrayBuffer - просто контейнер с байтами
const buffer = new ArrayBuffer(16); // Создаем "ящик" на 16 байт
console.log(buffer.byteLength);     // 16 (размер в байтах)

// ❌ Нельзя напрямую работать с данными
// buffer[0] = 42; // Ошибка! ArrayBuffer только контейнер

// ✅ Нужно создать "представление" (view)
const view = new Uint8Array(buffer);
view[0] = 42;     // Теперь можно записать байт
console.log(view[0]); // 42

// 🔍 2. Разные способы "смотреть" на одни данные
const int8View = new Int8Array(buffer);   // Как signed байты (-128 до 127)
const uint8View = new Uint8Array(buffer); // Как unsigned байты (0 до 255)
const uint32View = new Uint32Array(buffer); // Как 32-битные числа

// Записываем в uint8View
uint8View[0] = 255;
uint8View[1] = 255;
uint8View[2] = 255;
uint8View[3] = 255;

// Читаем как uint32 (те же байты!)
console.log(uint32View[0]); // 4294967295 (все биты установлены)
```

**🔍 Практические примеры:**

```javascript
// 🔍 Работа с файлами
function readFileAsArrayBuffer(file) {
  const reader = new FileReader();
  reader.onload = function(e) {
    const buffer = e.target.result; // ArrayBuffer
    const bytes = new Uint8Array(buffer);
    
    // Проверяем магические байты PNG
    if (bytes[0] === 0x89 && bytes[1] === 0x50 && 
        bytes[2] === 0x4E && bytes[3] === 0x47) {
      console.log('Это PNG файл! 🖼️');
    }
  };
  reader.readAsArrayBuffer(file);
}

// 🔍 Отправка бинарных данных
function sendBinaryData() {
  const buffer = new ArrayBuffer(8);
  const view = new DataView(buffer);
  
  // Записываем разные типы данных
  view.setUint32(0, 123456);    // 4 байта: число
  view.setFloat32(4, 3.14);     // 4 байта: float
  
  // Отправляем на сервер
  fetch('/api/binary', {
    method: 'POST',
    body: buffer,
    headers: { 'Content-Type': 'application/octet-stream' }
  });
}

// 🔍 Копирование между разными views
const sourceBuffer = new ArrayBuffer(12);
const source = new Uint8Array(sourceBuffer);
source.set([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]);

// Создаем новый buffer и копируем данные
const targetBuffer = new ArrayBuffer(12);
const target = new Uint8Array(targetBuffer);
target.set(source); // Копируем все байты

console.log(target); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
```

**🧠 Мнемоника для запоминания:**

- **ArrayBuffer** = "**Ящик**" (просто контейнер)
- **TypedArray** = "**Очки**" (способ смотреть на данные)
- **DataView** = "**Универсальные очки**" (можно читать любые типы)

**⚡ Почему НЕ обычный Array:**

```javascript
// ❌ Обычный Array - медленно и много памяти
const normalArray = [1, 2, 3, 4]; // Каждый элемент = объект JavaScript

// ✅ TypedArray - быстро и мало памяти
const typedArray = new Uint8Array([1, 2, 3, 4]); // Каждый элемент = 1 байт

console.log(Array.isArray(typedArray)); // false (это не Array!)
console.log(typedArray instanceof Uint8Array); // true

// 🔍 Проверка на TypedArray
function isTypedArray(obj) {
  return ArrayBuffer.isView(obj);
}

console.log(isTypedArray(normalArray));  // false
console.log(isTypedArray(typedArray));   // true
```

**⚡ Когда использовать:**

```javascript
// ✅ Хорошие случаи:
// 1. Работа с файлами (изображения, видео, звук)
// 2. WebGL (графика)
// 3. WebRTC (видеозвонки)
// 4. Криптография
// 5. Сетевые протоколы

// ❌ Плохие случаи:
// 1. Обычные массивы данных
// 2. JSON объекты
// 3. Строки
// 4. Простая математика
```

**Заключение:** ArrayBuffer — это низкоуровневый инструмент для работы с бинарными данными. Используйте когда нужна максимальная производительность или работа с файлами/сетью. Для обычных задач достаточно обычных массивов.

36. **encodeURI vs encodeURIComponent - в чем разница?**

🎯 **Простое объяснение:** Две функции для кодирования URL, но с разной "агрессивностью". encodeURI более мягкий, encodeURIComponent более строгий.

**🔍 Ключевая разница:**

```javascript
const url = "https://site.com/search?query=hello world&category=tech";

// 🔍 encodeURI - "мягкое" кодирование (сохраняет структуру URL)
console.log(encodeURI(url));
// "https://site.com/search?query=hello%20world&category=tech"
// Оставляет :, /, ?, &, = без изменений (это валидные символы URL)

// 🔍 encodeURIComponent - "жесткое" кодирование (кодирует ВСЁ)
console.log(encodeURIComponent(url));
// "https%3A%2F%2Fsite.com%2Fsearch%3Fquery%3Dhello%20world%26category%3Dtech"
// Кодирует ВСЕ специальные символы, включая :, /, ?, &, =
```

**🔍 Детальное сравнение:**

```javascript
// 🔍 Символы, которые НЕ кодируются

// encodeURI оставляет эти символы:
const uriSafe = "; , / ? : @ & = + $ # - _ . ! ~ * ' ( )";
console.log(encodeURI(uriSafe));
// "; , / ? : @ & = + $ # - _ . ! ~ * ' ( )" (без изменений)

// encodeURIComponent оставляет только эти:
const componentSafe = "- _ . ! ~ * ' ( )";
console.log(encodeURIComponent(componentSafe));
// "- _ . ! ~ * ' ( )" (без изменений)

// Все остальные символы кодируются!
console.log(encodeURIComponent("; , / ? : @ & = + $ #"));
// "%3B%20%2C%20%2F%20%3F%20%3A%20%40%20%26%20%3D%20%2B%20%24%20%23"
```

**🔍 Практические примеры:**

```javascript
// ✅ Используйте encodeURI для полных URL
const baseUrl = "https://api.example.com/search?q=hello world";
const encoded = encodeURI(baseUrl);
// "https://api.example.com/search?q=hello%20world"
// ✅ URL остается валидным и рабочим

// ✅ Используйте encodeURIComponent для частей URL
const query = "hello & goodbye";
const category = "fun/games";
const fullUrl = `https://site.com/search?q=${encodeURIComponent(query)}&cat=${encodeURIComponent(category)}`;
// "https://site.com/search?q=hello%20%26%20goodbye&cat=fun%2Fgames"
// ✅ Части URL безопасно закодированы

// 🔍 Опасный пример - НЕ делайте так:
const dangerousUrl = `https://site.com/search?q=${query}&cat=${category}`;
// "https://site.com/search?q=hello & goodbye&cat=fun/games"
// ❌ Символы & и / могут сломать URL!
```

**🧠 Мнемоника для запоминания:**

- **encodeURI** = "**Целый** URL" (для полных адресов)
- **encodeURIComponent** = "**Часть** URL" (для параметров и значений)

**⚡ Реальные сценарии использования:**

```javascript
// 🔍 1. Перенаправление на другой сайт
function redirectTo(url) {
  // ✅ encodeURI - кодируем весь URL
  window.location.href = encodeURI(url);
}

redirectTo("https://example.com/path with spaces/page.html");

// 🔍 2. Построение URL с параметрами
function buildSearchUrl(query, filters) {
  let url = "https://shop.com/search";
  
  // ✅ encodeURIComponent - кодируем каждый параметр отдельно
  const params = [
    `q=${encodeURIComponent(query)}`,
    `price=${encodeURIComponent(filters.price)}`,
    `category=${encodeURIComponent(filters.category)}`
  ];
  
  return url + "?" + params.join("&");
}

const searchUrl = buildSearchUrl("iPhone 15 Pro", {
  price: "1000-2000$",
  category: "phones/smartphones"
});
// "https://shop.com/search?q=iPhone%2015%20Pro&price=1000-2000%24&category=phones%2Fsmartphones"

// 🔍 3. Обратное декодирование
const encoded = "Hello%20World%21";
console.log(decodeURI(encoded));          // "Hello World!"
console.log(decodeURIComponent(encoded)); // "Hello World!"

// Оба работают одинаково для декодирования
```

**⚡ Частые ошибки:**

```javascript
// ❌ НЕ используйте encodeURI для параметров
const wrong = `https://site.com/search?q=${encodeURI("a&b=c")}`;
// "https://site.com/search?q=a&b=c" 
// Символы & и = НЕ закодированы - URL сломан!

// ✅ Правильно - encodeURIComponent для параметров
const correct = `https://site.com/search?q=${encodeURIComponent("a&b=c")}`;
// "https://site.com/search?q=a%26b%3Dc"
// Все символы безопасно закодированы

// ❌ НЕ используйте encodeURIComponent для полных URL
const brokenUrl = encodeURIComponent("https://site.com/path");
// "https%3A%2F%2Fsite.com%2Fpath"
// URL полностью сломан - браузер не поймет!
```

**Заключение:** encodeURI для полных URL, encodeURIComponent для отдельных частей URL. Выбор зависит от того, хотите ли вы сохранить структуру URL или закодировать все специальные символы.

37. **Генераторы и итераторы - что это и зачем?**

🎯 **Простое объяснение:** Генераторы — это функции, которые можно "приостанавливать" и "возобновлять". Итераторы — это объекты, которые умеют выдавать значения по одному.

**🔍 Генераторы - функции с паузами:**

```javascript
// 🔍 Обычная функция - выполняется до конца
function normalFunction() {
  console.log("Шаг 1");
  console.log("Шаг 2"); 
  console.log("Шаг 3");
  return "Готово!";
}

normalFunction(); // Выводит все сразу и завершается

// 🔍 Генератор - можно приостанавливать
function* generatorFunction() {
  console.log("Шаг 1");
  yield "Пауза после шага 1";
  
  console.log("Шаг 2");
  yield "Пауза после шага 2";
  
  console.log("Шаг 3");
  return "Готово!";
}

const gen = generatorFunction();
console.log(gen.next()); // Шаг 1, { value: "Пауза после шага 1", done: false }
console.log(gen.next()); // Шаг 2, { value: "Пауза после шага 2", done: false }
console.log(gen.next()); // Шаг 3, { value: "Готово!", done: true }
```

**🔍 Практические примеры генераторов:**

```javascript
// 🔍 1. Бесконечная последовательность
function* infiniteCounter() {
  let count = 0;
  while (true) { // Бесконечный цикл!
    yield count++;
  }
}

const counter = infiniteCounter();
console.log(counter.next().value); // 0
console.log(counter.next().value); // 1
console.log(counter.next().value); // 2
// Можете вызывать бесконечно!

// 🔍 2. Fibonacci последовательность
function* fibonacci() {
  let [a, b] = [0, 1];
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

const fib = fibonacci();
for (let i = 0; i < 10; i++) {
  console.log(fib.next().value); // 0, 1, 1, 2, 3, 5, 8, 13, 21, 34
}

// 🔍 3. Пошаговая обработка данных
function* processUsers(users) {
  for (const user of users) {
    console.log(`Обрабатываем: ${user.name}`);
    
    // Тяжелая операция (например, API запрос)
    yield fetch(`/api/user/${user.id}`);
    
    console.log(`Готово: ${user.name}`);
  }
}

const users = [{id: 1, name: 'Иван'}, {id: 2, name: 'Мария'}];
const processor = processUsers(users);

// Можем обрабатывать по одному, контролируя скорость
processor.next(); // Начинаем с первого пользователя
setTimeout(() => processor.next(), 1000); // Продолжаем через секунду
```

**🔍 Итераторы - объекты с методом next():**

```javascript
// 🔍 Создаем свой итератор
const customIterator = {
  [Symbol.iterator]() {
    let count = 0;
    const max = 3;
    
    return {
      next() {
        if (count < max) {
          return { value: count++, done: false };
        }
        return { done: true };
      }
    };
  }
};

// Можем использовать в for...of
for (const value of customIterator) {
  console.log(value); // 0, 1, 2
}

// Или вручную
const iterator = customIterator[Symbol.iterator]();
console.log(iterator.next()); // { value: 0, done: false }
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { done: true }
```

**🧠 Мнемоника для запоминания:**

- **Generator** = "**Генерал**" (командует выполнением, может приказать "стоп")
- **yield** = "**Уступать**" (уступает управление наружу)
- **Iterator** = "**Итератор**" (перебирает по одному)

**⚡ Реальные применения:**

```javascript
// 🔍 1. Ленивая загрузка данных
function* lazyDataLoader() {
  const urls = ['/page1', '/page2', '/page3'];
  
  for (const url of urls) {
    console.log(`Загружаем: ${url}`);
    yield fetch(url).then(r => r.json());
  }
}

// Данные загружаются только когда нужны!
const loader = lazyDataLoader();
const firstPage = await loader.next().value; // Загружает только /page1

// 🔍 2. Управление состоянием
function* stateManager() {
  let state = 'начало';
  
  while (true) {
    const action = yield state;
    
    switch (action) {
      case 'login':
        state = 'авторизован';
        break;
      case 'logout':
        state = 'гость';
        break;
      default:
        state = 'ошибка';
    }
  }
}

const fsm = stateManager();
console.log(fsm.next().value);        // 'начало'
console.log(fsm.next('login').value); // 'авторизован'
console.log(fsm.next('logout').value); // 'гость'

// 🔍 3. Пагинация
function* paginator(items, pageSize) {
  for (let i = 0; i < items.length; i += pageSize) {
    yield items.slice(i, i + pageSize);
  }
}

const items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const pages = paginator(items, 3);

console.log(pages.next().value); // [1, 2, 3]
console.log(pages.next().value); // [4, 5, 6]
console.log(pages.next().value); // [7, 8, 9]
console.log(pages.next().value); // [10]
```

**⚡ Генераторы vs обычные функции:**

```javascript
// ❌ Обычная функция - все сразу (может быть медленно)
function getAllNumbers() {
  const numbers = [];
  for (let i = 0; i < 1000000; i++) {
    numbers.push(i * 2);
  }
  return numbers; // Создали миллион чисел в памяти!
}

// ✅ Генератор - по требованию (быстро и экономично)
function* getNumbers() {
  for (let i = 0; i < 1000000; i++) {
    yield i * 2; // Создаем только когда нужно
  }
}

// Взяли только первые 5 - остальные даже не создавались!
const first5 = Array.from(getNumbers()).slice(0, 5);
console.log(first5); // [0, 2, 4, 6, 8]
```

**Заключение:** Генераторы позволяют создавать функции, которые можно приостанавливать и возобновлять. Это полезно для ленивых вычислений, управления потоком данных и экономии памяти. Итераторы — это протокол для перебора значений по одному.

38. **Анализ кода генератора - почему value: undefined?**

🎯 **Простое объяснение:** В этом генераторе `yield` возвращает результат `console.log()`, а `console.log()` всегда возвращает `undefined`.

**🔍 Пошаговый анализ кода:**

```javascript
function* fn(num) {
  for (let i = 0; i < num; i += 1) {
    yield console.log(i); // ⚠️ Проблема здесь!
  }
}

const loop = fn(5);

// Что происходит при каждом вызове next():
console.log(loop.next()); 
// 1. console.log(0) выводит: 0
// 2. console.log(0) возвращает: undefined  
// 3. yield получает: undefined
// 4. next() возвращает: { value: undefined, done: false }

console.log(loop.next()); 
// 1. console.log(1) выводит: 1
// 2. console.log(1) возвращает: undefined
// 3. yield получает: undefined
// 4. next() возвращает: { value: undefined, done: false }
```

**🔍 Почему так происходит:**

```javascript
// 🔍 console.log() ВСЕГДА возвращает undefined
const result1 = console.log("Привет!"); // Выводит "Привет!"
console.log(result1); // undefined

const result2 = console.log(42); // Выводит 42
console.log(result2); // undefined

// 🔍 Поэтому в генераторе:
function* problematicGenerator() {
  yield console.log("A"); // yield получает undefined
  yield console.log("B"); // yield получает undefined
}

const gen = problematicGenerator();
console.log(gen.next()); // Выводит "A", возвращает { value: undefined, done: false }
console.log(gen.next()); // Выводит "B", возвращает { value: undefined, done: false }
```

**🔍 Как исправить - варианты решения:**

```javascript
// ✅ Вариант 1: Yield значение, а потом console.log
function* correctGenerator1(num) {
  for (let i = 0; i < num; i += 1) {
    console.log(i);  // Выводим в консоль
    yield i;         // Возвращаем само значение
  }
}

const gen1 = correctGenerator1(3);
console.log(gen1.next()); // Выводит: 0, возвращает { value: 0, done: false }
console.log(gen1.next()); // Выводит: 1, возвращает { value: 1, done: false }

// ✅ Вариант 2: Yield значение, логирование отдельно
function* correctGenerator2(num) {
  for (let i = 0; i < num; i += 1) {
    yield i; // Сначала yield
  }
}

const gen2 = correctGenerator2(3);
let result = gen2.next();
while (!result.done) {
  console.log(result.value); // Логируем после получения
  result = gen2.next();
}

// ✅ Вариант 3: Создаем объект с данными и логами
function* correctGenerator3(num) {
  for (let i = 0; i < num; i += 1) {
    yield {
      value: i,
      log: `Обрабатываем: ${i}`
    };
  }
}

const gen3 = correctGenerator3(3);
let item = gen3.next();
while (!item.done) {
  console.log(item.value.log);   // Обрабатываем: 0
  console.log(item.value.value); // 0
  item = gen3.next();
}
```

**🧠 Мнемоника для запоминания:**

- **console.log()** = "**Болтун**" (много говорит, но ничего не возвращает - undefined)
- **yield** = "**Курьер**" (доставляет то, что получил)
- **Болтун + Курьер** = "**Курьер доставил пустоту**" (undefined)

**⚡ Практические примеры с логированием:**

```javascript
// 🔍 Правильный генератор с прогрессом
function* downloadFiles(urls) {
  for (let i = 0; i < urls.length; i++) {
    console.log(`Загружаем файл ${i + 1}/${urls.length}: ${urls[i]}`);
    
    // Симуляция загрузки
    const result = yield fetch(urls[i]);
    
    console.log(`Готово: ${urls[i]}`);
    yield result; // Возвращаем результат
  }
}

// 🔍 Генератор для отладки
function* debugGenerator() {
  const steps = ['подключение', 'авторизация', 'получение данных'];
  
  for (const step of steps) {
    console.log(`Начинаем: ${step}`);
    yield { step, status: 'в процессе' };
    
    console.log(`Завершено: ${step}`);
    yield { step, status: 'готово' };
  }
}

const debug = debugGenerator();
console.log(debug.next().value); // { step: 'подключение', status: 'в процессе' }
console.log(debug.next().value); // { step: 'подключение', status: 'готово' }
```

**Заключение:** `yield console.log(i)` возвращает `undefined`, потому что `console.log()` всегда возвращает `undefined`. Для корректной работы нужно разделить логирование и возврат значений в генераторе.

39. **Symbol и системы счисления - уникальность и преобразования**

🎯 **Простое объяснение:** Symbol — это уникальный тип данных для создания приватных ключей. Системы счисления — способы записи чисел в разных основаниях (2, 8, 10, 16).

**🔍 Symbol - абсолютная уникальность:**

```javascript
// 🔍 Каждый Symbol ВСЕГДА уникален
const sym1 = Symbol('description');
const sym2 = Symbol('description');
const sym3 = Symbol('description');

console.log(sym1 === sym2); // false ❌
console.log(sym1 === sym3); // false ❌  
console.log(sym2 === sym3); // false ❌

// Даже с одинаковым описанием - разные символы!
console.log(sym1); // Symbol(description)
console.log(sym2); // Symbol(description) - но НЕ равны!

// 🔍 Единственный способ получить тот же Symbol
const globalSym1 = Symbol.for('global');
const globalSym2 = Symbol.for('global');
console.log(globalSym1 === globalSym2); // true ✅
```

**🔍 Практические применения Symbol:**

```javascript
// 🔍 1. Приватные свойства объектов
const PRIVATE_DATA = Symbol('private');
const SECRET_METHOD = Symbol('secret');

class User {
  constructor(name) {
    this.name = name; // Публичное свойство
    this[PRIVATE_DATA] = { password: '123456' }; // Приватное!
  }
  
  [SECRET_METHOD]() {
    return 'Секретная функция';
  }
  
  getPrivateData() {
    return this[PRIVATE_DATA]; // Доступ только изнутри
  }
}

const user = new User('Иван');
console.log(user.name);         // "Иван" ✅
console.log(user.password);     // undefined ❌ (не видно снаружи)
console.log(user[PRIVATE_DATA]); // { password: '123456' } ✅ (если есть ссылка)

// Не видно в Object.keys()
console.log(Object.keys(user)); // ['name'] (только публичные)

// 🔍 2. Метаданные для библиотек
const TYPE_INFO = Symbol('type');
const VERSION = Symbol('version');

const myObject = {
  data: 'пользовательские данные',
  [TYPE_INFO]: 'User',
  [VERSION]: '1.0.0'
};

// Пользователь видит только data
console.log(Object.keys(myObject)); // ['data']

// Библиотека может использовать метаданные
function processObject(obj) {
  console.log(`Тип: ${obj[TYPE_INFO]}, Версия: ${obj[VERSION]}`);
}

// 🔍 3. Well-known символы (встроенные)
const customIterable = {
  values: [1, 2, 3],
  
  [Symbol.iterator]() {
    let index = 0;
    const values = this.values;
    
    return {
      next() {
        return index < values.length
          ? { value: values[index++], done: false }
          : { done: true };
      }
    };
  }
};

// Теперь можно использовать в for...of
for (const value of customIterable) {
  console.log(value); // 1, 2, 3
}
```

**🔍 Системы счисления - разные способы записи чисел:**

```javascript
// 🔍 Одно число в разных системах
const number = 255;

// Преобразуем В разные системы счисления
console.log(number.toString(2));   // "11111111" (двоичная)
console.log(number.toString(8));   // "377" (восьмеричная)  
console.log(number.toString(10));  // "255" (десятичная)
console.log(number.toString(16));  // "ff" (шестнадцатеричная)

// 🔍 Преобразуем ИЗ разных систем в десятичную
console.log(parseInt('11111111', 2));  // 255 (из двоичной)
console.log(parseInt('377', 8));       // 255 (из восьмеричной)
console.log(parseInt('255', 10));      // 255 (из десятичной)
console.log(parseInt('ff', 16));       // 255 (из шестнадцатеричной)
console.log(parseInt('FF', 16));       // 255 (регистр не важен)

// 🔍 Практические примеры
const colorHex = 'ff5733'; // Цвет в HTML/CSS
const red = parseInt(colorHex.substr(0, 2), 16);   // 255
const green = parseInt(colorHex.substr(2, 2), 16); // 87
const blue = parseInt(colorHex.substr(4, 2), 16);  // 51

console.log(`RGB(${red}, ${green}, ${blue})`); // RGB(255, 87, 51)

// 🔍 Битовые операции
const permissions = 0b101; // Двоичное: читать + выполнять
console.log(permissions.toString(2)); // "101"
console.log(permissions);             // 5 (в десятичной)

// Проверяем права
const READ = 0b001;    // 1
const WRITE = 0b010;   // 2  
const EXECUTE = 0b100; // 4

console.log(permissions & READ);    // 1 (есть право читать)
console.log(permissions & WRITE);   // 0 (нет права писать)
console.log(permissions & EXECUTE); // 4 (есть право выполнять)
```

**🧠 Мнемоника для запоминания:**

- **Symbol** = "**Снежинка**" (каждая уникальна)
- **Системы счисления**:
  - **Binary (2)** = "**Би**нарный = **Два** варианта (0, 1)"
  - **Octal (8)** = "**Окт**опус = **8** ног"
  - **Hex (16)** = "**Хе**кс = **16** = 0-9 + A-F"

**⚡ Полезные приемы:**

```javascript
// 🔍 Быстрые проверки с Symbol
const symbols = new Set();
symbols.add(Symbol('test'));
symbols.add(Symbol('test'));
console.log(symbols.size); // 2 (каждый Symbol уникален)

// 🔍 Удобные функции для систем счисления
function toBinary(num) {
  return '0b' + num.toString(2);
}

function toHex(num) {
  return '0x' + num.toString(16).toUpperCase();
}

function fromAnyBase(str, base) {
  return parseInt(str.replace(/^0[bxo]/i, ''), base);
}

console.log(toBinary(10));        // "0b1010"
console.log(toHex(255));          // "0xFF" 
console.log(fromAnyBase('0xFF', 16)); // 255

// 🔍 Генерация уникальных ID
const createUniqueId = () => Symbol(Date.now());
const id1 = createUniqueId();
const id2 = createUniqueId();
console.log(id1 === id2); // false (всегда уникальны)
```

**Заключение:** Symbol обеспечивает абсолютную уникальность для создания приватных ключей и метаданных. Системы счисления позволяют представлять числа в разных основаниях — полезно для цветов, битовых операций и низкоуровневого программирования.

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

40. **Currying (каррирование) - что это и зачем нужно?**

🎯 **Простое объяснение:** Currying превращает функцию с несколькими параметрами в цепочку функций с одним параметром. Как конструктор: вместо "собрать всё сразу", собираем "по частям".

**🔍 Базовый принцип:**

```javascript
// 🔍 Обычная функция - все параметры сразу
function normalAdd(a, b, c) {
  return a + b + c;
}

console.log(normalAdd(1, 2, 3)); // 6
// ❌ Нужны ВСЕ параметры сразу

// 🔍 Каррированная функция - по одному параметру
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

// Можем вызывать частями!
const add1 = curriedAdd(1);      // Зафиксировали первый параметр
const add1and2 = add1(2);        // Зафиксировали второй параметр  
const result = add1and2(3);      // Получили результат: 6

// Или все сразу
console.log(curriedAdd(1)(2)(3)); // 6

// 🔍 Современный синтаксис со стрелочными функциями
const modernCurriedAdd = a => b => c => a + b + c;
console.log(modernCurriedAdd(1)(2)(3)); // 6
```

**🔍 Практические применения:**

```javascript
// 🔍 1. Создание специализированных функций
const multiply = a => b => a * b;

const double = multiply(2);      // Удваивает число
const triple = multiply(3);      // Утраивает число
const byTen = multiply(10);      // Умножает на 10

console.log(double(5));  // 10
console.log(triple(4));  // 12
console.log(byTen(7));   // 70

// 🔍 2. Конфигурация логгера
const createLogger = level => message => data => {
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] [${level}] ${message}`, data);
};

const logError = createLogger('ERROR');
const logInfo = createLogger('INFO');
const logDebug = createLogger('DEBUG');

// Удобно использовать
logError('Database connection failed', { host: 'localhost', port: 5432 });
logInfo('User logged in', { userId: 123, username: 'ivan' });
logDebug('Cache hit', { key: 'user:123', ttl: 300 });

// 🔍 3. Валидация данных
const createValidator = rule => field => value => {
  switch (rule) {
    case 'required':
      return value ? null : `${field} обязательно для заполнения`;
    case 'email':
      return /\S+@\S+\.\S+/.test(value) ? null : `${field} должен быть email`;
    case 'minLength':
      return value.length >= 3 ? null : `${field} должен быть минимум 3 символа`;
    default:
      return null;
  }
};

const validateRequired = createValidator('required');
const validateEmail = createValidator('email');
const validateMinLength = createValidator('minLength');

// Проверяем поля формы
const validateName = validateRequired('Имя');
const validateUserEmail = validateEmail('Email');
const validatePassword = validateMinLength('Пароль');

console.log(validateName(''));           // "Имя обязательно для заполнения"
console.log(validateUserEmail('test'));  // "Email должен быть email"
console.log(validatePassword('12'));     // "Пароль должен быть минимум 3 символа"
```

**🔍 Универсальная функция curry:**

```javascript
// 🔍 Автоматическое каррирование любой функции
function curry(func) {
  return function curried(...args) {
    // Если получили все аргументы - вызываем функцию
    if (args.length >= func.length) {
      return func.apply(this, args);
    }
    
    // Иначе возвращаем функцию, ожидающую остальные аргументы
    return function(...moreArgs) {
      return curried.apply(this, args.concat(moreArgs));
    };
  };
}

// Применяем к любой функции
const sum = (a, b, c) => a + b + c;
const curriedSum = curry(sum);

// Все эти варианты работают!
console.log(curriedSum(1)(2)(3));    // 6
console.log(curriedSum(1, 2)(3));    // 6  
console.log(curriedSum(1)(2, 3));    // 6
console.log(curriedSum(1, 2, 3));    // 6

// 🔍 Практический пример с фильтрацией
const users = [
  { name: 'Иван', age: 25, role: 'admin' },
  { name: 'Мария', age: 30, role: 'user' },
  { name: 'Петр', age: 35, role: 'admin' }
];

// Каррированные функции для работы с данными
const prop = curry((property, obj) => obj[property]);
const equals = curry((value, item) => item === value);
const filter = curry((predicate, array) => array.filter(predicate));

// Создаем специализированные функции
const getName = prop('name');
const getAge = prop('age');  
const getRole = prop('role');
const isAdmin = equals('admin');
const isUser = equals('user');

// Фильтруем админов
const getAdmins = filter(user => isAdmin(getRole(user)));
const admins = getAdmins(users);
console.log(admins); // [{ name: 'Иван', age: 25, role: 'admin' }, { name: 'Петр', age: 35, role: 'admin' }]
```

**🧠 Мнемоника для запоминания:**

- **Curry** = "**Кур-ри**" = "**По кускам**" (кушать по кускам)
- **Обычная функция** = "**Большая тарелка**" (всё сразу)
- **Каррированная** = "**Маленькие порции**" (по кусочку)

**⚡ Реальные применения:**

```javascript
// 🔍 1. HTTP запросы
const request = curry((method, url, headers, data) => {
  return fetch(url, { method, headers, body: JSON.stringify(data) });
});

const get = request('GET');
const post = request('POST');
const withJsonHeaders = post('/api/users', { 'Content-Type': 'application/json' });

// Использование
get('/api/users')();  // GET запрос
withJsonHeaders({ name: 'Иван', age: 25 }); // POST с данными

// 🔍 2. Математические операции
const mathOp = curry((operation, a, b) => {
  switch (operation) {
    case 'add': return a + b;
    case 'multiply': return a * b;
    case 'divide': return a / b;
    case 'subtract': return a - b;
  }
});

const add = mathOp('add');
const multiply = mathOp('multiply');

const addTen = add(10);
const multiplyByTwo = multiply(2);

console.log(addTen(5));        // 15
console.log(multiplyByTwo(7)); // 14

// 🔍 3. Конфигурация компонентов
const createButton = curry((type, size, color, text) => {
  return `<button class="${type} ${size} ${color}">${text}</button>`;
});

const primaryButton = createButton('primary');
const largeButton = primaryButton('large');
const redButton = largeButton('red');

console.log(redButton('Нажми меня')); // <button class="primary large red">Нажми меня</button>
```

**⚡ Currying vs Partial Application:**

```javascript
// 🔍 Currying - всегда по одному аргументу
const curriedAdd = a => b => c => a + b + c;
curriedAdd(1)(2)(3); // Только так

// 🔍 Partial Application - можно несколько аргументов
function partialAdd(a, b, c) {
  return a + b + c;
}

const partial = partialAdd.bind(null, 1); // Фиксируем первый
console.log(partial(2, 3)); // 6 (передаем сразу два оставшихся)
```

**Заключение:** Currying превращает функции в "конструкторы" - можно собирать функциональность по частям, создавая специализированные версии. Особенно полезно в функциональном программировании для создания переиспользуемых компонентов.

41. **Мемоизация - кэширование для ускорения функций**

🎯 **Простое объяснение:** Мемоизация — это "память" для функций. Функция запоминает свои результаты, чтобы не пересчитывать одно и то же заново.

**🔍 Принцип работы:**

```javascript
// 🔍 Без мемоизации - каждый раз считаем заново
function slowFibonacci(n) {
  console.log(`Считаем fibonacci(${n})`);
  if (n <= 1) return n;
  return slowFibonacci(n - 1) + slowFibonacci(n - 2);
}

// Очень медленно!
console.log(slowFibonacci(10)); // Множество вычислений одних и тех же чисел

// 🔍 С мемоизацией - запоминаем результаты
function memoize(func) {
  const cache = new Map(); // Память функции
  
  return function(...args) {
    const key = JSON.stringify(args); // Ключ для поиска
    
    // Проверяем, есть ли результат в памяти
    if (cache.has(key)) {
      console.log(`📦 Из кэша: ${key}`);
      return cache.get(key);
    }
    
    // Если нет - вычисляем и запоминаем
    console.log(`🔄 Вычисляем: ${key}`);
    const result = func.apply(this, args);
    cache.set(key, result);
    
    return result;
  };
}

// Применяем мемоизацию
const fastFibonacci = memoize(function(n) {
  if (n <= 1) return n;
  return fastFibonacci(n - 1) + fastFibonacci(n - 2);
});

console.log(fastFibonacci(10)); // Быстро! Каждое число считается только один раз
```

**🔍 Практические примеры:**

```javascript
// 🔍 1. Дорогие математические вычисления
const expensiveMath = memoize(function(base, power) {
  console.log(`Вычисляем ${base}^${power}`);
  
  // Симуляция дорогой операции
  let result = 1;
  for (let i = 0; i < power; i++) {
    result *= base;
    // Искусственная задержка
    for (let j = 0; j < 1000000; j++) {}
  }
  
  return result;
});

console.time('Первый вызов');
console.log(expensiveMath(2, 10)); // 1024 - долго
console.timeEnd('Первый вызов');

console.time('Второй вызов');
console.log(expensiveMath(2, 10)); // 1024 - мгновенно!
console.timeEnd('Второй вызов');

// 🔍 2. API запросы
const memoizedFetch = memoize(async function(url) {
  console.log(`🌐 Запрос к: ${url}`);
  const response = await fetch(url);
  return response.json();
});

// Первый запрос пойдет на сервер
const user1 = await memoizedFetch('/api/users/123');

// Второй запрос вернется из кэша
const user2 = await memoizedFetch('/api/users/123'); // Мгновенно!

// 🔍 3. Проверка простых чисел
const isPrime = memoize(function(n) {
  console.log(`Проверяем ${n} на простоту`);
  
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

console.log(isPrime(97));   // Вычисляем
console.log(isPrime(97));   // Из кэша
console.log(isPrime(101));  // Вычисляем
console.log(isPrime(97));   // Из кэша
```

**🔍 Продвинутые техники мемоизации:**

```javascript
// 🔍 1. Мемоизация с ограничением размера кэша (LRU)
function memoizeLRU(func, maxSize = 100) {
  const cache = new Map();
  
  return function(...args) {
    const key = JSON.stringify(args);
    
    if (cache.has(key)) {
      // Перемещаем в конец (недавно использованный)
      const value = cache.get(key);
      cache.delete(key);
      cache.set(key, value);
      return value;
    }
    
    // Удаляем самый старый элемент если кэш переполнен
    if (cache.size >= maxSize) {
      const oldestKey = cache.keys().next().value;
      cache.delete(oldestKey);
      console.log(`🗑️ Удален старый кэш: ${oldestKey}`);
    }
    
    const result = func.apply(this, args);
    cache.set(key, result);
    
    return result;
  };
}

// 🔍 2. Мемоизация с временным истечением (TTL)
function memoizeWithTTL(func, ttlMs = 60000) {
  const cache = new Map();
  
  return function(...args) {
    const key = JSON.stringify(args);
    const now = Date.now();
    
    if (cache.has(key)) {
      const cached = cache.get(key);
      
      // Проверяем не истек ли срок
      if (now - cached.timestamp < ttlMs) {
        console.log(`⏰ Из кэша (${Math.round((now - cached.timestamp) / 1000)}с назад)`);
        return cached.value;
      } else {
        cache.delete(key);
        console.log(`⏰ Кэш истек, удаляем: ${key}`);
      }
    }
    
    const result = func.apply(this, args);
    cache.set(key, {
      value: result,
      timestamp: now
    });
    
    return result;
  };
}

// Пример: кэшируем данные на 5 секунд
const getWeather = memoizeWithTTL(function(city) {
  console.log(`🌤️ Запрашиваем погоду для ${city}`);
  return {
    city,
    temperature: Math.round(Math.random() * 30),
    timestamp: new Date().toLocaleTimeString()
  };
}, 5000);

console.log(getWeather('Москва'));  // Запрос
console.log(getWeather('Москва'));  // Из кэша

// Через 6 секунд
setTimeout(() => {
  console.log(getWeather('Москва')); // Новый запрос (кэш истек)
}, 6000);
```

**🧠 Мнемоника для запоминания:**

- **Memoization** = "**Мемо**ри" (память функции)
- **Cache** = "**Каша**" (склад с результатами)
- **Hit** = "**Хит**" (попадание в кэш)
- **Miss** = "**Мисс**" (промах - нужно вычислять)

**⚡ Когда использовать мемоизацию:**

```javascript
// ✅ ХОРОШИЕ случаи:

// 1. Чистые функции (один вход = один выход)
const pureFunction = memoize((x, y) => x * y + Math.sqrt(x));

// 2. Дорогие вычисления
const expensiveCalc = memoize((data) => {
  // Сложная обработка массива
  return data.map(x => Math.pow(x, 10)).filter(x => x > 1000);
});

// 3. Рекурсивные алгоритмы
const memoizedFactorial = memoize(n => 
  n <= 1 ? 1 : n * memoizedFactorial(n - 1)
);

// 4. API запросы с параметрами
const searchUsers = memoize(query => fetch(`/api/search?q=${query}`));

// ❌ ПЛОХИЕ случаи:

// 1. Функции с побочными эффектами
const badExample1 = memoize(() => {
  console.log('Side effect!'); // ❌ Побочный эффект
  return Math.random(); // ❌ Всегда разные результаты
});

// 2. Функции с редко повторяющимися аргументами
const badExample2 = memoize(timestamp => 
  new Date(timestamp).toLocaleString() // ❌ Каждый timestamp уникален
);

// 3. Когда память критична
const memoryHeavy = memoize(data => 
  data.map(x => new Array(1000000).fill(x)) // ❌ Съедает много памяти
);
```

**⚡ Мемоизация в классах:**

```javascript
// 🔍 Мемоизация методов класса
class DataProcessor {
  constructor() {
    // Мемоизируем метод при создании экземпляра
    this.processData = memoize(this.processData.bind(this));
  }
  
  processData(dataset) {
    console.log(`Обрабатываем ${dataset.length} элементов`);
    
    // Тяжелая обработка
    return dataset
      .filter(x => x > 0)
      .map(x => Math.sqrt(x))
      .sort((a, b) => b - a);
  }
}

const processor = new DataProcessor();
const data = [1, 4, 9, 16, 25];

console.log(processor.processData(data)); // Вычисляем
console.log(processor.processData(data)); // Из кэша
```

**Заключение:** Мемоизация — мощная техника оптимизации для чистых функций с дорогими вычислениями. Превращает медленные функции в быстрые, запоминая результаты. Главное — не злоупотреблять и следить за памятью!

42. **Чейнинг функций - строим цепочки вызовов**

🎯 **Простое объяснение:** Чейнинг — это когда методы объекта возвращают сам объект (`return this`), позволяя вызывать их подряд цепочкой. Как конвейер: результат одного метода сразу передается следующему.

**🔍 Основной принцип:**

```javascript
// 🔍 Без чейнинга - много строк кода
class Calculator {
  constructor(value = 0) {
    this.value = value;
  }
  
  add(num) {
    this.value += num;
    // ❌ Не возвращаем this - нет чейнинга
  }
  
  multiply(num) {
    this.value *= num;
    // ❌ Не возвращаем this - нет чейнинга
  }
  
  result() {
    return this.value;
  }
}

// Использование без чейнинга - неудобно
const calc1 = new Calculator(10);
calc1.add(5);
calc1.multiply(2);
const result1 = calc1.result(); // 30

// 🔍 С чейнингом - красиво и компактно
class ChainableCalculator {
  constructor(value = 0) {
    this.value = value;
  }
  
  add(num) {
    this.value += num;
    return this; // ✅ Возвращаем this для чейнинга
  }
  
  multiply(num) {
    this.value *= num;
    return this; // ✅ Возвращаем this для чейнинга
  }
  
  subtract(num) {
    this.value -= num;
    return this; // ✅ Возвращаем this для чейнинга
  }
  
  result() {
    return this.value; // Финальный результат
  }
}

// Использование с чейнингом - одна строка!
const result2 = new ChainableCalculator(10)
  .add(5)        // 15
  .multiply(2)   // 30  
  .subtract(5)   // 25
  .result();     // 25

console.log(result2); // 25
```

**🔍 Практические примеры:**

```javascript
// 🔍 1. Построение строк
class StringBuilder {
  constructor() {
    this.parts = [];
  }
  
  append(text) {
    this.parts.push(text);
    return this;
  }
  
  prepend(text) {
    this.parts.unshift(text);
    return this;
  }
  
  uppercase() {
    this.parts = this.parts.map(part => part.toUpperCase());
    return this;
  }
  
  wrap(before, after) {
    this.parts.unshift(before);
    this.parts.push(after);
    return this;
  }
  
  toString() {
    return this.parts.join('');
  }
}

// Строим HTML элемент цепочкой
const html = new StringBuilder()
  .append('Hello')
  .append(' ')
  .append('World')
  .uppercase()                    // "HELLO WORLD"
  .wrap('<h1>', '</h1>')          // "<h1>HELLO WORLD</h1>"
  .toString();

console.log(html); // "<h1>HELLO WORLD</h1>"

// 🔍 2. HTTP клиент с настройками
class ApiClient {
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
  
  auth(token) {
    return this.setHeader('Authorization', `Bearer ${token}`);
  }
  
  contentType(type) {
    return this.setHeader('Content-Type', type);
  }
  
  timeout(ms) {
    this.config.timeout = ms;
    return this;
  }
  
  async get(url) {
    const params = new URLSearchParams(this.config.params);
    const fullUrl = `${url}?${params}`;
    
    return fetch(fullUrl, {
      method: 'GET',
      headers: this.config.headers
    });
  }
}

// Настраиваем и отправляем запрос одной цепочкой
const response = await new ApiClient()
  .auth('your-token-123')
  .contentType('application/json')
  .timeout(10000)
  .setParam('page', 1)
  .setParam('limit', 10)
  .get('/api/users');

// 🔍 3. Обработка массивов (функциональный стиль)
class ArrayProcessor {
  constructor(array) {
    this.data = [...array]; // Копируем массив
  }
  
  filter(predicate) {
    this.data = this.data.filter(predicate);
    return this;
  }
  
  map(transform) {
    this.data = this.data.map(transform);
    return this;
  }
  
  sort(compareFn) {
    this.data = this.data.sort(compareFn);
    return this;
  }
  
  unique() {
    this.data = [...new Set(this.data)];
    return this;
  }
  
  take(count) {
    this.data = this.data.slice(0, count);
    return this;
  }
  
  debug(label) {
    console.log(`${label}:`, this.data);
    return this; // Не изменяем данные, только логируем
  }
  
  get() {
    return this.data; // Получаем результат
  }
}

// Обрабатываем данные цепочкой
const numbers = [5, 2, 8, 1, 9, 2, 7, 3, 8, 4];

const processed = new ArrayProcessor(numbers)
  .filter(x => x > 3)           // [5, 8, 9, 7, 8, 4]
  .debug('После фильтра')       // Логируем промежуточный результат
  .map(x => x * 2)              // [10, 16, 18, 14, 16, 8]
  .unique()                     // [10, 16, 18, 14, 8]
  .sort((a, b) => b - a)        // [18, 16, 14, 10, 8]
  .take(3)                      // [18, 16, 14]
  .debug('Финальный результат') // Логируем финал
  .get();

console.log(processed); // [18, 16, 14]
```

**🔍 Чейнинг с условиями:**

```javascript
// 🔍 Условный чейнинг
class ConditionalChain {
  constructor(value) {
    this.value = value;
  }
  
  if(condition, fn) {
    if (condition) {
      this.value = fn(this.value);
    }
    return this;
  }
  
  unless(condition, fn) {
    if (!condition) {
      this.value = fn(this.value);
    }
    return this;
  }
  
  when(condition, fn) {
    return this.if(condition, fn);
  }
  
  get() {
    return this.value;
  }
}

// Условная обработка
const userInput = "  HELLO world  ";

const cleaned = new ConditionalChain(userInput)
  .if(typeof userInput === 'string', str => str.trim())
  .unless(userInput.length === 0, str => str.toLowerCase())
  .when(userInput.includes('hello'), str => str.replace('hello', '👋'))
  .get();

console.log(cleaned); // "👋 world"
```

**🧠 Мнемоника для запоминания:**

- **Chaining** = "**Цепь**" (связываем методы как звенья)
- **return this** = "**Верни себя**" (метод возвращает объект для продолжения)
- **Fluent Interface** = "**Текучий интерфейс**" (код течет как вода)

**⚡ Популярные примеры чейнинга:**

```javascript
// 🔍 1. jQuery стиль (DOM манипуляции)
class SimpleDOM {
  constructor(selector) {
    this.elements = document.querySelectorAll(selector);
  }
  
  addClass(className) {
    this.elements.forEach(el => el.classList.add(className));
    return this;
  }
  
  css(property, value) {
    this.elements.forEach(el => el.style[property] = value);
    return this;
  }
  
  text(content) {
    this.elements.forEach(el => el.textContent = content);
    return this;
  }
  
  on(event, handler) {
    this.elements.forEach(el => el.addEventListener(event, handler));
    return this;
  }
}

// Использование как jQuery
// new SimpleDOM('.buttons')
//   .addClass('btn-primary')
//   .css('padding', '10px')
//   .text('Click me!')
//   .on('click', () => alert('Clicked!'));

// 🔍 2. Promise чейнинг (встроенный в JS)
fetch('/api/users')
  .then(response => response.json())
  .then(users => users.filter(u => u.active))
  .then(activeUsers => console.log(activeUsers))
  .catch(error => console.error(error));

// 🔍 3. Lodash-style чейнинг
class LodashStyle {
  constructor(value) {
    this.value = value;
  }
  
  map(fn) {
    this.value = this.value.map(fn);
    return this;
  }
  
  filter(predicate) {
    this.value = this.value.filter(predicate);
    return this;
  }
  
  sortBy(key) {
    this.value = this.value.sort((a, b) => a[key] - b[key]);
    return this;
  }
  
  value() {
    return this.value; // Завершающий метод
  }
}

const users = [
  { name: 'Иван', age: 25 },
  { name: 'Мария', age: 30 },
  { name: 'Петр', age: 20 }
];

const result = new LodashStyle(users)
  .filter(user => user.age > 21)
  .sortBy('age')
  .map(user => user.name)
  .value();

console.log(result); // ['Иван', 'Мария']
```

**⚡ Преимущества и недостатки:**

```javascript
// ✅ Преимущества чейнинга:

// 1. Читаемость - код читается как история
const story = new StringBuilder()
  .append('Жил-был')
  .append(' ')
  .append('программист')
  .wrap('«', '»')
  .toString(); // "«Жил-был программист»"

// 2. Компактность - меньше переменных
const result = new Calculator(100)
  .subtract(20)   // 80
  .divide(4)      // 20
  .add(5)         // 25
  .result();

// 3. Неизменяемость (если копировать данные)
const original = [1, 2, 3];
const processed = new ArrayProcessor(original)
  .map(x => x * 2)
  .filter(x => x > 4)
  .get(); // [6] (original не изменился)

// ❌ Недостатки чейнинга:

// 1. Сложность отладки
const debugResult = new Calculator(10)
  .add(5)       // Где ошибка?
  .multiply(0)  // Здесь?
  .divide(2)    // Или здесь?
  .result();    // NaN - но где проблема?

// 2. Принуждение к возврату this
class BadExample {
  process() {
    // Хотим вернуть результат, но нужно this для чейнинга
    const result = this.heavyCalculation();
    return this; // Теряем результат!
  }
}

// 3. Все методы должны быть chainable
class Mixed {
  chainableMethod() {
    return this; // Для чейнинга
  }
  
  getValue() {
    return this.value; // Прерывает цепочку
  }
}
```

**Заключение:** Чейнинг делает код элегантным и читаемым, позволяя выстраивать операции в логическую последовательность. Ключ — каждый метод возвращает `this`. Популярен в библиотеках (jQuery, Lodash) и современном JavaScript (Promise, Array methods).

43. **Function vs Arrow Function - в чем ключевые отличия?**

🎯 **Простое объяснение:** Обычные функции и стрелочные — это два разных способа создания функций в JavaScript. Главное отличие — как они работают с `this` и что у них есть/нет "из коробки".

**🔍 Основные различия:**

```javascript
// 🔍 1. СИНТАКСИС - короче записать
// Обычная функция - больше букв
function normalAdd(a, b) {
  return a + b;
}

// Стрелочная функция - короче
const arrowAdd = (a, b) => a + b;

// Ещё короче для одного параметра
const double = x => x * 2;
const sayHello = () => 'Hello!';

// 🔍 2. THIS CONTEXT - главное отличие!
const person = {
  name: 'Иван',
  
  // Обычная функция - this меняется в зависимости от вызова
  sayNameNormal: function() {
    console.log(`Меня зовут ${this.name}`); // 'Иван'
    
    // Проблема: this теряется в колбэке
    setTimeout(function() {
      console.log(`Привет от ${this.name}`); // undefined (this = window)
    }, 1000);
  },
  
  // Стрелочная функция - this НЕ меняется
  sayNameArrow: function() {
    console.log(`Меня зовут ${this.name}`); // 'Иван'
    
    // Решение: this сохраняется в стрелочной функции
    setTimeout(() => {
      console.log(`Привет от ${this.name}`); // 'Иван' (this сохранился!)
    }, 1000);
  },
  
  // ❌ Плохо: стрелочная функция как метод
  wrongMethod: () => {
    console.log(this.name); // undefined (this = window, а не person)
  }
};

// 🔍 3. ARGUMENTS - есть/нет
function withArguments() {
  console.log(arguments); // [1, 2, 3] - псевдомассив
  console.log(arguments.length); // 3
}

const withoutArguments = () => {
  // console.log(arguments); // ❌ ReferenceError!
};

// Для стрелочных функций используйте rest parameters
const withRest = (...args) => {
  console.log(args); // [1, 2, 3] - настоящий массив
  console.log(args.length); // 3
};

withArguments(1, 2, 3);
withRest(1, 2, 3);

// 🔍 4. КОНСТРУКТОРЫ - можно/нельзя
function NormalConstructor(name) {
  this.name = name;
}

const user1 = new NormalConstructor('Петр'); // ✅ Работает
console.log(user1.name); // 'Петр'

const ArrowConstructor = (name) => {
  this.name = name;
};

// const user2 = new ArrowConstructor('Мария'); // ❌ TypeError!

// 🔍 5. HOISTING - поднимается/не поднимается
console.log(hoistedFunc()); // ✅ 'Я поднялся!' (работает до объявления)

function hoistedFunc() {
  return 'Я поднялся!';
}

// console.log(notHoisted()); // ❌ ReferenceError!

const notHoisted = () => 'Я не поднялся!';
```

**🔍 Практические применения:**

```javascript
// 🔍 1. Event handlers - where this matters
class Counter {
  constructor() {
    this.count = 0;
    this.button = document.querySelector('#myButton');
  }
  
  // ❌ Проблема с обычной функцией
  bindWrong() {
    this.button.addEventListener('click', function() {
      this.count++; // ❌ this = button element, а не Counter!
      console.log(this.count); // undefined
    });
  }
  
  // ✅ Решение со стрелочной функцией
  bindCorrect() {
    this.button.addEventListener('click', () => {
      this.count++; // ✅ this = Counter instance
      console.log(this.count); // 1, 2, 3...
    });
  }
  
  // ✅ Альтернатива с bind
  bindAlternative() {
    this.button.addEventListener('click', function() {
      this.count++;
      console.log(this.count);
    }.bind(this)); // Привязываем this принудительно
  }
}

// 🔍 2. Array methods - стрелочные функции везде!
const numbers = [1, 2, 3, 4, 5];

// Коротко и красиво
const doubled = numbers.map(x => x * 2);        // [2, 4, 6, 8, 10]
const evens = numbers.filter(x => x % 2 === 0); // [2, 4]
const sum = numbers.reduce((acc, x) => acc + x, 0); // 15

// Vs обычные функции - громоздко
const doubledOld = numbers.map(function(x) { return x * 2; });

// 🔍 3. Async/await - стрелочные функции удобнее
const users = ['user1', 'user2', 'user3'];

// Получаем данные пользователей
const fetchUserData = async () => {
  const results = await Promise.all(
    users.map(async user => {
      const response = await fetch(`/api/users/${user}`);
      return response.json();
    })
  );
  return results;
};

// 🔍 4. React/Vue компоненты - часто используют стрелочные
class ReactComponent {
  constructor() {
    this.state = { clicked: false };
  }
  
  // ✅ Стрелочная функция - this всегда правильный
  handleClick = () => {
    this.setState({ clicked: true });
  }
  
  // ❌ Обычная функция - нужен bind в render
  handleClickWrong() {
    this.setState({ clicked: true });
  }
  
  render() {
    return `<button onclick="${this.handleClick}">Click me</button>`;
    // При обычной функции: onclick="${this.handleClickWrong.bind(this)}"
  }
}
```

**🧠 Мнемоника для запоминания:**

- **Arrow function** = "**Стрелка**" (точно в цель - сохраняет this)
- **Regular function** = "**Хамелеон**" (this меняется в зависимости от контекста)
- **this в стрелочной** = "**Наследует от родителя**"
- **this в обычной** = "**Смотрит кто вызвал**"

**⚡ Когда что использовать:**

```javascript
// ✅ СТРЕЛОЧНЫЕ ФУНКЦИИ используйте для:

// 1. Коллбэков в массивах
[1, 2, 3].map(x => x * 2)
[1, 2, 3].filter(x => x > 1)
[1, 2, 3].reduce((acc, x) => acc + x)

// 2. Event handlers в классах
button.addEventListener('click', () => this.handleClick())

// 3. Коротких функций
const add = (a, b) => a + b
const isEven = n => n % 2 === 0

// 4. Асинхронных коллбэков
setTimeout(() => console.log('Hello'), 1000)
fetch('/api').then(res => res.json())

// ✅ ОБЫЧНЫЕ ФУНКЦИИ используйте для:

// 1. Методов объектов
const obj = {
  name: 'Test',
  getName: function() { return this.name; } // ✅ this = obj
}

// 2. Конструкторов
function User(name) {
  this.name = name; // ✅ this = новый объект
}

// 3. Когда нужен arguments
function sum() {
  return Array.from(arguments).reduce((a, b) => a + b);
}

// 4. Event handlers где this = element
button.addEventListener('click', function() {
  this.style.color = 'red'; // this = button
});

// ❌ ИЗБЕГАЙТЕ:

// Стрелочные функции как методы объектов
const badObj = {
  name: 'Test',
  getName: () => this.name // ❌ this = window, а не badObj
};

// Обычные функции в коллбэках классов
class BadClass {
  method() {
    setTimeout(function() {
      this.doSomething(); // ❌ this = window, а не BadClass
    }, 1000);
  }
}
```

**⚡ Разбор кода из вопроса:**

```javascript
// pluckDeep - извлекает вложенные свойства
const pluckDeep = key => obj => key.split('.').reduce((accum, key) => accum[key], obj);

// Использование каррирования со стрелочными функциями
const user = { profile: { personal: { name: 'Иван' } } };
const getName = pluckDeep('profile.personal.name');
console.log(getName(user)); // 'Иван'

// compose - композиция функций
const compose = (...fns) => res => fns.reduce((accum, next) => next(accum), res);

const add1 = x => x + 1;
const multiply2 = x => x * 2;  
const subtract3 = x => x - 3;

// Функции выполняются справа налево
const composed = compose(subtract3, multiply2, add1);
console.log(composed(5)); // (5 + 1) * 2 - 3 = 9

// unfold - генератор последовательностей
const unfold = (f, seed) => {
  const go = (f, seed, acc) => {
    const res = f(seed);
    return res ? go(f, res[1], acc.concat([res[0]])) : acc;
  };
  return go(f, seed, []);
};

// Генерируем числа от 1 до 5
const range = unfold(n => n <= 5 ? [n, n + 1] : null, 1);
console.log(range); // [1, 2, 3, 4, 5]
```

**Заключение:** Стрелочные функции проще синтаксически и сохраняют `this` из внешнего контекста. Обычные функции более гибкие и имеют собственный `this`. Выбор зависит от задачи: стрелочные для коллбэков и коротких функций, обычные для методов и конструкторов.
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