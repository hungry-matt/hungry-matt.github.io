---
layout: default
title: contextLoads() FAILED
parent: Spring-Error
grand_parent: Error
---


Table of contents


- TOC
{:toc}


---

`Failed to load ApplicationContext` 오류를 검색해 보면 보통은 테스트 코드 작성 시 어노테이션이 누락되거나 설정 파일이 없어서 발생하였다.

하지만 나의 경우 예외로 스케줄러를 만들고 설정 파일내에 cron을 추가하지 않아 발생된 오류였다.

오류 메시지가 다음과 같이 정확한 원인을 알려주지 않아 꽤 애를 먹었다.

```
java.lang.IllegalStateException: Failed to load ApplicationContext
	at org.springframework.test.context.cache.DefaultCacheAwareContextLoaderDelegate.loadContext(DefaultCacheAwareContextLoaderDelegate.java:132)
	at org.springframework.test.context.support.DefaultTestContext.getApplicationContext(DefaultTestContext.java:123)
	at org.springframework.test.context.web.ServletTestExecutionListener.setUpRequestContextIfNecessary(ServletTestExecutionListener.java:190)
	at org.springframework.test.context.web.ServletTestExecutionListener.prepareTestInstance(ServletTestExecutionListener.java:132)
	at org.springframework.test.context.TestContextManager.prepareTestInstance(TestContextManager.java:244)
	at org.springframework.test.context.junit.jupiter.SpringExtension.postProcessTestInstance(SpringExtension.java:98)
	at org.junit.jupiter.engine.descriptor.ClassBasedTestDescriptor.lambda$invokeTestInstancePostProcessors$5(ClassBasedTestDescriptor.java:337)
	at org.junit.jupiter.engine.descriptor.ClassBasedTestDescriptor.executeAndMaskThrowable(ClassBasedTestDescriptor.java:342)
	at org.junit.jupiter.engine.descriptor.ClassBasedTestDescriptor.lambda$invokeTestInstancePostProcessors$6(ClassBasedTestDescriptor.java:337)
	at java.util.stream.ReferencePipeline$3$1.accept(ReferencePipeline.java:193)
	at java.util.stream.ReferencePipeline$2$1.accept(ReferencePipeline.java:175)
	at java.util.ArrayList$ArrayListSpliterator.forEachRemaining(ArrayList.java:1384)
	at java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:482)
	at java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:472)
	at java.util.stream.StreamSpliterators$WrappingSpliterator.forEachRemaining(StreamSpliterators.java:313)
	at java.util.stream.Streams$ConcatSpliterator.forEachRemaining(Streams.java:743)
	at java.util.stream.Streams$ConcatSpliterator.forEachRemaining(Streams.java:742)
	at java.util.stream.ReferencePipeline$Head.forEach(ReferencePipeline.java:647)
	at org.junit.jupiter.engine.desc
```