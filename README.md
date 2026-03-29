## 📂 중앙 설정 저장소 (Project Configs)

이 레포지토리는 MSA 프로젝트의 모든 마이크로서비스 환경 설정(`yml`)을 중앙 관리하는 저장소입니다.

`config-server`가 이 레포지토리를 읽어 각 서비스에 설정을 배달합니다.

---

### 폴더 및 파일 구조 규칙

모든 설정 파일은 아래의 계층 구조를 따릅니다.

```text

project-configs

├── common/                         # 모든 서비스 공통 설정
│   ├── application.yml             # \[기본] 로컬/공통 DB, Eureka 정보
│   ├── application-dev.yml         # \[개발] 개발 서버용 공통 정보
│   └── application-prod.yml        # \[운영] 실제 운영 서버용 공통 정보
│
└── {service-name}/                 # 각 서비스 전용 폴더 (예: user-service)
&#x20;   ├── {service-name}.yml          # 서비스별 기본 설정 (포트 등)

&#x20;   └── {service-name}-{profile}.yml # 서비스별 환경 설정 (dev, prod)
```

---

## 🛠️ 설정 추가/수정 가이드

본인의 서비스를 연결하려면 아래 규칙을 지켜주세요.

### 1. 신규 서비스 등록 시

1. 루트 경로에 본인의 spring.application.name과 동일한 이름으로 폴더를 생성합니다. (예: order-service/)

2. 폴더 안에 {service-name}.yml 파일을 만듭니다.

3. 서비스 전용 포트나 커스텀 설정을 작성합니다.

### 2. 설정 파일 우선순위

설정은 아래 순서로 읽히며, 아래쪽일수록 우선순위가 높습니다 (덮어쓰기).

1. common/application.yml

2. common/application-{profile}.yml

3. {service-name}/{service-name}.yml

4. {service-name}/{service-name}-{profile}.yml (최우선)

### 3. 변경사항 반영하기

Git에 Push 한다고 해서 즉시 실행 중인 서비스에 반영되지 않습니다.

반영을 원하시면 해당 서비스의 Actuator Refresh 엔드포인트를 호출해야 합니다.

```
curl -X POST http://localhost:{서비스포트}/actuator/refresh
```

---

## ⚠️ 주의사항

1. **파일명 일치**: 파일명이 서비스의 `spring.application.name`과 다르면 config-server가 파일을 찾지 못합니다.

2. **YAML 문법**: 공백(Space) 2칸 들여쓰기를 엄격히 준수하세요. 문법 에러 시 전체 서비스의 설정 로딩이 실패할 수 있습니다.
