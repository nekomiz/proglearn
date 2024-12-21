# Mockito
- [Mockito это?]()
- [Примеры]()


## Mockito это?
Mockito - это библиотека для создания фиктивных объектов (mocks) в тестировании на Java. Она предоставляет инструменты для имитации зависимостей и методов, что позволяет сосредоточиться на тестировании определенных частей кода без влияния внешних зависимостей. Mockito упрощает процесс создания mock-объектов и проверки вызовов методов, что делает его популярным инструментом для автоматизированного тестирования в среде Java.

## Примеры
Примеры использования Mockito могут включать:

1. **Создание фиктивного объекта**:
   ```java
   import org.mockito.Mockito;

   public class MyTestClass {
       private SomeService service;

       @Before
       public void setup() {
           this.service = Mockito.mock(SomeService.class);
       }

       @Test
       public void testMethodWithMock() {
           Mockito.when(service.someMethod()).thenReturn(expectedResult);
           assertEquals(expectedResult, someOtherMethod(arguments));
       }
   }
   ```

2. **Проверка вызовов методов**:
   ```java
   import org.mockito.ArgumentCaptor;
   import org.mockito.Mockito;

   public class MyTestClass {
       private SomeService service;

       @Before
       public void setup() {
           this.service = Mockito.mock(SomeService.class);
       }

       @Test
       public void testMethodInvocations() {
           ArgumentCaptor<SomeInterface> argument = ArgumentCaptor.forClass(SomeInterface.class);
           Mockito.verify(service).someMethod(argument.capture());
           assertEquals(expectedArgument, argument.getValue());
       }
   }
   ```

3. **Сравнение вызовов методов**:
   ```java
   import org.mockito.InOrder;
   import org.mockito.Mockito;

   public class MyTestClass {
       private SomeService service;

       @Before
       public void setup() {
           this.service = Mockito.mock(SomeService.class);
       }

       @Test
       public void testMethodInvocationOrder() {
           InOrder inOrder = Mockito.inOrder(service);
           inOrder.verify(service).someMethod(arg1);
           inOrder.verify(service).anotherMethod(arg2);
       }
   }
   ```

Эти примеры показывают, как Mockito используется для создания и проверки фиктивных объектов, а также для сравнения порядка вызовов методов.
