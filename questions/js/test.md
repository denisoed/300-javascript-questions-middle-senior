# Проверочные вопросы - JS Core

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