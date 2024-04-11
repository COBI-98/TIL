# Oracle Job Scheduler 정리

<br>

## 잡 스케줄러 정의
시간, 일, 월 단위로 주기적으로 수행되는 쿼리나 프로시저 등을 자동으로 실행하는 동작을 말하며 우리일의 생산성을 높여준다.

잡 스케줄러 생성하기
```plsql
DECLARE
X NUMBER;
BEGIN
SYS.DBMS_JOB.SUBMIT(
  job => X,
  what => '프로시저명;',
  next_date => to_date('04-02-2021 20:29:24', 'dd/mm/yyyy hh24:mi:ss'),
  interval => 'SYSDATE +1/24/6',
  no_parse => FALSE
);
SYS.dbms_output.put_line('Job Number is : ' ||to_char(x));
commit;
END;
/
```
- job: 실행 job 번호
- what: 실행할 PL/SQL 프로시저 명
- next_date: job의 시작 날짜 지정
- interval: job을 수행한 후 다음 실행 시간까지의 시간을 지정
- no_parse: TRUE면 SUBMIT 시에 job을 parsing 하지 않음 

<br>

## 잡 스케줄러 내 프로시저 구성
- **DB에 추가된 job 삭제하기**

```plsql
DBMS_JOB.REMOVE(job number);
```

- **DB에 저장되어 있는 job의 field들 변경하기**

```plsql
DBMS_JOB.CHANGE(job number, what, next_date, interval);
```

- **job이 수행하는 작업 변경하기**

```plsql
DBMS_JOB.WHAT(job number, what);
```

- ** job이 다음에 실행될 날짜 변경하기**


```plsql
DBMS_JOB.NEXT_DATE(job number, next_date);
```
- **job 실행 주기 파라미터 변경하기**
```plsql
DBMS_JOB.INTERVAL(job number, interval);
```
- **DB에 저장된 job의 상태를 정상 또는 Broken 상태로 설정하기**

```plsqlDBMS_JOB.BROKEN(job number, broken, next_date);
```
- **job을 현재 session에서 즉시 수행하기**

```plsql
DBMS_JOB.RUN(job number);
```

잡 스케줄러 실행 확인하기
실행된 잡 스케줄러 확인:
```sql
SELECT * FROM USER_JOBS;
```
여기서 LAST_DATE, LAST_SEC는 잡 스케줄러가 마지막으로 실행된 시간을 확인할 수 있으며, <br> NEXT_DATE, NEXT_SEC는 다음에 실행될 시간, WHAT은 어떤 잡 스케줄러가 실행되는지를 확인할 수 있다.

<br>

## reference
[잡 스케줄러 생성](https://m.blog.naver.com/sensate1024/220855531878)
[잡 스케줄러 사용법](https://m.blog.naver.com/fromyongsik/222268984228)