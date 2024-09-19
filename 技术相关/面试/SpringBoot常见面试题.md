## SpringBoot自动配置原理

1. 通过@SpringBootConfiguration引入@EnableAutoConfiguration注解
2. @EnableAutoConfiguration注解来引入@Import注解，然后import注解使用AutoConfigurationImportSelector来读取spring.factories种配置的类，然后去重和校验是否有效，把那些类注入到IOC中。

为什么  