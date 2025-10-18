# 09_Connection Pool

> 커넥션 풀에 대한 이해

## 1. 커넥션 풀이란?

어플리케이션에서 데이터베이스와 연결하면서 매번 연결을 위한 객체를 생성하고 소멸시키는 것은 낭비가 많은 작업이다.

따라서 매번 커넥션 객체를 생성하지 말고, 풀(pool)에 저장을 해뒀다가 꺼내서 쓰는 방식을 커넥션 풀 방식이라고 한다.

### 1) 동작 과정

어플리케이션을 실행 시작 시점에 커넥션 객체를 미리 생성하여 커넥션 풀에 보관한다.

- 서비스의 특징과 설정 정보에 따라 초기에 생성되는 커넥션 객체의 수는 다르다.
- 커넥션 풀에 저장된 커넥션 객체는 TCP/IP로 DB와 연결되어 있는 상태이기 때문에, 요청에 대해서 즉시 DB로 전달할 수 있는 상태이다.

커넥션 요청이 있을 때, 매번 새로 생성하지 않고, 풀에 있는 커넥션 객체 중 하나를 반환하여 응답하는 방식으로 동작한다.

<br>

### 2) 커넥션 풀 초기화

#### (1) Eager Initialization

어플리케이션이 실행될 때, 커넥션 풀을 만드는 설정으로 기본값이다. 커넥션 객체는 이미 DB와 연결된 상태이기 때문에 커넥션 객체를 만들기 위해서는 그 시점에 DB와 통신이 가능해야 한다.

만약 DB와 통신이 불가능할 경우 커넥션 객체를 생성하는데 실패하고, 커넥션 풀도 생성하지 못하여 어플리케이션 시작이 실패한다.

#### (2) Lazy Initialization

설정을 통해 초기화에 실패해도 일단 어플리케이션을 실행하는 지연 초기화 설정도 존재한다.

```yml
#application.yml
spring:
	datasource:
		hikari:
			initialization-fail-timeout: -1 # 초기화 실패해도 일단 시작함
```

혹은 소스 코드 상에서도 설정할 수 있다.

```java
HikariConfig config = new HikariConfig();
config.setInitializationFailTimeout(-1); // 지연 초기화 설정
```

지연 초기화 옵션을 사용할 경우, 서비스는 일단 커넥션 풀 초기화에 실패하더라도 기동을 계속 진행하고 실제 DB 요청이 올 때 커넥션 생성 시도를 한다.

<br>

### 3) 커넥션 풀 종류

대표적인 커넥션 풀 종류로 크게 c3p0와 hikari cp가 있다. 둘 다 미리 정해진 옵션에 따라서 커넥션을 생성해서 풀에 보관하는 방식이다. (옵션, 성능 등에서 차이가 있다.)

#### (1) c3p0 (Combo Pool)

```java
ComboPooledDataSource cpds = new ComboPooledDataSource();
cpds.setDriverClass("com.mysql.jdbc.Driver");
cpds.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
cpds.setUser("user");
cpds.setPassword("password");

cpds.setInitialPoolSize(5);      // 초기 커넥션 수
cpds.setMinPoolSize(5);           // 최소 유지 커넥션
cpds.setMaxPoolSize(20);          // 최대 커넥션
cpds.setAcquireIncrement(5);      // 부족할 때 증가 단위
```

- 특징
  - PreparedStatement를 캐싱해서 재사용한다.
  - 필요에 따라 동적으로 풀의 크기를 증가/감소시킬 수 있다.
  - `idleConnectionTestPeriod` 옵션을 통해 주기적으로 커넥션 헬스체크를 할 수 있다.
    - preferredTestQuery에 지정된 쿼리를 주기적으로 실행하는 방식으로 동작한다. 보통 SELECT 1과 같은 매우 간단한 쿼리를 사용한다. (지정하지 않을 경우 드라이버가 기본적으로 제공하는 커넥션 체크 방식을 사용한다.)
  - 동기화를 위해 synchronized 블록을 많이 사용하여 hikari cp에 비해 성능적으로 떨어진다.

#### (2) Hikari CP

```java
HikariConfig config = new HikariConfig();
config.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
config.setUsername("user");
config.setPassword("password");

config.setMinimumIdle(5);         // 최소 idle 커넥션
config.setMaximumPoolSize(20);    // 최대 커넥션
config.setConnectionTimeout(30000); // 타임아웃

HikariDataSource ds = new HikariDataSource(config);
```

