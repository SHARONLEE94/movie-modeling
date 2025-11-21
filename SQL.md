# 최종 SQL DDL 스크립트
- 아래는 앞서 논의된 요구사항을 반영한 최종 SQL DDL 스크립트입니다.
- 스키마는 생략됨.
- MySQL 8.x
- NOT NULL / PK / FK / 기본값 / 인덱스 포함

```sql

/* ==========================================================
1. CODE GROUP & COMMON CODE
   ========================================================== */
   CREATE TABLE code_group (
   group_code VARCHAR(50) PRIMARY KEY,
   group_name VARCHAR(100) NOT NULL,
   description VARCHAR(255)
   );

CREATE TABLE common_code (
code_value VARCHAR(50) PRIMARY KEY,
group_code VARCHAR(50) NOT NULL,
code_name VARCHAR(100) NOT NULL,
display_order INT DEFAULT 0,
is_active CHAR(1) DEFAULT 'Y',
extra_fee INT DEFAULT 0,
FOREIGN KEY (group_code) REFERENCES code_group(group_code)
);


/* ==========================================================
2. THEATER (추가)
   ========================================================== */
   CREATE TABLE theater (
   theater_id INT AUTO_INCREMENT PRIMARY KEY,
   name VARCHAR(100) NOT NULL,
   location VARCHAR(255) NOT NULL
   );


/* ==========================================================
3. EMPLOYEE (추가)
   ========================================================== */
   CREATE TABLE employee (
   employee_id INT AUTO_INCREMENT PRIMARY KEY,
   employee_name VARCHAR(100) NOT NULL,
   employee_phone VARCHAR(20) NOT NULL
   );


/* ==========================================================
4. SCREEN (기존 + FK 2개 추가)
   ========================================================== */
   CREATE TABLE screen (
   screen_id INT AUTO_INCREMENT PRIMARY KEY,
   theater_id INT NOT NULL,
   employee_id INT NOT NULL,
   screen_name VARCHAR(100) NOT NULL,
   total_seats INT DEFAULT 0,
   FOREIGN KEY (theater_id) REFERENCES theater(theater_id),
   FOREIGN KEY (employee_id) REFERENCES employee(employee_id)
   );


/* ==========================================================
5. SEAT (기존 + LOCATION 추가)
   ========================================================== */
   CREATE TABLE seat (
   seat_id INT AUTO_INCREMENT PRIMARY KEY,
   screen_id INT NOT NULL,
   seat_number VARCHAR(10) NOT NULL,
   seat_grade_code VARCHAR(50) NOT NULL,
   seat_status_code VARCHAR(50) NOT NULL,
   location VARCHAR(50) NOT NULL,
   FOREIGN KEY (screen_id) REFERENCES screen(screen_id),
   FOREIGN KEY (seat_grade_code) REFERENCES common_code(code_value),
   FOREIGN KEY (seat_status_code) REFERENCES common_code(code_value)
   );


/* ==========================================================
6. MOVIE (기존 유지)
   ========================================================== */
   CREATE TABLE movie (
   movie_id INT AUTO_INCREMENT PRIMARY KEY,
   title VARCHAR(200) NOT NULL,
   genre VARCHAR(100),
   running_time INT NOT NULL,
   rating VARCHAR(20),
   director VARCHAR(100),
   actors VARCHAR(300),
   release_date DATE,
   start_date DATE,
   end_date DATE
   );


/* ==========================================================
7. PRICE POLICY (기존 유지)
   ========================================================== */
   CREATE TABLE price_policy (
   price_policy_id INT AUTO_INCREMENT PRIMARY KEY,
   day_type_code VARCHAR(50) NOT NULL,
   time_slot_code VARCHAR(50) NOT NULL,
   base_price INT NOT NULL,
   FOREIGN KEY (day_type_code) REFERENCES common_code(code_value),
   FOREIGN KEY (time_slot_code) REFERENCES common_code(code_value)
   );


/* ==========================================================
8. SHOWTIME (기존 유지)
   ========================================================== */
   CREATE TABLE showtime (
   showtime_id INT AUTO_INCREMENT PRIMARY KEY,
   movie_id INT NOT NULL,
   screen_id INT NOT NULL,
   start_time DATETIME NOT NULL,
   end_time DATETIME NOT NULL,
   showtime_status_code VARCHAR(50) NOT NULL,
   price_policy_id INT NOT NULL,
   FOREIGN KEY (movie_id) REFERENCES movie(movie_id),
   FOREIGN KEY (screen_id) REFERENCES screen(screen_id),
   FOREIGN KEY (showtime_status_code) REFERENCES common_code(code_value),
   FOREIGN KEY (price_policy_id) REFERENCES price_policy(price_policy_id)
   );


/* ==========================================================
9. USER (기존 유지)
   ========================================================== */
   CREATE TABLE user (
   user_id INT AUTO_INCREMENT PRIMARY KEY,
   name VARCHAR(100) NOT NULL,
   email VARCHAR(100),
   phone VARCHAR(20)
   );


/* ==========================================================
10. RESERVATION (기존 유지)
    ========================================================== */
    CREATE TABLE reservation (
    reservation_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    showtime_id INT NOT NULL,
    reservation_status_code VARCHAR(50) NOT NULL,
    reservation_time DATETIME NOT NULL,
    FOREIGN KEY (user_id) REFERENCES user(user_id),
    FOREIGN KEY (showtime_id) REFERENCES showtime(showtime_id),
    FOREIGN KEY (reservation_status_code) REFERENCES common_code(code_value)
    );


/* ==========================================================
11. RESERVATION SEAT (기존 유지)
    ========================================================== */
    CREATE TABLE reservation_seat (
    reservation_id INT NOT NULL,
    seat_id INT NOT NULL,
    PRIMARY KEY (reservation_id, seat_id),
    FOREIGN KEY (reservation_id) REFERENCES reservation(reservation_id),
    FOREIGN KEY (seat_id) REFERENCES seat(seat_id)
    );


/* ==========================================================
12. PAYMENT (기존 유지)
    ========================================================== */
    CREATE TABLE payment (
    payment_id INT AUTO_INCREMENT PRIMARY KEY,
    reservation_id INT NOT NULL,
    payment_status_code VARCHAR(50) NOT NULL,
    amount INT NOT NULL,
    payment_time DATETIME NOT NULL,
    FOREIGN KEY (reservation_id) REFERENCES reservation(reservation_id),
    FOREIGN KEY (payment_status_code) REFERENCES common_code(code_value)
    );


/* ==========================================================
13. TICKET (과제 요구사항 충족용 — 추가)
    ========================================================== */
    CREATE TABLE ticket (
    ticket_id INT AUTO_INCREMENT PRIMARY KEY,
    reservation_id INT NOT NULL,
    seat_id INT NOT NULL,
    masked_phone VARCHAR(20) NOT NULL,
    FOREIGN KEY (reservation_id) REFERENCES reservation(reservation_id),
    FOREIGN KEY (seat_id) REFERENCES seat(seat_id)
    );


```
