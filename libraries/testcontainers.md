# Testcontainers
- [Что это?](#что-это)

## Что это?
Testcontainers — это библиотека с открытым исходным кодом для Java, которая позволяет запускать контейнеры Docker во время выполнения тестов. Она предназначена для облегчения тестирования интеграций с внешними зависимостями, такими как базы данных, брокеры сообщений, веб-серверы и другие сервисы. Testcontainers автоматизирует создание, настройку и удаление контейнеров, что делает тестирование более надежным и воспроизводимым.

### Основные особенности Testcontainers:

1. **Поддержка различных сервисов**:
   Testcontainers поддерживает широкий спектр сервисов, включая базы данных (PostgreSQL, MySQL, MongoDB и др.), брокеров сообщений (RabbitMQ, Kafka), поисковые движки (Elasticsearch) и многие другие.

2. **Автоматическое управление контейнерами**:
   Библиотека автоматически создает и удаляет контейнеры перед началом и после окончания тестов, освобождая разработчика от ручных операций.

3. **Интеграция с популярными фреймворками**:
   Testcontainers хорошо интегрируется с популярными фреймворками для тестирования, такими как JUnit, Spock и TestNG.

4. **Настройка окружения**:
   Позволяет конфигурировать параметры запуска контейнеров, такие как версии образов, переменные окружения, монтирование файлов и папок.

### Пример использования Testcontainers

Допустим, нам нужно протестировать приложение, которое использует PostgreSQL. Вот пример теста с использованием Testcontainers и JUnit 5:

```java
import org.junit.jupiter.api.Test;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;

@Testcontainers
public class DatabaseIntegrationTest {
    
    @Container
    private static final PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:latest");

    @Test
    void testDatabaseConnection() throws SQLException {
        String jdbcUrl = postgres.getJdbcUrl();
        String username = postgres.getUsername();
        String password = postgres.getPassword();
        
        try (Connection conn = DriverManager.getConnection(jdbcUrl, username, password)) {
            Statement stmt = conn.createStatement();
            ResultSet rs = stmt.executeQuery("SELECT 1");
            
            assertTrue(rs.next());
            assertEquals(1, rs.getInt(1));
        }
    }
}
```

В этом примере мы создаем контейнер PostgreSQL с помощью аннотации `@Container`, а затем используем его для подключения к базе данных и выполнения простого запроса.

### Преимущества использования Testcontainers:

- **Устойчивость тестов**: Тесты становятся менее подвержены проблемам с окружением, так как все зависимости управляются автоматически.
- **Воспроизводимость**: Результаты тестов одинаковы на любой машине, так как все зависимости запускаются в контейнерах.
- **Минимум ручной настройки**: Разработчику не нужно вручную устанавливать и настраивать внешние сервисы.
- **Совместимость с CI/CD**: Testcontainers отлично подходит для использования в конвейерах непрерывной интеграции и доставки (CI/CD).

### Заключение

Testcontainers — это мощный инструмент для автоматизации тестирования интеграций с внешними зависимостями. Он значительно упрощает процесс написания надежных и воспроизводимых тестов, делая разработку более эффективной и предсказуемой.
