# Trees
- [Красно-черные деревья](#красно-черные-деревья)

## Красно-черные деревья
Свойства общие:
1) Оба поддерева являются бинарными деревьями поиска.
2) Для каждого узла с ключом $k$ выполняется критерий упорядочения:
> ключи всех левых потомков <= $ k $ < ключи всех правых потомков

>Это неравенство должно быть истинным для всех потомков узла, а не только его дочерних узлов.

Свойства красно-черного:
1) Каждый узел окрашен либо в красный, либо в черный цвет (в структуре данных узла появляется дополнительное поле – бит цвета).
2) Корень окрашен в черный цвет.
3) Листья(так называемые NULL-узлы) окрашены в черный цвет.
4) Каждый красный узел должен иметь два черных дочерних узла. 
> Нужно отметить, что у черного узла могут быть черные дочерние узлы. Красные узлы в качестве дочерних могут иметь только черные.
5) Пути от узла к его листьям должны содержать одинаковое количество черных узлов (это черная высота).

### Ну и почему такое дерево является сбалансированным?

Действительно, красно-черные деревья не гарантируют строгой сбалансированности (разница высот двух поддеревьев любого узла не должна превышать 1), как в АВЛ-деревьях. Но соблюдение свойств красно-черного дерева позволяет обеспечить выполнение операций вставки, удаления и выборки за время $O(log N)$. И сейчас посмотрим, действительно ли это так.

Пусть у нас есть красно-черное дерево. Черная высота равна $bh$(black height).

Если путь от корневого узла до листового содержит минимальное количество красных узлов (т.е. ноль), значит этот путь равен $bh$.

Если же путь содержит максимальное количество красных узлов ($bh$ в соответствии со свойством $4$), то этот путь будет равен $2bh$.

То есть, пути из корня к листьям могут различаться не более, чем вдвое ($h<=2log(N + 1)$, где h — высота поддерева), этого достаточно, чтобы время выполнения операций в таком дереве было $O(log N)$

### Как производится вставка?

Вставка в красно-черное дерево начинается со вставки элемента, как в обычном бинарном дереве поиска. Только здесь элементы вставляются в позиции NULL-листьев. Вставленный узел всегда окрашивается в красный цвет. Далее идет процедура проверки сохранения свойств красно-черного дерева $1-5$.

Свойство 1 не нарушается, поскольку новому узлу сразу присваивается красный цвет.

Свойство 2 нарушается только в том случае, если у нас было пустое дерево и первый вставленный узел (он же корень) окрашен в красный цвет. Здесь достаточно просто перекрасить корень в черный цвет.

Свойство 3 также не нарушается, поскольку при добавлении узла он получает черные листовые NULL-узлы.

В основном встречаются 2 других нарушения:

1) Красный узел имеет красный дочерний узел (нарушено свойство $4$).

2) Пути в дереве содержат разное количество черных узлов (нарушено свойство $5$).

Подробнее о балансировке красно-черного дерева при разных случаях (их пять, если включить нарушение свойства $2$) можно почитать на [wiki](https://ru.wikipedia.org/wiki/%D0%9A%D1%80%D0%B0%D1%81%D0%BD%D0%BE-%D1%87%D1%91%D1%80%D0%BD%D0%BE%D0%B5_%D0%B4%D0%B5%D1%80%D0%B5%D0%B2%D0%BE).