- 특징
  - CAS(Compare And Swap) 방식을 사용하여 synchronized를 사용하는 c3p0보다 성능적으로 뛰어나다. (수십배 빠르기도 함)
  - 고정 크기의 풀을 사용하여 오버헤드를 감소시킨다. (아무래도 풀의 사이즈가 동적으로 늘어날 수 있고, 상황에 따라 변경이 발생하므로써 부하를 유발시킬 수 있음)
  - SpringBoot의 기본 커넥션 풀로 사용 중

<br>

## 2. 커넥션 풀과 커넥션

### 1) 설정 옵션

yml, properties 파일에서 설정할 수도 있지만, 어플리케이션 코드를 기준으로 정리

#### (1) c3p0

- 풀 크기
  - setInitialPoolSize: 초기 커넥션 수
  - setMinPoolSize: 최소 커넥션 수
  - setMaxPoolSize: 최대 커넥션 수
  - setAcquireIncrement: 부족할 경우 증가 단위
- 타임아웃
  - setCheckoutTimeout: 커넥션 획득 대기 (ms)
  - setMaxIdleTime: 유휴(idle) 커넥션 최대 유지 시간 (s)
  - setMaxConnectionAge: 커넥션 최대 수명 (s)
  - setIdleConnectionTestPeriod: 유휴 커넥션 검증 주기 (s)
- 커넥션 검증
  - setTestConnectionOnCheckout: 커넥션 대여 시 체크 (true / false)
  - setTestConnectionOnCheckin: 커넥션 반환 시 체크 (true / false)
  - setPreferredTestQuery: 검증 쿼리
- 커넥션 누수
  - setUnreturnedConnectionTimeout: 미반환 커넥션 타임아웃 (s)
    - 장점: 커넥션 누수 방지, 장애 자동 복구 가능
    - 단점: 정상 동작 중인 커넥션도 타임아웃 발생 (따라서 적당히 긴 시간으로 설정함, 60초 ~ )
  - setDebugUnreturnedConnectionStackTraces: 미반환 커넥션에 대한 스택 트레이스 설정 (true / false)
- Statement 캐싱
  - setMaxStatements: 전체 statement 캐싱
  - setMaxStatementsPerConnection: 커넥션당 캐시
- 재시도
  - setAcquireRetryAttempts: 재시도 횟수
  - setAcquireRetryDelay: 재시도 간격 (ms)
  - setBreakAfterAcquireFailure: 실패 시 풀 중단 여부 (true / false)
- 기타
  - setAutoCommitOnClose: 자원 close 시 오토 커밋 (true / false)

#### (2) Hikari CP

- 풀 크기
  - setMinimumIdle: 최소 idle 커넥션 수
  - setMaximumPoolSize: 커넥션 풀 최대 크기
- 타임아웃
  - setConnectionTimeout: 커넥션 획득 대기 시간 (ms)
  - setIdleTimeout: 유휴 커넥션 유지 시간 (ms)
  - setMaxLifetime: 커넥션 최대 수명 (ms)
  - setValidationTimeout: 유효성 검사 타임아웃 (ms)
- 커넥션 검증
  - setConnectionTestQuery: 검증 쿼리
- 커넥션 누수
  - setLeakDetectionThreshold: 누수 감지 (ms, 0=비활성화)
    - 운영에서 키는 옵션 아님. 커넥션 누수는 감지 시에는 이미 일어난 상태기 때문에 늦음.
    - 강제 회수 기능이 없는 이유는 HikariCP의 철학("커넥션을 안 돌려주는 것은 어플리케이션의 버그다. 버그를 감춰주는 기능은 만들지 않는다.")
    - try-with-resource / close를 통해 확실하게 자원을 사용하고 반납해라
- 기타
  - setPoolName: 커넥션 풀 명칭
  - setAutoCommit: 오토 커밋 여부
  - setReadOnly: 읽기 전용 여부

#### (3) 요약

```bash
// c3p0 → HikariCP
setMinPoolSize()                 → setMinimumIdle()
setMaxPoolSize()                 → setMaximumPoolSize()
setCheckoutTimeout()             → setConnectionTimeout()
setMaxIdleTime()                 → setIdleTimeout()
setMaxConnectionAge()            → setMaxLifetime()
setUnreturnedConnectionTimeout() → setLeakDetectionThreshold()
setPreferredTestQuery()          → setConnectionTestQuery()
```

