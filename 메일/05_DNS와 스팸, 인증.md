# 05_DNS와 스팸/인증

> 수신 서버의 검증 방법

수신 서버가 "이 메일이 정말 해당 도메인에서 온 것인가"를 판단하는 기준

## 1. DNS

> Domain Name System

메일은 여러 종류의 DNS 레코드를 사용한다.

| 레코드       | 풀네임         | 메일에서의 용도                            |
| ------------ | -------------- | ------------------------------------------ |
| `MX`         | Mail Exchanger | 도메인의 수신 서버 지정                    |
| `A` / `AAAA` | Address        | 서버 이름 → IPv4 / IPv6 주소 (정방향 조회) |
| `PTR`        | Pointer        | IP → 서버 이름 (역방향 조회)               |
| `TXT`        | Text           | SPF, DKIM, DMARC 정책을 문자열로 저장      |
| `CNAME`      | Canonical Name | 이름의 별칭                                |

### 1) MX

```
$ dig MX example.com +short
10 mx1.example.com.
20 mx2.example.com.
```

- mx 레코드는 우선순위가 존재한다. mx 레코드 조회 시 앞에 있는 숫자는 우선순위를 의미하며 우선순위가 작을 수록 먼저 시도한다.
- mx는 서버 이름 (A 레코드가 있는 이름)을 가리켜야 하고, IP를 직접 적거나 CNAME을 가리키면 안 된다.
- mx가 아예 없을 경우에는 도메인의 A레코드로 폴백한다.
- mx 레코드 변경 이후 이전 레코드의 캐시가 TTL만큼 남아있어 변경된 내용 반영까지 시간이 걸릴 수도 있다. (메일 수/발신에 이슈가 있을 가능성)

<br>

### 2) A레코드와 CNAME

<br>

### 3) PTR

역방향 DNS라고도 하며, **발신 서버의 IP가 어떤 이름으로 등록되어 있는지 확인**하기 위해 사용하는 레코드이다.

수신 서버는 다수는 다음 내용을 확인한다.

- IP에 PTR이 있는가 (PTR 레코드 존재 여부)
- PTR이 가리키는 이름을 다시 조회(A레코드, 정방향 조회)하면 같은 IP가 나오는가
- EHLO 이름과도 일관되는가

```bash
$ dig -x 198.51.100.10 +short
mail.sender.com.
$ dig A mail.sender.com +short
198.51.100.10
```

PTR 레코드가 없거나 PTR - A 레코드 조회 시 어긋나는 사항이 있을 경우 스팸 점수가 오르거나 거절될 가능성이 있다.

PTR 레코드의 경우 IP를 소유한 쪽에서 설정해줘야 하는 부분이기 때문에 ISP, CSP 측에서 설정해줘야 한다. (도메인 호스팅 업체에서 설정 불가능한 정보)

<br>

### 4) TXT

TXT 레코드는 종종 SPF 레코드라고도 하며, **"이 도메인으로 보낼 수 있는 서버(IP) 목록**"을 의미한다. 이를 DNS에 공개하여 해당 서버가 해당 도메인으로 메일을 보낼 수 있는지 확인하는 방식이다.

#### (1) SPF (Sender Policy Framework)

이 도메인으로 보낼 수 있는 서버 IP 목록을 DNS에 공개하는 형태이다.

```
example.com.  TXT  "v=spf1 ip4:198.51.100.0/24 include:_spf.mailservice.com mx -all"
```

- 위 SPF 레코드를 해석하면 "봉투 MAIL FROM이 @example.com인 메일은 3가지 ip (단일, 대역, 목록)중 하나에 해당하는 곳에서 올 때만 정상이고 나머지는 전부 실패 처리해라"는 의미이다.
  - `example.com`: 봉투의 MAIL FROM이 해당 도메인일 경우 해당 SPF 레코드 검증.
  - `v=spf1`: 이 레코드는 SPF 레코드이다.
  - 3개의 ip 및 대역에서만 허용한다.
    -  `ip4:198.51.100.0/24`로 198.51.100.0 ~ 198.51.100.255 (256개)
    - `include:_spf.mailservice.com`: 발송 대행사 `mailservice.com`이 공개한 SPF 목록(을 가져와서 검사하여 그 목록에 있는 ip면 허용)
    - `mx`: example.com의 mx 레코드가 가리키는 서버 ip를 허용 (수신 서버가 발송도 한다고 보는 것)
  - `-all`: 봉투의 도메인이 위 ip와 하나도 안 맞으면 실패 처리해라
- SPF 레코드 평가 방식은 왼쪽부터 우측으로 순서대로 검사하고, 처음 매칭된 항목의 결과로 확정한다.
- 도메인 당 SPF 레코드는 1개만 허용된다. 만약에 SPF 레코드가 여러 개 있을 경우 수신 서버 측에서는 어느 SPF 레코드를 기준으로 도메인 검증을 할지 알 수 없기 때문에 메일 발신이 실패할 수도 있다.

#### (2) DKIM (Domain Keys Identified Mail)



#### (3) DMARC (Domain-based Message Authentication, Reporting and Conformance)



![spf_dkim_dmarc_flow](assets/spf_dkim_dmarc_flow.png)



<br>

## 2. 인증과 스팸