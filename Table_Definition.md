# DB 테이블 정의서 (Table Definition Document)

## 1) code_group
| 컬럼명         | 타입           | 제약조건/기본값 | 설명       |
| ----------- | ------------ | -------- | -------- |
| group_code  | VARCHAR(50)  | PK       | 코드 그룹 ID |
| group_name  | VARCHAR(100) | NOT NULL | 그룹 이름    |
| description | VARCHAR(255) | NULL     | 그룹 설명    |

## 2) common_code
| 컬럼명           | 타입           | 제약조건/기본값                   | 설명                 |
| ------------- | ------------ | -------------------------- | ------------------ |
| code_value    | VARCHAR(50)  | PK                         | 실제 코드값             |
| group_code    | VARCHAR(50)  | FK → code_group.group_code | 코드 그룹              |
| code_name     | VARCHAR(100) | NOT NULL                   | 코드 이름              |
| display_order | INT          | DEFAULT 0                  | 정렬 순서              |
| is_active     | CHAR(1)      | DEFAULT 'Y'                | 활성 여부              |
| extra_fee     | INT          | DEFAULT 0                  | 추가 요금(좌석 등급/시간대 등) |

## 3) theater
| 컬럼명        | 타입           | 제약조건/기본값           | 설명    |
| ---------- | ------------ | ------------------ | ----- |
| theater_id | INT          | PK, AUTO_INCREMENT | 지점 ID |
| name       | VARCHAR(100) | NOT NULL           | 지점명   |
| location   | VARCHAR(255) | NOT NULL           | 지점 위치 |

## 4) employee
| 컬럼명            | 타입           | 제약조건/기본값           | 설명     |
| -------------- | ------------ | ------------------ | ------ |
| employee_id    | INT          | PK, AUTO_INCREMENT | 직원 ID  |
| employee_name  | VARCHAR(100) | NOT NULL           | 직원 이름  |
| employee_phone | VARCHAR(20)  | NOT NULL           | 직원 연락처 |

## 5) screen
| 컬럼명         | 타입           | 제약조건/기본값                  | 설명     |
| ----------- | ------------ | ------------------------- | ------ |
| screen_id   | INT          | PK, AUTO_INCREMENT        | 상영관 ID |
| theater_id  | INT          | FK → theater.theater_id   | 소속 지점  |
| employee_id | INT          | FK → employee.employee_id | 담당 직원  |
| screen_name | VARCHAR(100) | NOT NULL                  | 상영관 이름 |
| total_seats | INT          | DEFAULT 0                 | 좌석 총 수 |

## 6) seat
| 컬럼명              | 타입          | 제약조건/기본값                    | 설명              |
| ---------------- | ----------- | --------------------------- | --------------- |
| seat_id          | INT         | PK, AUTO_INCREMENT          | 좌석 ID           |
| screen_id        | INT         | FK → screen.screen_id       | 어느 상영관인지        |
| seat_number      | VARCHAR(10) | NOT NULL                    | 좌석 번호(A1 등)     |
| seat_grade_code  | VARCHAR(50) | FK → common_code.code_value | 좌석 등급           |
| seat_status_code | VARCHAR(50) | FK → common_code.code_value | 좌석 상태           |
| location         | VARCHAR(50) | NOT NULL                    | 위치(앞/중간/뒤/좌우 등) |

## 7) movie
| 컬럼명          | 타입           | 제약조건/기본값           | 설명         |
| ------------ | ------------ | ------------------ | ---------- |
| movie_id     | INT          | PK, AUTO_INCREMENT | 영화 ID      |
| title        | VARCHAR(200) | NOT NULL           | 영화 제목      |
| genre        | VARCHAR(100) | NULL               | 장르         |
| running_time | INT          | NOT NULL           | 상영시간(분 단위) |
| rating       | VARCHAR(20)  | NULL               | 관람등급       |
| director     | VARCHAR(100) | NULL               | 감독         |
| actors       | VARCHAR(300) | NULL               | 배우         |
| release_date | DATE         | NULL               | 개봉일        |
| start_date   | DATE         | NULL               | 상영 시작일     |
| end_date     | DATE         | NULL               | 상영 종료일     |

