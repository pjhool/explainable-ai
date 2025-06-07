```mermaid
erDiagram

    grades ||--o{ classrooms : "has"
    classrooms ||--o{ students : "has"
    classrooms ||--o{ teacher_assignments : "has"
    classrooms }o--|| teachers : "homeroom"
    subjects ||--o{ teachers : "teaches"
    subjects ||--o{ teacher_assignments : "included"
    teachers ||--o{ teacher_assignments : "assigned"
    teacher_assignments }o--|| classrooms : "targets"
    teacher_assignments }o--|| subjects : "teaches"
    teacher_assignments }o--|| teachers : "by"
    
    grades {
        int id PK
        int grade_level
    }
    
    classrooms {
        int id PK
        int grade_id FK
        int class_number
        int homeroom_teacher_id FK
    }
    
    students {
        int id PK
        string name
        int classroom_id FK
    }
    
    subjects {
        int id PK
        string name
    }

    teachers {
        int id PK
        string name
        int subject_id FK
    }

    teacher_assignments {
        int id PK
        int teacher_id FK
        int classroom_id FK
        int subject_id FK
    }

```

```mermaid
graph TD
  %% 외부 엔티티
  E1[학생]
  E2[교사]
  E3[관리자]

  %% 프로세스
  P1[학생 정보 관리]
  P2[수업 관리]
  P3[반/학년 관리]
  P4[과목/교사 배정 관리]

  %% 데이터 저장소
  D1[(학생 테이블)]
  D2[(반 테이블)]
  D3[(학년 테이블)]
  D4[(교사 테이블)]
  D5[(과목 테이블)]
  D6[(교사배정 테이블)]

  %% 외부 엔티티 ↔ 프로세스
  E1 -->|학생 정보 등록/조회| P1
  E2 -->|과목/배정 요청| P4
  E3 -->|전체 시스템 관리| P1
  E3 -->|반/학년 설정| P3
  E3 -->|과목/교사 설정| P4
  E3 -->|시간표 편성 등| P2

  %% 프로세스 ↔ 데이터 저장소
  P1 --> D1
  P1 --> D2
  P2 --> D6
  P2 --> D2
  P2 --> D4
  P3 --> D2
  P3 --> D3
  P4 --> D4
  P4 --> D5
  P4 --> D6
```

```mermaid
sequenceDiagram
    participant 학생
    participant 관리자
    participant 시스템
    participant DB

    학생->>+시스템: 입학 신청서 제출
    시스템->>+관리자: 신규 입학 요청 알림
    관리자->>+시스템: 입학 승인 및 학년/반 지정
    시스템->>+DB: 학생 정보 INSERT
    시스템->>+DB: 학년 및 반 배정 UPDATE

    시스템->>+관리자: 담임 교사 확인 요청
    관리자->>+시스템: 반에 맞는 담임 교사 지정
    시스템->>+DB: 학생 ↔ 담임 교사 간 연결 (classroom_id로 연결)

    시스템->>학생: 입학 완료 및 반 배정 결과 알림
    학생-->>시스템: 확인

    deactivate 학생
    deactivate 관리자
    deactivate 시스템
    deactivate DB
```



