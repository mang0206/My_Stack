Mysql에 csv파일 넣기
=============

테이블 생성 스키마
-------------
```
CREATE TABLE food
(
	id INT PRIMARY KEY AUTO_INCREMENT,
	food_name  VARCHAR(64) NOT NULL,
	calorie FLOAT NOT NULL,
	protein FLOAT NOT NULL,
	fat FLOAT NOT NULL,
	carbohydrate FLOAT NOT NULL,
	sugar FLOAT NOT NULL,
	calcium FLOAT NOT NULL,
	phosphorus FLOAT NOT NULL,
	iron FLOAT NOT NULL,
	salt FLOAT NOT NULL,
	potassium FLOAT NOT NULL,
	vitaminA FLOAT NOT NULL,
	vitaminB1 FLOAT NOT NULL,
	vitaminB2 FLOAT NOT NULL,
	folic_acid FLOAT NOT NULL,
    niacin FLOAT NOT NULL,
    vitaminC FLOAT NOT NULL,
    selenium FLOAT NOT NULL,
    vitaminD2 FLOAT NOT NULL,
    zinc  FLOAT NOT NULL,
    fatty_acid FLOAT NOT NULL
) ENGINE=INNODB DEFAULT CHARSET=utf8;
```
ENGINE=INNODB는 default 값이긴 하지만 명시해주는 것이 좋다
DEFAULT CHARSET=utf8;의 경우 지정해 주지 않으면 문자열이 한글일때 오류가 나거나 깨질 수 있으므로 명시해준다.

csv 파일 load 스키마
-------------
```
LOAD DATA INFILE '파일의 절대경로/food.csv'
REPLACE
INTO TABLE food
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\r\n'
IGNORE 1 LINES ;
```
1. REPLACE
    * 중복 행 발견시 삭제 후 입력
2. FIELDS TERMINATED BY ',' 
    * csv 파일의 구분인자를 명시해주는 부분
3. LINES TERMINATED BY '\r\n'
    * csv 파일에서 행 변화를 명시해주는 부분 \n(줄바꿈)만 적을 시 데이터 잘림 오류가 발생할 수 있어 \r도 같이 써준다.
4. IGNORE 1 LINES
    * csv 파일의 첫 번째 행 제외하고 load

load하면서 발생했던 오류 코드
-------------

1. Error 1290
    * Mysql의 경우 기본적으로 지정된 경로에서만 파일을 load할 수 있다.
    * SELECT @@GLOBAL.secure_file_priv; 을 입력해서 경로를 확인할 수 있다.
    * +---------------------------+
    * | @@GLOBAL.secure_file_priv |
    * +---------------------------+
    * | /해당 경로                 |
    * +---------------------------+          
    * 이 경우 해당 경로에 파일을 넣어주는 방식도 있지만 이 방식으로는 해결하지 못했다.
    * 그래서 해당 값을 default로 만들어주는 방식으로 설정
        * 윈도우의 경우 my.ini 파일 수정 
        * 리눅스의 경우 my.cnf 파일 수정
    * 윈도우에서 my.ini파일은 MySQL Server 8.0 폴더에 있다. 리눅스의 경우 vi /etc/my.cnf를 통해 접근
    * secure-file-priv="경로" 이 부분을 secure-file-priv=""이렇게 바꿔준다.
    * 바꿔 준 후 mysql 서버 재시작
        * 윈도우의 경우 관리자 cmd에서 net stop mysql80, net start mysql80을 입력
        * 리눅스의 경우 sudo systemctl stop mysqld, sudo systemctl start mysqld 입력  (우분투의 경우 다름)

    * 서버 재시작해서 SELECT @@GLOBAL.secure_file_priv;로 다시 확인해보면 아래와 같이 변해있다.
    * +---------------------------+
    * | @@GLOBAL.secure_file_priv |
    * +---------------------------+
    * |                           |
    * +---------------------------+  

2. ERROR 29 (HY000):
    * 뒤에 os errno 2인경우 -> 단순히 경로 명이나 파일 명을 잘못적어 파일을 찾지 못하는 경우
    * 뒤에 os errno 13인경우 -> mysql에서 해당 경로나 파일에 대해 접근 권한이 없는 경우
        1. 해당 경로나 파일에 대해 chmod를 통해 권한을 부여한다.
        2. 접근 가능한 곳에 파일을 옮긴다(ex윈도우의 경우 공유 폴더, 리눅스의 경우 tmp 디렉토리)


3. ERROR 1265 (01000):
    * 데이터 유형 불일치, 잘못된 종료 문자에 의해 발생
    1. 데이터 유형 불일치의 경우 테이블의 해당 컬럼 타입 변경
        * alter table food modify 컬럼명 바꿀타입;
        * ex) alter table food modify  serving_size FLOAT;
    2. 컬럼의 타입을 변경했음에도 계속 오류가 발행 한다면 SET SQL_MODE='' 입력하여 해결
        * sql mode는 유효성을 검사하는 mysql 시스템 환경변수이다.
    3. 잘못된 종료 문자로 인해 발생한 경우
        *위의 load 스키마에서 LINES TERMINATED BY '\r\n' 이렇게 작성해줌으로써 해결 됌


4. ERROR 1366 (HY000):
    >한글 오류 - 어떻게 해결했는지 기록이 없다.....

5. ERROR 1366 (2뭐시기) 위의 HY000이랑은 다름:
    * 필드에 들어갈 data type이 맞지 않을 때 생기는 오류다. 정수형 타입에 null이나 문자형이 입력 될 때 주로 발생
    * set @@global.sql_mode = 'ONLY_FULL_GROUP_BY,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'; 입력
    * 유효성 검사 부분을 바꿔줌으로써 해결 3번에서 SET SQL_MODE='' 이 부분가 어떻게 다른지는 모르겠다.

덤프 파일을 통해 테이블 생성
-------------

기존에 성공적으로 만들어진 테이블이 있다면 dunp 방식으로 쉽게 테이블 생성 가능

1. database 전체를 덤프하려면
    * mysqldump -u root p > 파일이름.sql    입력

2. table만 덤프하려면
    * mysqldump -u root -p 데이터베이스명 테이블명 > 파일이름.sql   입력

3. 덤프파일로 테이블 만들기
    * mysql -u root -p 데이터베이스명 < 파일이름.sql    입력
    > 테이블만 덤프하는 상황도 데이터베이스명만 입력
