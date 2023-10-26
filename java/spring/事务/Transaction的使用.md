# @Transational的使用方法

**当使用@Transactional注解在同一个方法内的时候**，并且默认的传播方式是EQUEST的时候。

### 情况 1

如下面的伪代码代码：

```java
public void methodA(){
	insertA();
	
	methodB();
}

@Transactional
public void methodB(){
	insertB();
  throw new RuntimeException();
}
```

> methodA和methodB都不会发生事务的回滚；

### 情况 2

```java
@Transactional
public void methodA(){
	insertA();
	
	methodB();
}


public void methodB(){
	insertB();
  throw new RuntimeException();
}
```

> insertA操作数据库发生回滚了，而insertB并没有发生回滚；

### 情况 3

```java
@Transactional
public void methodA(){
	insertA();
	
	methodB();
}

@Transactional
public void methodB(){
	insertB();
  throw new RuntimeException();
}
```

> insertA和 insertB都发生了回滚；

## **mehtodA和mothodB都不在同一个类的时候**