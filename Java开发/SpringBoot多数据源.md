打印当前使用的数据源
```java
@Autowired  
private DataSource dataSource;  
  
@PostConstruct  
public void check() {  
    System.out.println("DataSource type: " + dataSource.getClass().getName());  
}
```