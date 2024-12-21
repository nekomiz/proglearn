# SLF4J
- [SLF4J это?]()
- [Пример использования]()

## SLF4J это?
SLF4J (Simple Logging Facade for Java) - это абстрактный слой поверх различных систем логирования, который предоставляет единый API для работы с журналами. Основная цель SLF4J - обеспечить плавный переход между различными системами логирования без необходимости менять весь код при смене системы логирования. SLF4J может использоваться вместе с такими библиотеками, как Logback, Log4j 1.2 и Jakarta Commons Logging.

## Пример использования
Вот несколько примеров использования SLF4J:

1. **Логирование ошибок**:
   ```java
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   public class ErrorLoggingExample {
       private static final Logger logger = LoggerFactory.getLogger(ErrorLoggingExample.class);

       public void logError() {
           try {
               // Логический блок кода
           } catch (Exception ex) {
               logger.error("Ошибка: ", ex);
           }
       }
   }
   ```

2. **Логирование предупреждений**:
   ```java
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   public class WarningLoggingExample {
       private static final Logger logger = LoggerFactory.getLogger(WarningLoggingExample.class);

       public void logWarning() {
           logger.warn("Предупреждение: ");
       }
   }
   ```

3. **Логирование информационных сообщений**:
   ```java
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   public class InfoLoggingExample {
       private static final Logger logger = LoggerFactory.getLogger(InfoLoggingExample.class);

       public void logInfo() {
           logger.info("Информационное сообщение: ");
       }
   }
   ```

4. **Логирование успешных действий**:
   ```java
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   public class SuccessLoggingExample {
       private static final Logger logger = LoggerFactory.getLogger(SuccessLoggingExample.class);

       public void logSuccess() {
           logger.info("Успешное действие: ");
       }
   }
   ```

Эти примеры демонстрируют, как использовать SLF4J для различных уровней логирования, от ошибок до успешных действий.