## 8) price_policy
| 컬럼명             | 타입          | 제약조건/기본값                    | 설명        |
| --------------- | ----------- | --------------------------- | --------- |
| price_policy_id | INT         | PK, AUTO_INCREMENT          | 가격 정책 ID  |
| day_type_code   | VARCHAR(50) | FK → common_code.code_value | 요일/공휴일 유형 |
| time_slot_code  | VARCHAR(50) | FK → common_code.code_value | 시간대 구분    |
| base_price      | INT         | NOT NULL                    | 기본 요금     |

## 9) showtime
| 컬럼명                  | 타입          | 제약조건/기본값                          | 설명        |
| -------------------- | ----------- | --------------------------------- | --------- |
| showtime_id          | INT         | PK, AUTO_INCREMENT                | 상영시간 ID   |
| movie_id             | INT         | FK → movie.movie_id               | 상영 영화     |
| screen_id            | INT         | FK → screen.screen_id             | 상영관       |
| start_time           | DATETIME    | NOT NULL                          | 상영 시작     |
| end_time             | DATETIME    | NOT NULL                          | 상영 종료     |
| showtime_status_code | VARCHAR(50) | FK → common_code.code_value       | 상영 상태     |
| price_policy_id      | INT         | FK → price_policy.price_policy_id | 적용된 가격 정책 |

## 10) user
| 컬럼명     | 타입           | 제약조건/기본값           | 설명     |
| ------- | ------------ | ------------------ | ------ |
| user_id | INT          | PK, AUTO_INCREMENT | 고객 ID  |
| name    | VARCHAR(100) | NOT NULL           | 고객 이름  |
| email   | VARCHAR(100) | NULL               | 이메일    |
| phone   | VARCHAR(20)  | NULL               | 휴대폰 번호 |

## 11) reservation
| 컬럼명                     | 타입          | 제약조건/기본값                    | 설명    |
| ----------------------- | ----------- | --------------------------- | ----- |
| reservation_id          | INT         | PK, AUTO_INCREMENT          | 예약 ID |
| user_id                 | INT         | FK → user.user_id           | 예약자   |
| showtime_id             | INT         | FK → showtime.showtime_id   | 상영시간  |
| reservation_status_code | VARCHAR(50) | FK → common_code.code_value | 예약 상태 |
| reservation_time        | DATETIME    | NOT NULL                    | 예약 시각 |

## 12) reservation_seat
| 컬럼명            | 타입  | 제약조건/기본값                            | 설명     |
| -------------- | --- | ----------------------------------- | ------ |
| reservation_id | INT | PK, FK → reservation.reservation_id | 예약 ID  |
| seat_id        | INT | PK, FK → seat.seat_id               | 예약된 좌석 |

## 13) payment
| 컬럼명                 | 타입          | 제약조건/기본값                        | 설명    |
| ------------------- | ----------- | ------------------------------- | ----- |
| payment_id          | INT         | PK, AUTO_INCREMENT              | 결제 ID |
| reservation_id      | INT         | FK → reservation.reservation_id | 예약 ID |
| payment_status_code | VARCHAR(50) | FK → common_code.code_value     | 결제 상태 |
| amount              | INT         | NOT NULL                        | 결제 금액 |
| payment_time        | DATETIME    | NOT NULL                        | 결제 시간 |

## 14) ticket
| 컬럼명            | 타입          | 제약조건/기본값                        | 설명              |
| -------------- | ----------- | ------------------------------- | --------------- |
| ticket_id      | INT         | PK, AUTO_INCREMENT              | 티켓 ID           |
| reservation_id | INT         | FK → reservation.reservation_id | 연결된 예약          |
| seat_id        | INT         | FK → seat.seat_id               | 발권된 좌석(1티켓=1좌석) |
| masked_phone   | VARCHAR(20) | NOT NULL                        | 마스킹된 전화번호(출력용)  |
