# mongoDB
몽고DB 기본 개념 및 NOSQL 정리

## NoSQL 기본 개념 정리
- https://kciter.so/posts/about-mongodb

## mongoDB Physical 데이타 저장 구조 설명
- https://kkyunstory.tistory.com/67

## mongoDB INDEX
- https://eunsour.tistory.com/72

## mongoDB wiredTiger 스토리지 엔진
- https://rastalion.me/mongodb%EC%9D%98-wiredtiger-%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80-%EC%97%94%EC%A7%84/

## mongoDB DOC
- https://docs.mongodb.com/manual/

## 한 레코드안의 배열들 조회하는 방법
- https://v3sjy.tistory.com/64

## MongoDB에 없거나 있는 컬럼으로 쿼리문 작성
- MongoDB 컬럼이 없는 경우의 데이터 불러오기
> 아래는 쿼리문을 사용하여 만약 name 이름의 컬럼이 없는 경우만 해당하는 값을 가져온다.
```
{ $exists: false }
db.collection.find('name': { $exists: false })
```

- MongoDB 컬럼이 있는 경우의 데이터 불러오기
> 그렇다면 반대로 값이 있는 경우의 데이터만 가져오려면?  $exist: true로 바꾸어준다.
```
{ $exists: true }
db.collection.find('name': { $exists: true })
```
## Document 를 조회하는 find() 메소드
- https://pro-self-studier.tistory.com/59


