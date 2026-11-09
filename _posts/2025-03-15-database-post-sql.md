---
title: "SQL 정리 및 트랜잭션"
description: "DDL, DML, DCL 및 Transaction"
date: 2025-03-15 09:00:00 +0900
categories: [Database]
tags: [sql, ddl, dml, transaction]
permalink: /database/post-name-here/  # 기존 URL 유지 (검색 노출·utterances 댓글 연결)
---

## SQL 문법 정리 및 트랜잭션

----

## DDL(Data Define Language)
* DDL은 SCHEMA, DOMAIN, TABLE, VIEW, INDEX를 정의하거나 변경 또는 삭제할 때 사용하는 언어
* CREATE, ALTER, DROP

## DML(Data Manipulation Language)
* DML은 사용자가 질의어를 통하여 데이터를 실질적으로 처리하는데 필요한 언어
* SELECT, INSERT, DELETE, UPDATE

## DCL(Data Control Language)
* DCL은 데이터의 보안, 무결성, 회복, 병행 수행 제어 등을 정의하는데 필요한 언어
* COMMIT, ROLLBACK, GRANT, REVOKE

----

## 트랜잭션
* 그룹으로 처리되어야 할 구문들의 집합
* 데이터의 일관성을 보존하고 안정적인 데이터 복구를 위해 사용

트랜잭션의 예시
1. A은행에서 B은행으로 100억원을 송금하는 상황
2. 알 수 없는 오류로 A은행에서 돈이 인출되었지만 B은행에서 입금되지 않음
3. A은행 계좌의 출금을 취소하거나, 출금된 금액만큼 B은행 계좌에 송금하면됨
4. 하지만 위 방법은 더 심한 오류를 발생시킬수 있음
5. 따라서 거래가 모두 성공적으로 끝난 후에야 이를 완전한 거래로 승인하고, 거래 과정에서의 오류가 발생하면 처음부터 거래를 다시 시작해야함(원자성)
6. 이를 DB관점으로 보면 데이터를 연산하는 과정에서 갱신, 삭제 등의 이상이 발견되면 모든 작업의 상태를 원상복구 해야함
7. DB 연산과정이 성공적으로 수행되었을때만 DB에 반영

## 트랜잭션의 특징
* 원자성 : 트랜잭션이 DB에 모두 반영되던가 반영되지 않아야 한다.
* 일관성 : 트랜잭션의 처리 결과가 항상 일관성이 있어야 한다
* 독립성 : 트랜잭션이 진행중에는 그 어떤 트랜잭션이 처리과정에서 끼어들 수 없다
* 지속성 : 트랜잭션이 처리될 경우 영구적으로 반영되어야 한다

## 트랜잭션의 상태
* 활동(Active) : 트랜잭션이 실행중인 상태
* 실패(Failed) : 트랜잭션이 오류가 발생되어 중단된 상태
* 철회(Aborted) : 트랜잭션이 비정상적으로 종료되어 ROLLBACK 연산을 수행한 상태
* 부분적 완료(Partially Commited) : COMMIT 연산이 실행되기 전 작성한 것들을 저장하지 않은 상태
* 완료(Committed) : 트랜잭션이 성공적으로 처리된 상태

## 트랜잭션 연산
* COMMIT
  * DB에 영구적으로 저장
  * 트랜잭션의 종료과정
  * COMMIT을 수행하면 이전 데이터 UPDATE
* ROLLBACK
  * 변경 사항을 취소
  * 마지막 COMMIT을 완료한 시점으로 ROLLBACK
  * ALL-OR-NOTHING 방식으로 연산 수행
* SAVEPOINT
  * ROLLBACK 명령을 실행할 경우 DML 작업 전체가 취소되는데 특정 부분에서 트랜잭션을 취소할 수 있다
  * 여러개의 SQL문을 실행하는 트랜잭션의 중간 단계에서 SAVEPOINT를 지정할 수 있다
  * SAVEPOINT를 지정하려면 __SAVEPOINT [이름];__ 을 작성하면 된다
  * SAVEPOINT를 사용하기 위해선 __ROLLBACK TO [이름];__ 을 통해 SAVEPOINT 까지 ROLLBACK 할 수 있다

```mysql
create table money_transaction(
	id int Primary key,
    amount int not null,
    account_from varchar(30) not null,
    account_to varchar(30) not null
);

insert into money_transaction values(0, 300, "A", "B");
insert into money_transaction values(2, 400, "A", "C");
insert into money_transaction values(23, 200, "C", "B");
insert into money_transaction values(45, 100, "B", "A");
```
```mysql
DELETE FROM money_transaction WHERE ID=0;
COMMIT;
SAVEPOINT S1;
DELETE FROM money_transaction WHERE ID=2;
SAVEPOINT S2;
DELETE FROM money_transaction WHERE ID=23;
SAVEPOINT S3;
DELETE FROM money_transaction WHERE ID=45;
ROLLBACK TO S2;
SELECT * FROM TRAT3;
COMMIT;
ROLLBACK  TO S1;
```

이 경우 11번줄의 COMMIT을 하면 앞에 있는 SAVEPOINT는 전부 날라가고 COMMIT까지만 완료된다
주의할 점은 COMMIT을 하게되면 이전 과정의 SAVEPOINT는 적용되지 않는다.
