## @Transactional
- [Коротко](#коротко)
- [При вызове такого метода происходит следующее](#при-вызове-такого-метода-происходит-следующее)
- [Что произойдёт, если один метод с @Transactional вызовет другой метод с @Transactional?](#что-произойдёт-если-один-метод-с-transactional-вызовет-другой-метод-с-transactional)
- [Что произойдёт, если один метод БЕЗ @Transactional вызовет другой метод с @Transactional?](#что-произойдёт-если-один-метод-без-transactional-вызовет-другой-метод-с-transactional)
- [Будет ли транзакция откачена, если будет брошено исключение, которое указано в контракте метода?](#будет-ли-транзакция-откачена-если-будет-брошено-исключение-которое-указано-в-контракте-метода)
- [Значения атрибута propagation у аннотации](#значения-атрибута-propagation-у-аннотации)
- [Остальные атрибуты](#остальные-атрибуты)
- [Внутри одного класса два метода с аннотацией @transactional. Если один метод вызывает другой, то сколько транзакций будет открыто? Как это исправить?](#внутри-одного-класса-два-метода-с-аннотацией-transactional-если-один-метод-вызывает-другой-то-сколько-транзакций-будет-открыто-как-это-исправить)
- [Такой же пример, но с логами. Аннотация с логами над методами. Один взывает другой, будут ли записаны все логи?](#такой-же-пример-но-с-логами-аннотация-с-логами-над-методами-один-взывает-другой-будут-ли-записаны-все-логи)
- [Подробно](#подробно)

## Коротко
Spring создает прокси для всех классов, помеченных @Transactional (либо если любой из методов класса помечен этой аннотацией), что позволяет вводить транзакционную логику до и после вызываемого метода. 

[к оглавлению](#transactional)

## При вызове такого метода происходит следующее:
- proxy, который создал Spring, создаёт persistence context (или соединение с базой),
- открывает в нём транзакцию и сохраняет всё это в контексте нити исполнения (натурально, в ThreadLocal).
- По мере надобности всё сохранённое достаётся и внедряется в бины.
    
Таким образом, если в вашем коде есть несколько параллельных нитей, у вас будет и несколько параллельных транзакций, которые будут взаимодействовать друг с другом согласно уровням изоляции.

[к оглавлению](#transactional)
    
## Что произойдёт, если один метод с @Transactional вызовет другой метод с @Transactional?     
Если это происходит в рамках одного сервиса, то второй транзакционный метод будет считаться частью первого, так как вызван у него изнутри, а так как спринг не знает о внутреннем вызове, то не создаст прокси для второго метода.

[к оглавлению](#transactional)

## Что произойдёт, если один метод БЕЗ @Transactional вызовет другой метод с @Transactional?
Так как spring не знает о внутреннем вызове, то не создаст прокси для второго метода.

[к оглавлению](#transactional)

## Будет ли транзакция откачена, если будет брошено исключение, которое указано в контракте метода?
Если в контракте описано это исключение, то она не откатится. Unchecked исключения в транзакционном методе можно ловить, а можно и не ловить. 

[к оглавлению](#transactional)

## Значения атрибута propagation у аннотации
- REQUIRED — применяется по умолчанию. При входе в @Transactional метод будет использована уже существующая транзакция или создана новая транзакция, если никакой ещё нет
- REQUIRES_NEW — новая транзакция всегда создаётся при входе в метод, ранее созданные транзакции приостанавливаются до момента возврата из метода. 
- NESTED — корректно работает только с базами данных, которые умеют savepoints. При входе в метод в уже существующей транзакции создаётся savepoint, который по результатам выполнения метода будет либо сохранён, либо откачен. Все изменения, внесённые методом, подтвердятся только позднее, с подтверждением всей транзакции. Если текущей транзакции не существует, будет создана новая.
- MANDATORY — всегда используется существующая транзакция и кидается исключение, если текущей транзакции нет.
- SUPPORTS — метод с этим правилом будет использовать текущую транзакцию, если она есть, либо будет исполнятся без транзакции, если её нет.
- NOT_SUPPORTED — при входе в метод текущая транзакция, если она есть, будет приостановлена и метод будет выполняться без транзакции.
- NEVER — явно запрещает исполнение в контексте транзакции. Если при входе в метод будет существовать транзакция, будет выброшено исключение.

Propagation работает только если метод вызывает другой метод в другом сервисе. Если метод вызывает другой метод в этом же сервисе, то используется this и вызов проходит мимо прокси. Это ограничение можно обойти при помощи self-injection. 

[к оглавлению](#transactional)

## Остальные атрибуты
- rollbackFor = Exception.class - если какой-либо метод выбрасывает указанное исключение, контейнер всегда откатывает текущую транзакцию. По умолчанию отлавливает RuntimeExceptionnoRollbackFor = Exception.class - указание того, что любое исключение, кроме заданных, должно приводить к откату транзакции.
- rollbackForClassName и noRollbackForClassName - для задания имен исключений в строковом виде.
- readOnly - разрешает только операции чтения.
- transactionManager - хранится ссылка на менеджер транзакций, определенный в конфигурации Spring.
- timeOut - По умолчанию используется таймаут, установленный по умолчанию для базовой транзакционной системы. Сообщает менеджеру tx о продолжительности времени, чтобы дождаться простоя tx, прежде чем принять решение об откате не отвечающих транзакций.
- isolation - уровень изолированности транзакций

[к оглавлению](#transactional)

## Внутри одного класса два метода с аннотацией @transactional. Если один метод вызывает другой, то сколько транзакций будет открыто? Как это исправить?
Eсли внутри транзакционного метода вызывается другой транзакционный метод того же объекта, то этот внутренний вызов не будет обернут в новую транзакцию, а будет выполняться в рамках уже существующей транзакции (вызван с помощью this.).

Это ограничение можно обойти при помощи self-injection. 

[к оглавлению](#transactional)

## Такой же пример, но с логами. Аннотация с логами над методами. Один взывает другой, будут ли записаны все логи?

Скорее да?? Хотя скорее нет, тут может выйти такая же проблема, как и у @Transactional, потому что вызывается с помощью this и идет игнорирование самой аннотации, если в ней не прописано, чтобы прошел обязательный вызов, как и в предыдущем примере

[к оглавлению](#transactional)

## Подробно
Для работы с транзакциями Spring Framework использует AOP-прокси.
Для включения возможности управления транзакциями нужно разместить аннотацию @EnableTransactionManagement у класса конфигурации @Configuration. Она означает, что классы, помеченные @Transactional, должны быть обернуты аспектом транзакций. Отвечает за регистрацию необходимых компонентов Spring, таких как TransactionInterceptor и советы прокси. Регистрируемые компоненты помещают перехватчик в стек вызовов при вызове методов @Transactional. 

Если мы используем Spring Boot и имеем зависимости spring-data-* или spring-tx, то управление транзакциями будет включено по умолчанию.

Слой логики(Service) - лучшее место для @Transactional.Помечая @Transactional класс @Service, то все его методы станут транзакционными. Так, при вызове, например, метода save() произойдет примерно следующее:
1. Вначале мы имеем:
- класс TransactionInterceptor, у которого вызывается метод invoke(...), внутри которого вызывается метод класса-родителя TransactionAspectSupport: nvokeWithinTransaction(...), в рамках которого происходит магия транзакций.
- TransactionManager: решает, создавать ли новый EntityManager и/или транзакцию.
- EntityManager proxy: EntityManager - это интерфейс, и то, что внедряется в бин в слое DAO на самом деле не является реализацией EntityManager. В это поле внедряется EntityManager proxy, который будет перехватывать обращение к полю EntityManager и делегировать выполнение конкретному EntityManager в рантайме. Обычно EntityManager proxy представлен классом SharedEntityManagerInvocationHandler.

2. Transaction Interceptor
- В TransactionInterceptor отработает код до работы метода save(), в котором будет определено, выполнить ли метод save() в пределах уже существующей транзакции БД или должна стартовать новая отдельная транзакция. TransactionInterceptor сам не содержит логики по принятию решения, решение начать новую транзакцию, если это нужно, делегируется TransactionManager. Грубо говоря, на данном этапе наш метод будет обёрнут в try-catch и будет добавлена логика до его вызова и после:

```java
    try {
        transaction.begin(); // логика до
        service.save(); 
        transaction.commit(); // логика после
    } catch(Exception ex) {
        transaction.rollback();
        throw ex;
    }
```

3. TransactionManager
- Менеджер транзакций должен предоставить ответ на два вопроса:
    - Должен ли создаться новый EntityManager?
    - Должна ли стартовать новая транзакция БД?

    Решение принимается, основываясь на следующих фактах:
    - выполняется ли хоть одна транзакция в текущий момент или нет;
    - атрибута «propagation» в @Transactional. Если TransactionManager решил создать новую транзакцию, тогда:
        - Создается новый EntityManager;
        - EntityManager «привязывается» к текущему потоку (Thread);
        - «Получается» соединение из пула соединений БД; 
        - Соединение «привязывается» к текущему потоку. 
        
        (И EntityManager и это соединение привязываются к текущему потоку, используя переменные ThreadLocal.)
        
4. EntityManager proxy
- Когда метод save() слоя Service делает вызов метода save() слоя DAO, внутри которого вызывается, например, entityManager.persist(), то не происходит вызов метода persist() напрямую у EntityManager, записанного в поле класса DAO. Вместо этого метод вызывает EntityManager proxy, который достает текущий EntityManager для нашего потока, и у него вызывается метод persist().
5. Отрабатывает DAO-метод save().
6. TransactionInterceptor
- Отработает код после работы метода save(), а именно будет принято решение по коммиту/откату транзакции.
- Кроме того, если мы в рамках одного метода сервиса обращаемся не только к методу save(), а к разным методам Service и DAO, то все они буду работать в рамках одной транзакции, которая оборачивает этот метод сервиса.

Вся работа происходит через прокси-объекты разных классов. 

Представим, что у нас в классе сервиса только один метод с аннотацией @Transactional, а остальные нет. Если мы вызовем метод с @Transactional, из которого вызовем метод без @Transactional, то оба будут отработаны в рамках прокси и будут обернуты в нашу транзакционную логику. Однако, если мы вызовем метод без @Transactional, из которого вызовем метод с @Transactional, то они уже не будут работать в рамках прокси и не будут обернуты в нашу транзакционную логику.

[к оглавлению](#transactional)

# Источники
- [Java Spring Extended](https://quizlet.com/ru/647407292/java-spring-extended-flash-cards/)
