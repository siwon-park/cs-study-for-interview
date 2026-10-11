# 메일 학습 커리큘럼

> 메일 학습 내용을 정리

### 1단계 - 메일의 큰 그림

- 메일은 어떻게 동작하는가 (텍스트 기반 프로토콜)
- 역할 구분: MUA / MTA / MDA / MSA, 메일 박스(저장소)
- 발송부터 수신까지 전체 흐름 도식화

### 2단계 - 프로토콜 3종

- SMTP: 명령어(EHLO, MAIL FROM, RCPT TO, DATA), 응답 코드(2xx/4xx/5xx), 포트(25/465/587)
- POP3와 IMAP: 동작 원리, 차이점, 포트, 서버 보관 vs 다운로드
- TLS/STARTTLS와 인증(AUTH)

### 3단계 - 메일 메시지 구조

- 봉투(Envelope)와 본문(Header + Body)의 차이 (MAIL FROM과 From: 헤더가 다를 수 있는 이유)
- MIME, 첨부파일, 인코딩(Base64, Quoted-Printable), 한글 charset

### 4단계 - 헤더 읽기

- Received 체인을 아래→위로 읽는 법
- From / To / Return-Path / Message-ID / Date 등 주요 헤더
- 지연 구간 찾기, 경유 서버 추적

### 5단계 - DNS와 스팸/인증

- MX, SPF, DKIM, DMARC, PTR(역방향 DNS)
- 스팸 판정 원리, 블랙리스트
- 헤더의 Authentication-Results 읽기

### 6단계 - 반송과 장애 대응

- NDR(반송 메일) 읽는 법, 4xx(일시적) vs 5xx(영구적) 오류
- 메일 큐(deferred, 재시도)
- 케이스 스터디: "안 간다 / 안 온다 / 늦게 온다 / 스팸함에 간다 / 한글이 깨진다"

### 7단계 - 다우오피스 메일 구조 (보류)

- 프로세스별 역할, 로그 위치, 설정 파일, 장애 시 확인 순서
- 별도 학습 후, 1~6단계 지식을 바탕으로 매핑 (`ps -ef`, `systemctl`, `ss -lntp`로 프로세스·포트 확인 → 설정 파일·로그 경로 확인)