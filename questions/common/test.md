# Проверочные вопросы - Общие

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