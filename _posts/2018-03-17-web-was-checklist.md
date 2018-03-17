---
layout: post
title: "WEB/WAS 구성 체크리스트"
description: "Tomcat, Apache 구성 체크리스트"
date: 2018-03-17
tags: [web, was, tomcat, apache]
comments: true
share: true
---

프로젝트 때 WEB/WAS 구성하였던 경험을 바탕으로 작성한 체크리스트. 디테일한 기억은 나지 않고 설정별로 어떠한 기능인지도 다 기억나지는 않지만 항목별로 주요 포인트를 체크리스트로 뽑아봄.

## Tomcat 설치 및 구성 체크리스트

#### 디렉토리 지정

- WAS 디렉토리
>ex) /app/was
- 톰캣 디렉토리
>ex) /app/was/tomcat
- 서버 디렉토리
>ex) /app/was/tomcat/servers/test-server1
- 서비스 디렉토리
>ex) /app/svc/
- 로그 디렉토리
>ex) /logs

#### 설치버전 확인
- 자바
>ex) /app/was/jdk1.8.x_xxx/bin/java -version
- 톰캣  
>ex) /app/was/tomcat/bin/version.sh

#### 로그 설정
- accesslog  
로그디렉토리, 파일명 형태 지정

```xml
<!-- example -->
<Valve className="org.apache.catalina.valves.AccessLogValve"
    directory="/logs/was/tomcat/%{server_name}/accesslog"
    prefix="%{server-name}_access_log" suffix=".log"
    pattern="%h %l %u %t %r %s %b %{Referer}i %{User-Agent}i"
    fileDateFormat="yyyy-MM-dd-HH" resolveHosts="false" />
```
- catalina.out  
로그디렉토리, 파일명 형태 지정, 로테이션, 재기동시마다 백업  
AP로그와 WAS로그가 같이 있게 하지 말고 별도 분리 처리

- gclog
로그디렉토리, 파일명 형태 지정, 로테이션, 재기동시마다 백업  
구동 스크립트 JAVA_OPTS에 항목 추가
>ex) -verbose:gc, -XX:+UseParallelGC, -XX:+PrintGCDetails, -XX:PrintGcTimeStamps, -XX:+PrintHeapAtGC, -XX:+PrintGCDateStamps...