#### (4) addDataSourceProperty

yml이나 properties로는 설정할 수 있는데, 소스코드 레벨에서 setter 함수가 없을 경우 해당 메서드를 통해서 직접 옵션을 설정할 수도 있다. HikaiCP의 경우 unreturnedConnection 타임아웃 설정이 없기 때문에 아래 옵션을 고려해볼 수도 있다.

이 옵션이 있는 이유는 DB 드라이버마다 설정 프로퍼티 정보값이 다르다보니 메서드화가 어려워 표준화된 설정 메서드를 만든 것이다.

```java
// 쿼리 타임아웃 설정 with addDataSourceProperty
config.setConnectionTimeout(5000);      // 커넥션 얻기 5초
config.addDataSourceProperty("socketTimeout", "10000");  // 쿼리 실행 10초
config.addDataSourceProperty("connectTimeout", "3000");  // DB 연결 3초
```

<br>

### 2) 권장/필수 설정

#### (1) 커넥션 최대 수명

반드시 `max-lifetime < DB wait_timeout` 필수로 설정해야 한다.

만약 max-lifetime이 DB의 타임아웃 시간보다 크다면, 유효하지 않은 커넥션이 커넥션 풀에 계속해서 남아있을 가능성이 있기 때문이다.

유효하지 않은 커넥션을 사용하여 연결을 시도할 경우 에러가 발생하기 때문에 max-lifetime을 DB wait_timeout보다 짧게 가져가서 max-lifetime을 초과한 커넥션을 반드시 삭제하도록 하는 것이 필수적이다.

#### (2) 커넥션 풀 크기

보통 커넥션 풀 크기를 설정한다고 하면 최대 크기(maximum pool size)를 설정하는 것을 의미한다.

일반적으로는 `(CPU 코어 수 × 2) + 디스크 수` 만큼 설정한다. (기본값: 10)

- minimum-idle의 경우 최소 유휴 커넥션을 의미하기 때문에 사실상 최소 풀 사이즈와 같은 의미이다.
- 다만 이 옵션은 설정하지 않는 것을 권장 값으로 둔다. 왜냐하면 고정 크기 풀 사용 목적으로 maximum pool size의 값을 따라가도록 하기 위함이다.

<br>

## 3. 커넥션 누수

> Connection Leak

커넥션 풀에 더 이상 연결 가능한 커넥션이 없을 때, 커넥션 누수가 발생했다고 말한다.

트래픽이 몰리는 특정 순간에는 커넥션 풀에서 남아있는 커넥션이 없을 수도 있지만, 일반적인 상황에서는 최소 어느 정도는 항상 있는데, 커넥션이 없어서 스레드들이 커넥션을 얻기 위해 대기하는 현상이 발생하게 된다.

커넥션 누수는 단순히 잠깐 DB 연결이 안 되는 수준이 아니라 전체 서비스의 장애를 가져올 수도 있기 때문에 누수가 발생한다면 발생하는 원인을 찾아서 반드시 해결해야 한다.

### 1) 예외와 커넥션 반환

DB 연결을 하려고 했을 때, 연결이 정상적으로 되지 않아 예외가 발생하는 것과 커넥션을 반환하는 것은 별개의 상황임을 유의해야 한다. 예외가 터졌다고 해서 커넥션이 반드시 반환되지는 않는다.

보통 일반적으로는 예외를 catch 했을 때, finally 블록으로 가서 close를 통해 자원을 반납할 수 있지만 동작상 finally 구문을 호출하지 못할 수도 있다. (스레드 무한 대기, 커넥션 객체 자체가 null일 수도 있어 close를 못하는 등)

또한 연결 실패 예외의 경우, 타임아웃 설정 시간이 길다면 스레드가 DB 연결 요청한 것에 대한 예외가 굉장히 나중에 돌아오게 된다. 그러면 예외가 돌아오기까지 다른 스레드들의 요청은 계속해서 대기를 할 수밖에 없고 예외가 터진 다음 커넥션을 정상 반환하더라도 이전 요청들이 반납한 커넥션을 바로 다시 사용하여 요청을 하기 때문에 스레드 자원 고갈 및 커넥션 누수가 발생하게 된다.