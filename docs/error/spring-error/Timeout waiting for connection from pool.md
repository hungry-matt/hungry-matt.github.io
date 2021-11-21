---
layout: default
title: Timeout waiting for connection from pool
parent: Spring-Error
grand_parent: Error
---


Table of contents


- TOC
{:toc}


---

# 상황

개발서버에 올라갔던 기능이 어느날 먹통이 되었다. 

다행이 서버 자체가 다운되지 않고 기능 오류만 발생하였다.

# 환경

- S3
- Java 8
- Spring Boot

# 문제

기능 자체는 S3로 업로드하는 파일에 리다이렉트 주소를 매핑하는 용도였다. 

문제는 업로드시 s3object 에 스트림이 포함되어 있었고, 입력 스트림에 대한 자원을 반환하지 않아 발생하였다.

```bash
Servlet.service() for servlet [dispatcherServlet] in context with path [/***] threw exception [Request processing failed; nested exception is com.amazonaws.SdkClientException: Unable to execute HTTP request: Timeout waiting for connection from pool] with root cause

org.apache.http.conn.ConnectionPoolTimeoutException: Timeout waiting for connection from pool
	at org.apache.http.impl.conn.PoolingHttpClientConnectionManager.leaseConnection(PoolingHttpClientConnectionManager.java:316) ~[httpclient-4.5.12.jar!/:4.5.12]
	at org.apache.http.impl.conn.PoolingHttpClientConnectionManager$1.get(PoolingHttpClientConnectionManager.java:282) ~[httpclient-4.5.12.jar!/:4.5.12]
	at sun.reflect.GeneratedMethodAccessor250.invoke(Unknown Source) ~[na:na]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_212]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_212]
	at com.amazonaws.http.conn.ClientConnectionRequestFactory$Handler.invoke(ClientConnectionRequestFactory.java:70) ~[aws-java-sdk-core-1.11.415.jar!/:na]
	at com.amazonaws.http.conn.$Proxy255.get(Unknown Source) ~[na:na]
	at org.apache.http.impl.execchain.MainClientExec.execute(MainClientExec.java:190) ~[httpclient-4.5.12.jar!/:4.5.12]
	at org.apache.http.impl.execchain.ProtocolExec.execute(ProtocolExec.java:186) ~[httpclient-4.5.12.jar!/:4.5.12]
	at org.apache.http.impl.client.InternalHttpClient.doExecute(InternalHttpClient.java:185) ~[httpclient-4.5.12.jar!/:4.5.12]
	at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:83) ~[httpclient-4.5.12.jar!/:4.5.12]
...
```

# 해결

reference를 참고하여 다음과 같이 해결 하였다.

```yaml
GetObjectRequest getObjectRequest = new GetObjectRequest(BUCKET_NAME, key);

try(S3Object s3Object = amazonS3Client.getObject(getObjectRequest)) {
    //불필요한 데이터를 읽지 않기 위해 http 요청을 중단
    s3Object.getObjectContent().abort();

    ...
} catch (SdkClientException e) {
    log.warn(e.getMessage());
} catch (IOException e) {
    log.warn(e.getMessage());
}
```

# Reference
- [https://github.com/aws/aws-sdk-java/issues/1738](https://github.com/aws/aws-sdk-java/issues/1738)
- [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/s3/AmazonS3.html#getObject-com.amazonaws.services.s3.model.GetObjectRequest-](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/s3/AmazonS3.html#getObject-com.amazonaws.services.s3.model.GetObjectRequest-)
- [https://stackoverflow.com/questions/45624175/amazons3-getting-warning-s3abortableinputstreamnot-all-bytes-were-read-from-t](https://stackoverflow.com/questions/45624175/amazons3-getting-warning-s3abortableinputstreamnot-all-bytes-were-read-from-t)