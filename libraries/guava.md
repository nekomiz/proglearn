# Guava
-[Guava это?]()
-[Основные утилиты]()
-[В каких ситуациях лучше использовать guava, а в каких apache commons?]()

## Guava это?
Guava - это библиотека с открытым исходным кодом, 
предоставляющая широкий набор вспомогательных классов для 
проектов на Java. Она включает в себя множество полезных утилит, 
таких как функции коллекций, вспомогательные классы для работы с временем, 
безопасные и быстрые структуры данных, методы сравнения, а также множество 
других функций. Guava широко используется в разработке Java-проектов 
благодаря своей гибкости и эффективности.

## Основные утилиты
Основные утилиты в Guava включают:

1. Функции коллекций:
   - Utility classes for common operations on collections like lists, sets, maps, etc.
   - Helper methods for manipulating elements within collections.

2. Время и даты:
   - Classes and methods for handling time and date operations.
   - Convenient methods for formatting dates and times.

3. Безопасные и быстрые структуры данных:
   - Immutable data structures like Tuples, HashMaps, Sets, etc.
   - Fast and efficient implementation of these data structures.

4. Методы сравнения:
   - Tools for comparing objects and values.
   - Comparison methods that are type-safe and performant.

5. Дополнительные функции:
   - Miscellaneous utilities like validation, iterators, preconditions, and more.
   - Useful methods for various programming tasks.


## В каких ситуациях лучше использовать guava, а в каких apache commons?
Выбор между Guava и Apache Commons может зависеть от ваших конкретных потребностей и предпочтений. Вот несколько общих рекомендаций:

**Guava:**
- Используйте Guava, если вам нужен единый, хорошо интегрированный набор вспомогательных классов для широкого спектра задач.
- Guava предлагает удобные и быстрые структуры данных, такие как ImmutableList, ImmutableSet, и другие.
- Если важна высокая производительность и отсутствие зависимости от внешних библиотек, Guava может быть хорошим выбором.

**Apache Commons:**
- Apache Commons подходит, если вам нужна гибкость выбора отдельных библиотек для конкретных задач.
- Выбирайте Apache Commons, если у вас есть специфические потребности в разных областях разработки, таких как логирование, сетевое взаимодействие, обработка файлов, дата и время, сравнение и другие.
- Для исторических проектов, которые используют старые версии Java, где нет встроенных функций, таких как потоковое ввода-вывода, Apache Commons может быть подходящим вариантом.

**Общие рекомендации:**
- В большинстве случаев Guava предпочтителен из-за своей унифицированной структуры и хорошо документированных классов.
- Если у вас есть узконаправленные задачи, Apache Commons может предложить специализированные библиотеки для конкретных нужд.
- Оба проекта активно поддерживаются и имеют обширное сообщество, поэтому выбор часто зависит от личных предпочтений и удобства использования.
