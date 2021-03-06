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
<br>
<br>

여러개의 열을 가져오고 싶다면 콤마(,)로 구분하면 되고<br>
열 이름의 순서는 마음대로 바꿔도 된다.<br>
결과 또한 명령어에서 작성한 열 이름 순서대로 나온다. <br>

```sql
SELECT first_name, last_name, gender FROM employees;
```
![png](/images/MySQL_basic_files/select_multicol.png)

<br>

결과를 보니 first_name, last_name, gender 열이 작성한 순서대로 나왔음을 확인할 수 있다.<br>

## 특정한 조건의 데이터만 조회하는 <SELECT ... FROM ... WHERE>

### 기본적인 WHERE 절
WHERE절은 조회하는 결과에 특정한 조건을 줘서 원하는 데이터만 보고 싶을 때 사용 <br>
다음과 같은 형식을 갖는다. <br>

```sql
SELECT 필드이름 FROM 테이블이름 WHERE 조건식;
```
<br>

비교를 위해 WHERE 조건 없이 조회를 해보자 <br>
아 그전에 앞으로 미리 만들어둔 DB인 sqldb를 사용할 것임<br>
sqldb에 usertbl, buytbl 2개의 테이블이 있을 것임 <br>

```sql
SELECT * FROM usertbl;
```
![png](/images/MySQL_basic_files/select_usertbl.png)

<br>
usertbl 테이블에 있는 모든 열을 나타내야 한다. <br>
보면 10개의 행 즉 10개의 데이터가 확인된다.
<br><br>

```sql
SELECT * FROM usertbl WHERE name = '김경호';
```
![png](/images/MySQL_basic_files/select_where.png)

<br>
usertbl 테이블에 모든 열을 나타내는데 name = '김경호' 를 만족하는 행 (데이터)를 나타낸다. <br>
<br>

## 관계 연산자의 사용
이전에 사용한 테이블로 1970년 이후에 출생하고 신장이 182 이상인 사람의 아이디와 이름을 조회 해볼 것이다.<br>

```sql
SELECT userID, Name FROM usertbl WHERE birthYear >= 1970 AND height >= 182;
```
<br>
그 결과는 <br>
![png](/images/MySQL_basic_files/관계 연산자(1).png)

<br><br>

1970년 이후에 출생했거나 신장이 182 이상인 사람의 아이디와 이름을 조회해보면

```sql
SELECT userID, Name FROM usertbl WHERE birthYear >= 1970 OR height >= 182;
```
<br>

![png](/images/MySQL_basic_files/관계 연산자(2).png)

<br><br>

~ 했거나, ~ 또는 등 : <b>OR</b> 연산자를 사용<br>
~ 하고, ~ 면서, ~ 그리고 등 : <b>AND</b> 연산자를 사용<br>

<br>
위 처럼 조건 연산자(=, <, >, <=, >=, <>, != 등)와 관계 연산자(NOT, AND, OR 등)을 잘 조합하면 다양한 조건의 쿼리를 생성할 수 있다.
<br>

### BETWEEN~~~ AND와 IN() 그리고 LIKE

이번에는 키가 180 ~ 183인 사람의 이름과 키를 조회해보면
<br>

```sql
SELECT name, height FROM usertbl WHERE height >= 180 AND height <= 183;
```
![png](/images/MySQL_basic_files/where사용.png)

<br>

이것을 동일한 결과를 내놓는 다른 방법으로 <b>BETWEEN~~~ AND</b>를 사용할 수 있다. <br>

```sql
SELECT name, height FROM usertbl WHERE height BETWEEN 180 AND 183;
```

![png](/images/MySQL_basic_files/where사용.png)

<br>
같은 결과를 내놓는다. <br>
키의 경우 숫자로 구성되어 있고 연속된 값을 가지기 때문에 BETWEEN~~~ AND를 사용이 가능하다. <br>
하지만 지역이 '경기', '강원'인 사람을 찾는 경우에는 연속된 값이 아니므로 BETWEEN~~~ AND 사용이 불가능 하다. <br>
<br>

그렇다면 지역이 '경남', '경북'인 사람의 이름과 지역을 확인해 보자. <br>

```sql
SELECT name, addr FROM usertbl WHERE addr = '경남' OR addr = '경북';
```
<br>
![png](/images/MySQL_basic_files/지역확인.png)

<br>

위 경우처럼 연속적인 값이 아닌 이산적인 값을 위해 <b>IN</b>을 사용할 수 있다. <br>

```sql
SELECT name, addr FROM usertbl WHERE addr IN ('경남', '경북');
```
<br>

![png](/images/MySQL_basic_files/지역확인.png)

<br>
이전과 동일한 결과를 확인할 수 있다. <br><br>

문자열 내용을 검색하기 위해서는 <b>LIKE</b> 연산자를 사용할 수 있다. <br>

```sql
SELECT name, height FROM usertbl WHERE name LIKE '김%';
```
<br>
위 코드의 조건은 성이 '김'씨이고 그 뒤에는 무엇이든 허용한다는 의미로 <b>%</b>로 나타낸다. <br>

![png](/images/MySQL_basic_files/LIKE연산자.png)

<br>
결과를 보면 이름이 '김'이 제일 앞 글자인 경우의 이름과 키를 나타낸다.
<br><br>

만약 %처럼 무엇이든 허용하지말고 한 글자만을 허용하고 싶을떄는 어떻게 하지? <br>
그럴땐 <b>_</b>를 사용한다.
<br>

```sql
SELECT name, height FROM usertbl WHERE name LIKE '_종신';
```

<br>

![png](/images/MySQL_basic_files/한문자_연산자.png)

<br>

위 sql문을 보면 이름이 '_종신' 으로 종신 앞에 딱 한글자 아무거나 들어가는 모든 이름에 해당하는 이름과 키 정보를 조회하는 것이고 <br>
결과를 보면 이름 윤종신, 키 170에 해당하는 한 사람만이 존재하는 것을 확인할 수 있다. <br><br>

위에서 배운 '_', '%'를 조합해서 사용할 수 도 있다. <br>
'_123%' 경우 123 앞에 아무거나 딱 한 문자가 있고 뒤에는 아무 문자 무엇이든 허용한다는 의미로 <br>
예를들어 0<b>123</b>4, 0<b>123</b>56, 가<b>123</b>나3디2 <br>
등등 이 가능하다. 
<br><br>

### ANY / ALL / SOME 그리고 서브쿼리(SubQuery, 하위쿼리)