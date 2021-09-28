---
title: "SQL 기본"
categories:
  - MySQL
tags:
  - SELECT
  - INSERT
  - UPDATE
  - DELETE
use_math: true
---

이번에는 기본적인 SQL 문장인 SELECT, INSERT, UPDATE, DELETE에 대해 알아볼 것이다. <br><br>

실습을 통해 진행할 것인데<br>
샘플 데이터베이스로 emplyees라는 이름을 가진 데이터베이스를 사용할 것이다 <br><br>

# SELECT
: SELECT는 데이터베이스 내의 테이블에서 원하는 정보를 추출하는 명령이다.<br>
<br>
SELECT문이 매우 간단해 보이지만 다양한 옵션이 존재한다. 하지만 많이 사용되는 형태로 구조를 요약해보면 <br>

```sql
SELECT select_expr
    [FROM table-references]
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}]
    [HAVING where_condition]
    [ORDER BY {col_name | expr | position}] 
```
<br>

위의 내용도 복잡해 보인다면 좀더 자주 사용하고 간단한 형태로 구조를 나타내면 <br>

```sql
SELECT 열 이름
FROM 테이블 이름
WHERE 조건
```

<br>

간단히 SELECT 구문은 이러한 형태임을 알 수 있고 지금부터는 하나하나 옵션을 붙여가며 SELECT에 대해 알아보자. <br><br>

## USE 구문
SELECT문 뿐만 아니라 다른 명령어를 사용하기 전에 사용할 데이터베이스를 지정해야 한다. <br>
따라서 해당 데이터베이스를 지정하거나 변경해야할 경우 USE 구문을 사용한다.<br>
<br>

현재 사용하는 데이터베이스를 지정하거나 변경하는 구문은 아래와 같다.<br>
```sql
USE 데이터베이스_이름;
```
<br>

만약 employees라는 이름을 가진 데이터베이스를 사용하기 위해 쿼리 창에 아래와 같이 입력하면 된다.<br>
```sql
USE employees;
```
<br><br>

만약 이렇게 데이터베이스를 지정해둔다면 다시 USE문을 사용한다던지 다른 DB를 사용하겠다는 명시를 하지 않는다면 모든 SQL문은 employees라는 이름을 가진 데이터베이스에서 수행된다.

## SELECT와 FROM

```sql
USE employees;
SELECT * FROM titles;
```
<br>
-> employees 데이터베이스를 선택한 후
-> titles라는 테이블에서 모든 열의 내용을 가져와라
<br>

*이 나온 위치는 해당 테이블의 열 이름이 나오는 위치이다.<br>
그런데 열 이름대신 *가 나오면 모든 열을 의미한다. <br>
<br>
FROM 다음에는 테이블/뷰의 항목이다.
<br><br>

사실 원칙적으로는 테이블의 전체 이름은 <br>
"데이터베이스이름.테이블이름" 형식으로 표현된다.<br>
따라서 위의 SELECT 구문을 정확히 작성하면 <br>

```sql
SELECT * FROM emplyees.titles;
```
<br>
가 된다.
<br><br>

그러나 데이터베이스 이름을 생략해도 이전에 USE를 이용하여 선택된 데이터베이스 이름이 자동으로 붙게 된다. <br><br>

titles 테이블의 모든 열을 가져오니 결과가 아래와 같다 <br>
![png](/images/MySQL_basic_files/select_star.png)
<br>
데이터가 무수히 많지만 일부분만 보인 것이다. <br><br>

그럼 이제는 전체 열이 아닌 특정 열만 가져와보자 <br>
사원 테이블의 이름만 가져와 보쟈<br>

```sql
SELECT first_name FROM employees;
```
<br>
titles 테이블의 first_name 열만을 가져오면 아래와 같다.<br>
![png](/images/MySQL_basic_files/select_firstname.png)