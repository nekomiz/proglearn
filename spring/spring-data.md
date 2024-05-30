# Spring Data
- [Что это?](#что-это)
- [Конфигурация Spring Data](#конфигурация-spring-data)



## Что это?
>Spring Data — дополнительный удобный механизм для взаимодействия с сущностями базы данных, организации их в репозитории, извлечение данных, изменение, в каких то случаях для этого будет достаточно объявить интерфейс и метод в нем, без имплементации.

+ __CrudRepository__ - обеспечивает функции CRUD
+ __PagingAndSortingRepository__ - предоставляет методы для разбивки на страницы и сортировки записей
+ __JpaRepository__ - предоставляет связанные с JPA методы. При этом JpaRepository содержит полный API CrudRepository и PagingAndSortingRepository

__Репозиторий__ -  основное понятие в Spring Data. Это несколько интерфейсов которые используют JPA Entity для взаимодействия с ней. Так например интерфейс ( _public interface CrudRepository<T, ID extends Serializable> extends Repository<T, ID>_ ) обеспечивает основные операции по поиску, сохранения, удалению данных (CRUD операции).

Т.е. если того перечня что предоставляет интерфейс достаточно для взаимодействия с сущностью, то можно прямо расширить базовый интерфейс для своей сущности, дополнить его своими методами запросов и выполнять операции.

Понятно что этого перечня, скорее всего не хватит для взаимодействия с сущностью, и тут можно расширить свой интерфейс дополнительными методами запросов. Запросы к сущности можно строить прямо из имени метода. Для этого используется механизм префиксов find…By, read…By, query…By, count…By, и get…By, далее от префикса метода начинает разбор остальной части. Вводное предложение может содержать дополнительные выражения, например, Distinct. Далее первый By действует как разделитель, чтобы указать начало фактических критериев. Можно определить условия для свойств сущностей и объединить их с помощью And и Or.

Если нужен специфичный метод или его реализация, которую нельзя описать через имя метода, то это можно сделать через некоторый Customized интерфейс ( CustomizedEmployees) и сделать реализацию вычисления. А можно пойти другим путем, через указание запроса (HQL или SQL), как вычислить данную функцию. Отметив запрос аннотацией @Query.

Нативный запрос можно написать так:
```java
public interface RoleRepository extends JpaRepository<Role, Long>{

    Optional<Role> findRoleByRoleName(String name);

    @Modifying
    @Transactional
    @Query(value = "INSERT INTO user_roles (user_id, role_id) VALUE (:user_id, :role_id)", nativeQuery = true)
    void insertRoles(@Param("user_id) Long user_id, @Param("role_id") Long role_id);

    //можно использовать с EntityGraph, обычным, не NamedEntityGraph
    @EntityGraph(value = "customer.products")
    List<Customer> findAll(@Nullable Specification<Customer> specification)
}
```


## Конфигурация Spring Data
Поскольку мы используем JPA, нам нужно определить свойства для подключения к базе данных в файле persistence.xml, а не в hibernate.cfg.xml. Создайте новый каталог с именем META-INF в исходной папке проекта, чтобы поместить в него файл persistence.xml.

Затем прописать в вайле свойства для подключения к базе, например:
```xml
<persistence-unit name="DataBaseName">
        <properties>
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:3306/sales" />
            <property name="javax.persistence.jdbc.user" value="root" />
            <property name="javax.persistence.jdbc.password" value="root" />
            <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver" />
            <property name="hibernate.show_sql" value="true" />
            <property name="hibernate.format_sql" value="true" />
        </properties>
    </persistence-unit>
```

Для работы с Spring Data JPA нам надо создать два beans-компонента: EntityManagerFactory и JpaTransactionManager. Поэтому создадим другой конфигурационный класс JpaConfig:
```java
@Configuration
@EnableJpaRepositories(basePackages = {"net.codejava.customer"})
@EnableTransactionManagement
public class JpaConfig {

    @Bean
    public LocalEntityManagerFactoryBean entityManagerFactory() {
        LocalEntityManagerFactoryBean factoryBean = new LocalEntityManagerFactoryBean();
        factoryBean.setPersistenceUnitName("SalesDB");

        return factoryBean;
    }

    @Bean
    public JpaTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
        JpaTransactionManager transactionManager = new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(entityManagerFactory);

        return transactionManager;
    }
}
```

@EnableJpaRepositories: сообщает Spring Data JPA, что нужно искать классы репозитория в указанном пакете (net.codejava) для внедрения соответсвующего кода во время выполнения.

@EnableTransactionManagement: сообщает Spring Data JPA, чтобы тот генерировал код для управления транзакциями во время выполнения.

В этом классе первый метод создаёт экземпляр EntityManagerFactory для управления Persistence Unit нашей SalesDB (это имя указано выше в persistence.xml).

Последний метод создаёт экземпляр JpaTransactionManager для EntityManagerFactory, созданный методом ранее.

Это минимальная необходимая конфигурация для использования Spring Data JPA.
