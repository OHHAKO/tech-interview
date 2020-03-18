## SQL 명령어 요약정리

### 명령어 요약

- select 옵션
    - distinct
    - count((distinct) 컬럼명 또는 *)
    - max(), min(), sum()
    - ifnull(a,b)
    - as
    - hour()
    - date_format(,) 
        - Y는 nnnn, y는 NN 으로 변환함
    - case when then else end
    
- from 옵션
    - right join , left join
    
- where 옵션
    - like, not like
    - is null, is not null
    - in( )

- group by
    - having

- order by 옵션 
    - desc, asc
    - limit n

- 컬럼값 가공
    - hour(datetime)


<br>

### 기본 명령어
- 레코드 삽입
    - insert into 테이블명 (컬럼1, 컬럼2, ...) values('value', value, ...);

- 레코드 검색
    - select 컬럼명 from 테이블명 where 조건

- 레코드 수정
    - update 테이블명 set 컬럼명=값 where 조건

- 테이블에 컬럼 추가
    - **alter table** 테이블명 **add role** 컬럼속성 **after** 컬럼명
    - 예시) name이름의 컬럼 추가: alter table animal_ins add role VARCHAR(20) NOT NULL after name;


### 기본 명령어에 옵션 추가
- 컬럼값 중복없이 검색
    - select distinct 컬럼명 from 테이블명

- 레코드 개수 검색 
    - count(컬럼명). select의 지정요소로 사용한다.
    - 테이블 전체 레코드 개수: select **count(*)** from animal_ins;
    - 특정 컬럼명을 지정할 경우 그 컬럼이 null이 아닌 레코드만 카운트된다.
    - select count(name) from animal_ins;
    - 이름 중복없이 카운트: select **count(distinct name)** from animal_ins;

- 최대값 레코드 검색
    - select max(컬럼명) from 테이블명
    - 전체 레코드가 아닌, 지정한 컬럼의 결과만 출력된다.

- 최소값 레코드 검색
    - select min(컬럼명) from 테이블명
    - 전체 레코드가 아닌, 지정한 컬럼의 결과만 출력된다.

- null값을 다른 값으로 바꾸어 출력
    - select ifnull(속성명,'변환값')
```sql
SELECT animal_type,
ifnull(name,'No name'),
sex_upon_intake
from animal_ins
order by animal_id;
```

- datetime 데이터값 hour단위로 변환 
    - hour함수에 datetime 삽입하면 자연수로 변환해준다. 
    - 조건에 having이 아니라 where을 쓴 이유는 count에 대한 조건을 걸려는게 아니라 **컬럼값에 따른 레코드 선택**을 하려는 목적이기 때문이다.
```sql
SELECT hour(datetime), count(*) 
from animal_outs
where hour(datetime) >=9
and hour(datetime) <= 19
group by hour(datetime);
```

- DATETIME을 DATE로 형 변환
    - 출력결과를 확인하려는 datetime 칼럼에 포맷함수를 적용해야 한다.
    - **DATE_FORMAT(datetime 칼럼명,포맷형)**
```sql 
SELECT animal_id, name, date_format(datetime,'%Y-%m-%d') as 날짜
from animal_ins;
```

- CASE WHEN 조건절 
    - 컬럼에 하나의 조건(WHEN)을 지정해 해당,비해당 경우 출력할 값을 지정한다.
    - 표기 순서 주의하라. CASE WHEN 컬럼에 대한조건 
    - THEN 결과1, ELSE 결과2 END
```sql 
SELECT animal_id, name, 
CASE WHEN sex_upon_intake LIKE '%neutered%' OR sex_upon_intake LIKE'%spayed%'
    THEN 'O'
    ELSE 'X'
    END AS '중성화'
FROM animal_ins
ORDER BY animal_id;
```

- 테이블 join
    - 외래어 animal_id 이용해 테이블 조인하기
    - animal_out에는 있지만 animal_ins 에는 id가 없는 레코드
    - animal_ins 테이블에는 없으므로 null 임을 이용하자
```sql
SELECT animal_outs.animal_id, animal_outs.name 
from animal_ins right join animal_outs on animal_ins.animal_id = animal_outs.animal_id
where animal_ins.animal_id is null
order by animal_id;
```

- where 조건 옵션
    - 특정 단어로 시작하는 레코드: update animal_ins set state='aged' **where** datetime **like '2000%'**;
    - 특정 단어로 시작하지 않는 레코드: select name from animal_ins **where** datetime **not like '2020%'**;
    - 특정 컬럼의 값이 없는 (null인) 레코드: SELECT animal_id from animal_ins **where** name **is null**;
```sql    
SELECT animal_id,name 
from animal_ins
where name like '%el%'
and animal_type='dog'
order by name;
```

- 데이터 null인 레코드 검색
    - where 컬럼명 is null


- 데이터 null아닌 레코드 검색
    - where 컬럼명 is not null
    - 위치 주의. where 컬럼명 바로 뒤에 온다.
    - SELECT animal_id from animal_ins where name is not null;

- 특정 데이터들 중 하나를 갖고있는 레코드 찾기
    - where 칼럼명 in('값1','값2','값3','값4'..)
    - 해당 칼럼이 저 괄호안 목록의 값을 갖고 있다면 색출된다.

이 코드를
```sql   
SELECT animal_id,name,sex_upon_intake
from animal_ins
where name='Lucy'
or name='ella'
or name='pickle'
or name='rogan';
```
이렇게 간단하게 축약 할 수 있다.
```sql   
SELECT animal_id,name,sex_upon_intake
from animal_ins
where name in('Lucy','ella','pickle','rogan');
```


- 컬럼값 마다 레코드 수
    - 컬럼의 값마다 레코드의 수를 알고싶다면 **select 컬럼명, count** 명시하고  **group by 컬렴명** 사용하기 
```sql
select animal_type,count(*) as count
from animal_ins
group by animal_type;
```

- group by에 조건걸기
    - group by 바로뒤에 사용하기
```sql
SELECT name, count(*) as count
from animal_ins
where name is not null
group by name
having count(*) > 1
order by name;
```

- 정렬조건 검색
    - order by. 컬럼 여러개에 오름차순, 내림차순을 우선순위대로 지정할 수 있다.
    - 먼저 나오는게 우선순위가 높다.
    - 반드시 from, where 절 이후에 사용한다.
    - For example) 이름 오름차순, 시간 내림차순 검색
```sql
SELECT animal_id,name,datetime 
from animal_ins 
order by name, datetime desc;
```

- 상위 레코드 n개 검색
    - limit 개수. 반드시 order by 조건 이후에 사용한다. 
    - 가장 오래된 레코드 하나 검색: SELECT name from animal_ins order by datetime asc **limit 1**;
    - 가장 최근 레코드 하나 검색: SELECT datetime from animal_ins order by datetime desc **limit 1**;

- datetime 가공하기
    - hour함수에 datetime 삽입하면 자연수로 변환해준다. 
    - 조건에 having이 아니라 where을 쓴 이유는 count에 대한 조건을 걸려는게 아니라 **컬럼값에 따른 레코드 선택**을 하려는 목적이기 때문이다.
```sql
SELECT hour(datetime), count(*) 
from animal_outs
where hour(datetime) >=9
and hour(datetime) <= 19
group by hour(datetime);
```



