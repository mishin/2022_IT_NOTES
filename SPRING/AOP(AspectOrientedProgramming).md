Аспектно-ориентированное программирование - это парадигма программирования, которая позволяет нам включать сквозную функциональность. Т.е. это система, кот. позволяет нам внедрять функциональность в необходимые нам места приложения без изменения ранее описанных конфигураций классов. Простой пример: внедрение логирования в приложение без прописывания логеров в каждом месте.
Аспект состоит из нескольких частей.  
 - Join point.  
Before — перед вызовом метода.  
After — после вызова метода.  
After returning — после возврата значения из функции.  
After throwing — в случае exception.  
After finally — в случае выполнения блока finally.  
Around — можно сделать пред., пост., обработку перед вызовом метода, а также вообще обойти вызов метода.  
  
 - Pointcut.  
Pointcut - это срез, какой набор классов, пакетов и т.д. будет подвергаться проксированию. Т.е. если хотя бы один метод будет попадать под аспект, то весь класс будет сделан как прокси.  
   
 Над каждым объектом, который будет попадать под действия аспекта, будет автоматически создан прокси объект. Этот прокси позволяет обслуживать объект и его методы при помощи доп. функциональности, описанной аспектом.  
 ```java
 @Pointcut("execution(public * com.example.demoAspects.MyService.*(..))")  
 public void callAtMyServicePublic() { }  
 ```  
 - Advice.  
 Это уже непосредственно код, кот. будет выполняться согласно join point & pointcut.  
   
Для работы Spring Aspect мы подтянули 3 библиотеки:  
```xml
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>${aspectj.version}</version>
		</dependency>

		<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjweaver</artifactId>
			<version>${aspectj.version}</version>
		</dependency>
```		
Зачем ружны последние две. Есть отдельная библиотека, отвечающая за аспекты, поинт-каты, джоин-поинты и непосредственно за код. Прослойка aop позволяет подружить библиотеку с аспектами и со спрингом. Т.е. дать возможность спрингу включить для поддержания работы внутри своих бинов сквозную функциональность.  
  
Далее создаем директорию aop с классом CustomAspect.  
В нём прописываем `private static final Logger log = Logger.getLogger(CustomAspect.class);`  
Т.е. таким образом, мы хотим что-то логировать.  
Далее в коде говорим, что будем логировать:  
```java
	@Pointcut("execution(* by.lev.repository.jdbctemplate.JdbcTemplateUserRepository.*(..))")
	public void aroundRepositoryPointcut(){
	}
```
Тем самым мы говорим, что нужно логировать все методы ( *(..) ) в классе JdbcTemplateUserRepository  
  
Далее пишем окружение:  
```java
 @Around("aroundRepositoryPointcut()")
    public Object logAroundMethods(ProceedingJoinPoint joinPoint) throws Throwable{

        //Use StopWatch

        log.info("Method " + joinPoint.getSignature().getName() + " start.");
        Object proceed = joinPoint.proceed();
        log.info("Method " + joinPoint.getSignature().getName() + " finish.\n");
        return proceed;
    }
```    