#### 설정 표준
- JVM 옵션  
서버 스펙에 맞게 메모리 옵션 추가 및 GC설정 추가 및 힙덤프 설정  
[오라클 JVM옵션 Document](http://www.oracle.com/technetwork/articles/java/vmoptions-jsp-140102.html)

- server.xml  
executor 태그 주석제거 및 서비스 특성에 맞는 thread 수 설정  
connector 포트 설정 및 HTTP/AJP 파라미터 튜닝  
hot-deploy 설정끄기 (autoDeploy="false")  
이중화 (jvmRoute를 worker명에 일치 시킴)

```xml
<!-- example -->
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
    maxThreads="300" minSpareThreads="100"/>

<Connector executor="tomcatThreadPool"
    port="8080" protocol="HTTP/1.1"
    enableLookups="false" server="server"
    maxPostSize="-1" maxKeepAliveRequests="1000"
    connectionTimeout="20000" maxParameterCount="100"
    acceptCount="1500" redirectPort="8443" />
<Connector executor="tomcatThreadPool"
    port="8009" protocol="AJP/1.3"
    enableLookups="false" server="server"
    maxPostSize="-1" connectionTimeout="20000"
    maxParameterCount="100" redirectPort="8443"/>

<Host name="localhost"  appBase="/app/was/svc"
    unpackWARs="true" autoDeploy="false">

<!-- tomcat 1번 인스턴스 -->
<Engine name="Catalina" defaultHost="localhost" jvmRoute="worker1">
<!-- tomcat 2번 인스턴스 -->
<Engine name="Catalina" defaultHost="localhost" jvmRoute="worker2">
```

- Connection Pool  
DB연동 부분으로 연동되는 DB종류, 접속 url, ID/PW, 커넥션 체크, 성능 등에 대한 설정 필요

```xml
<!-- example -->
<Resource name="jdbc/mysql"
        auth="Container"
        type="javax.sql.DataSource"
        driverClassName="com.mysql.jdbc.Driver"
        factory="org.apache.tomcat.jdbc.pool.DataSourceFactory를 감싸놓은 암호화 팩토리 모듈"
        url="jdbc:mysql://127.0.0.1:3316/testDB"
        maxTotal="100"
        minIdle="10"
        maxIdle="20"
        maxWaitMillis="20000"
        validationQuery="SELECT 1"
        testOnBorrow="true"
        validationQueryTimeout="30"
        defaultAutoCommit="false"
        username="암호화된 값"
        password="암호화된 값"
        initialSize="10"
        poolPreparedStatements="true"
        maxOpenPreparedStatements="50"
        removeAbandoned="true"
        removeAbandonedTimeout="60"
        logAbandoned="true"
        ... />
```

#### 보안 
- 톰캣 구동 계정확인  
ex) ps -ef | grep java | grep tomcat
- 디렉토리 권한 확인 및 구성 ( chmod -R 700 )
>ex) ls -al /app/was/tomcat
- DB접속 ID/패스워드 암호화 확인(db)  
JAVA로 DataSourceFactory 클래스를 상속(Extends)하여 암호화 모듈 로직을 넣어 ID/PW를 암호화해주는 모듈을 구현하고, 설정에서 ID/PW가 그대로 노출되지 않도록 해야함
- 불필요 디렉토리 삭제  
{TOMCAT_HOME}/webapps 밑의 manager, host-manager, examples 디렉토리 삭제
- web.xml  
tomcat에서 구동되는 어플리케이션의 web.xml을 수정하여 보안취약점 제거  
HTTP request 중 PUT, DELETE, TRACE, OPTIONS 요청을 제한하고, 자바파일 생성 제한을 걸어둠  

```xml
<security-constraint>
    <display-name>Protected Context</display-name>
    <web-resource-collection>
        <web-resource-name>Protected Context</web-resource-name>
        <url-pattern>/*</url-pattern>
        <http-method>PUT</http-method>
        <http-method>DELETE</http-method>
        <http-method>TRACE</http-method>
        <http-method>OPTIONS</http-method>
    </web-resource-collection>
    <auth-constraint />
</security-constraint>
...
<servlet>
    ...
    <init-param>
        <param-name>readonly</param-name>
        <param-value>true</param-value>
    </init-param>
    ...
    <init-param>
        <param-name>keepgenrated</param-name>
        <param-value>false</param-value>
    </init-param>
    ...    
</servlet>
```

---

## Apache 설치 및 구성 체크리스트

#### 디렉토리 지정

- WEB 디렉토리
>ex) /app/web
- 아파치 디렉토리
>ex) /app/web/apache
- 서비스 디렉토리
>ex) /app/svc/
- 로그 디렉토리
>ex) /logs

#### 설치버전 확인
- 아파치
>ex) /app/web/apache/bin/httpd -v
아파치는 컴파일하여 설치하여야 하며, 자세한 사항은 공식 페이지 참조...

#### 로그 설정
- accesslog, customlog, errorlog (대상파일 : httpd.conf, httpd-vhost.conf, httpd-ssl.conf 등)  
로그디렉토리, 파일명 형태 지정이 되야 하며, 로테이션 형태로 로그가 저장되어야 함

#### 설정 표준
- conf  
(httpd.conf /  httpd-default.conf / httpd-mpm.conf / httpd-ssl.conf)
- mpm
- 톰캣연동  
( mod-jk/ Proxy, SSL)
- SSL 인증서 구성  
- 설정 context 확인
>ex) httpd -t

#### 보안 
- 구동 계정확인  
ex) ps -ef | grep httpd
- 디렉토리 권한 확인 (chmod -R 700)
>ex) ls -al /app/web/apache
- HTTP request 정책 및 디렉토리 설정
HTTP Request는 GET, POST만 허용하며, 디렉토리 지시자 설정 확인  

