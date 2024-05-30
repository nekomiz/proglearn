# Spring Security
- [Что это?](#что-это)
- [Основные классы и интерфейсы](#основные-классы-и-интерфейсы)
- [Как это работает с формой логина и UserDetailsService](#как-это-работает-с-формой-логина-и-userdetailsservice)
- [Аннотации](#аннотации)


## Что это?
> Spring Security - это список фильтров в виде класса FilterChainProxy, интегрированного в контейнер сервлетов, и в котором есть поле List<SecurityFilterChain>. Каждый фильтр реализует какой-то механизм безопасности. Важна последовательность фильтров в цепочке.

Spring Security предоставляет широкие возможности для защиты приложения. Кроме стандартных настроек для аутентификации, авторизации и распределения ролей и маппинга доступных страниц, ссылок и т.п., предоставляет защиту от различных вариантов атак

![Image alt](https://github.com/Shell26/Java-Developer/raw/master/img/Spring8.png)

Когда мы добавляем аннотацию @EnableWebSecurity добавляется DelegatingFilterProxy, его задача заключается в том, чтобы вызвать цепочку фильтров (FilterChainProxy) из Spring Security.

В Java-based конфигурации цепочка фильтров создается неявно.

Если мы хотим настроить свою цепочку фильтров, мы можем сделать это, создав класс, конфигурирующий наше Spring Security приложение, и имплементировав интерфейс WebSecurityConfigurerAdapter. В данном классе, мы можем переопределить метод:
```java
@Override
protected void configure(HttpSecurity http) throws Exception {
   http.csrf().disable().authorizeRequests();
}
```

Именно этот метод конфигурирует цепочку фильтров Spring Security и логика, указанная в этом методе, настроит цепочку фильтров.


## Основные классы и интерфейсы
__SecurityContext__ - интерфейс, отражающий контекст безопасности для текущего потока. Является контейнером для объекта типа Authentication. (Аналог - ApplicationContext, в котором лежат бины).

По умолчанию на каждый поток создается один SecurityContext. SecurityContext-ы хранятся в SecurityContextHolder.

Имеет только два метода: getAuthentication() и setAuthentication(Authentication authentication).

__SecurityContextHolder__ - это место, где Spring Security хранит информацию о том, кто аутентифицирован. Класс, хранящий в ThreadLocal SecurityContext-ы для каждого потока, и содержащий статические методы для работы с SecurityContext-ами, а через них с текущим объектом Authentication, привязанным к нашему веб-запросу.

![Image alt](https://github.com/Shell26/Java-Developer/raw/master/img/Spring9.png)

__Authentication__ - объект, отражающий информацию о текущем пользователе и его привилегиях. Вся работа Spring Security будет заключаться в том, что различные фильтры и обработчики будут брать и класть объект Authentication для каждого посетителя. Кстати объект Authentication можно достать в Spring MVC контроллере командой SecurityContextHolder.getContext().getAuthentication(). Authentication имеет реализацию по умолчанию - класс UsernamePasswordAuthenticationToken, предназначенный для хранения логина, пароля и коллекции Authorities.

__Principal__ - интерфейс из пакета java.security, отражающий учетную запись пользователя. В терминах логин-пароль это логин. В интерфейсе Authentication есть метод getPrincipal(), возвращающий Object. При аутентификации с использованием имени пользователя/пароля Principal реализуется объектом типа UserDetails.

__Credentials__ - любой Object; то, что подтверждает учетную запись пользователя, как правило пароль (отпечатки пальцев, пин - всё это Credentials, а владелец отпечатков и пина - Principal).

__GrantedAuthority__ - полномочия, предоставленные пользователю, например, роли или уровни доступа.

__UserDetails__ - интерфейс, представляющий учетную запись пользователя. Как правило модель нашего пользователя должна имплементировать его. Она просто хранит пользовательскую информацию в виде логина, пароля и флагов isAccountNonExpired, isAccountNonLocked, isCredentialsNonExpired, isEnabled, а также коллекции прав (ролей)пользователя. Данная информация позже инкапсулируется в объекты Authentication.

__UserDetailsService__ - интерфейс объекта, реализующего загрузку пользовательских данных из хранилища. Созданный нами объект с этим интерфейсом должен обращаться к БД и получать оттуда юзеров. используется чтобы создать UserDetails объект путем реализации единственного метода этого интерфейса

```java
UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
```

__AuthenticationManager__ - основной стратегический интерфейс для аутентификации. Имеет только один метод, который срабатывает, когда пользователь пытается аутентифицироваться в системе:
```java
public interface AuthenticationManager {
    Authentication authenticate(Authentication authentication) throws AuthenticationException;
}
```

AuthenticationManager может сделать одну из 3 вещей в своем методе authenticate():

1. вернуть Authentication (с authenticated=true), если предполагается, что вход осуществляет корректный пользователь.
2. бросить AuthenticationException, если предполагается, что вход осуществляет некорректный пользователь.
3. вернуть null, если принять решение не представляется возможным.

Наиболее часто используемая реализация AuthenticationManager - родной класс ProviderManager, который содержит поле private List<AuthenticationProvider>providers со списком AuthenticationProvider-ов и итерирует запрос аутентификации по этому списку AuthenticationProvider-ов. Идея такого разделения - поддержка различных механизмов аутентификации на сайтах.

__AuthenticationProvider__ - интерфейс объекта, выполняющего аутентификацию. Имеет массу готовых реализаций. Также можем задать свой тип аутентификации. Как правило в небольших проектах одна логика аутентификации - по логину и паролю. В проектах побольше логик может быть несколько: Google-аутентификация и т.д., и для каждой из них создается свой объект AuthenticationProvider.

AuthenticationProvider немного похож на AuthenticationManager, но у него есть дополнительный метод, позволяющий вызывающей стороне спрашивать, поддерживает ли он переданный ему объект Authentication, возможно этот AuthenticationProvider может поддерживать только аутентификацию по логину и паролю, но не поддерживать Googleаутентификацию:
```java
boolean supports(java.lang.Class<?> authentication)
```

__PasswordEncoder__ - интерфейс для шифрования/расшифровывания паролей. Одна из популярных реализаций - BCryptPasswordEncoder.

В случае, если нам необходимо добавить логику при успешной/неудачной аутентификации, мы можем создать класс и имплементировать интерфейсы __AuthenticationSuccessHandler__ и __AuthenticationFailureHandler__ соответственно, переопределив их методы.

## Как это работает с формой логина и UserDetailsService
1. Пользователь вводит в форму и отправляет логин и пароль.
2. UsernamePasswordAuthenticationFilter создает объект Authentication - UsernamePasswordAuthenticationToken, где в качестве Principal - логин, а в качестве Credentials - пароль.
3. Затем UsernamePasswordAuthenticationToken передаёт объект Authentication с логином и паролем AuthenticationManager-у.
4. AuthenticationManager в виде конкретного класса ProviderManager внутри своего списка объектов AuthenticationProvider, имеющих разные логики аутентификации, пытается аутентифицировать посетителя, вызывая его метод authenticate(). У каждого AuthenticationProvider-а:
    1. Метод authenticate() принимает в качестве аргумента незаполненный объект Authentication, например только с логином и паролем, полученными в форме логина на сайте. Затем с помощью UserDetailsService метод идёт в БД и ищет такого пользователя.
    2. Если такой пользователь есть в БД, AuthenticationProvider получает его из базы в виде объекта UserDetails. Объект Authentication заполняется данными из UserDetails - в него включаются Authorities, а в Principal записывается сам объект UserDetails, содержащий пользователя.
    3. Затем этот метод возвращает заполненный объект Authentication (прошли аутентификацию). Вызывается AuthenticationSuccessHandler.
    4. Если логин либо пароль неверные, то выбрасывается исключение. Вызывается AuthenticationFailureHandler.
5. Затем этот объект Authentication передается в AccessDecisionManager и получаем решение на получение доступа к запрашиваемой странице (проходим авторизацию).


## Аннотации
+ @EnableGlobalMethodSecurity - включает глобальный метод безопасности.
+ @EnableWebMvcSecurity - "включает" Spring Security. Не будет работать, если наш класс не наследует WebSecurityConfigurerAdapter
+ @Secured используется для указания списка ролей в методе
+ @PreAuthorize и @PostAuthorize обеспечивают контроль доступа на основе выражений. @PreAuthorize проверяет данное выражение перед входом в метод, тогда как аннотация @PostAuthorize проверяет его после выполнения метода и может изменить результат.
+ @PreFilter для фильтрации аргумента коллекции перед выполнением метода
