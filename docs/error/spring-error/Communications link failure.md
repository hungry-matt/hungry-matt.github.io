---
layout: default
title: Communications link failure
parent: Spring-Error
grand_parent: Error
---


Table of contents


- TOC
{:toc}


---

# 상황

새로운 프로젝트 진행을 위해 디비 접속을 시도 하였다.

# 환경

- Spring Boot
- Java 8
- EC2
- RDS(Aurora MySQL)

# 문제

다음과 같은 오류와 함께 디비 접속에 실패하였다.

```sh
com.mysql.cj.jdbc.exceptions.CommunicationsException: Communications link failure

The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.
	at com.mysql.cj.jdbc.exceptions.SQLError.createCommunicationsException(SQLError.java:174) ~[mysql-connector-java-8.0.21.jar:8.0.21]
	at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:64) ~[mysql-connector-java-8.0.21.jar:8.0.21]
	at com.mysql.cj.jdbc.ConnectionImpl.createNewIO(ConnectionImpl.java:836) ~[mysql-connector-java-8.0.21.jar:8.0.21]
	at com.mysql.cj.jdbc.ConnectionImpl.<init>(ConnectionImpl.java:456) ~[mysql-connector-java-8.0.21.jar:8.0.21]
	at com.mysql.cj.jdbc.ConnectionImpl.getInstance(ConnectionImpl.java:246) ~[mysql-connector-java-8.0.21.jar:8.0.21]
	at com.mysql.cj.jdbc.NonRegisteringDriver.connect(NonRegisteringDriver.java:197) ~[mysql-connector-java-8.0.21.jar:8.0.21]
	at com.zaxxer.hikari.util.DriverDataSource.getConnection(DriverDataSource.java:138) ~[HikariCP-3.4.5.jar:na]
	at com.zaxxer.hikari.pool.PoolBase.newConnection(PoolBase.java:358) ~[HikariCP-3.4.5.jar:na]
	at com.zaxxer.hikari.pool.PoolBase.newPoolEntry(PoolBase.java:206) ~[HikariCP-3.4.5.jar:na]
	at com.zaxxer.hikari.pool.HikariPool.createPoolEntry(HikariPool.java:477) [HikariCP-3.4.5.jar:na]
	at com.zaxxer.hikari.pool.HikariPool.checkFailFast(HikariPool.java:560) [HikariCP-3.4.5.jar:na]
	at com.zaxxer.hikari.pool.HikariPool.<init>(HikariPool.java:115) [HikariCP-3.4.5.jar:na]
	at com.zaxxer.hikari.HikariDataSource.getConnection(HikariDataSource.java:112) [HikariCP-3.4.5.jar:na]
	at org.hibernate.engine.jdbc.connections.internal.DatasourceConnectionProviderImpl.getConnection(DatasourceConnectionProviderImpl.java:122) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.engine.jdbc.env.internal.JdbcEnvironmentInitiator$ConnectionProviderJdbcConnectionAccess.obtainConnection(JdbcEnvironmentInitiator.java:180) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.engine.jdbc.env.internal.JdbcEnvironmentInitiator.initiateService(JdbcEnvironmentInitiator.java:68) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.engine.jdbc.env.internal.JdbcEnvironmentInitiator.initiateService(JdbcEnvironmentInitiator.java:35) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.boot.registry.internal.StandardServiceRegistryImpl.initiateService(StandardServiceRegistryImpl.java:101) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.createService(AbstractServiceRegistryImpl.java:263) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.initializeService(AbstractServiceRegistryImpl.java:237) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.getService(AbstractServiceRegistryImpl.java:214) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.id.factory.internal.DefaultIdentifierGeneratorFactory.injectServices(DefaultIdentifierGeneratorFactory.java:152) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.injectDependencies(AbstractServiceRegistryImpl.java:286) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.initializeService(AbstractServiceRegistryImpl.java:243) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.getService(AbstractServiceRegistryImpl.java:214) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.<init>(InFlightMetadataCollectorImpl.java:176) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.boot.model.process.spi.MetadataBuildingProcess.complete(MetadataBuildingProcess.java:118) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.metadata(EntityManagerFactoryBuilderImpl.java:1224) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:1255) [hibernate-core-5.4.21.Final.jar:5.4.21.Final]
	at org.springframework.orm.jpa.vendor.SpringHibernateJpaPersistenceProvider.createContainerEntityManagerFactory(SpringHibernateJpaPersistenceProvider.java:58) [spring-orm-5.2.9.RELEASE.jar:5.2.9.RELEASE]
	at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.createNativeEntityManagerFactory(LocalContainerEntityManagerFactoryBean.java:365) [spring-orm-5.2.9.RELEASE.jar:5.2.9.RELEASE]
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:391) [spring-orm-5.2.9.RELEASE.jar:5.2.9.RELEASE]
	at java.util.concurrent.FutureTask.run(FutureTask.java:266) ~[na:1.8.0_292]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) ~[na:1.8.0_292]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) ~[na:1.8.0_292]
	at java.lang.Thread.run(Thread.java:748) ~[na:1.8.0_292]
Caused by: com.mysql.cj.exceptions.CJCommunicationsException: Communications link failure
```

# 해결

- 먼저 방화벽 때문에 접근이 되지 않을 수 있다는 글을 보고 보안그룹에서 인바운드 규칙을 추가해주었다.
- 인바운드 규칙을 추가하고 디비 접속을 확인하였지만 프로젝트에서는 동일한 오류가 발생하였고 다음과 같이 해결하였다.

```yaml
# 변경전
url: jdbc:mysql://localhost:3306/sample?useUnicode=true
# 변경후
url: jdbc:mysql://localhost:3306/sample?useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&serverTimezone=UTC&enabledTLSProtocols=TLSv1,TLSv1.1,TLSv1.2
```