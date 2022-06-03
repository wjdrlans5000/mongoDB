# mongoDB
몽고DB 기본 개념 및 NOSQL 정리
[01. 인덱싱](./01. 인덱싱)

## NoSQL 기본 개념 정리
- https://kciter.so/posts/about-mongodb
- https://ryu-e.tistory.com/2
- https://dreamcoding.tistory.com/5

## mongoDB Physical 데이타 저장 구조 설명
- 컬렉션 > 도큐먼트의 집합 > 도큐먼트의 사이즈는 16MB를 넘을수 없음.
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

## 스크립트
- 배열 요소 찾기
```
db.getCollection('CIData_00').find({"iSr.data":{"$elemMatch":{"tdi":{$gte:"20211101165000",$lte:"20211102165000"}}}},{"_id":true,"iSr":true})
db.getCollection('CIData_00').find({last_update_datetime:{$gte:ISODate("2021-11-01T00:00:00"),$lt:ISODate("2021-11-02T00:00:00")},"iSr.data":{"$elemMatch":{"tdi":{$gte:"20211101165000",$lte:"20211102165000"}}}},{"_id":true})
```

- update
```
db.getCollection('CIData_11').updateOne(
{"_id" : "534886b8056aa3b45319a9a217f0142faff-7ef3"},
{"$set" : {
			"iCr" : {
				"syncTime" : ISODate(),
				"data" : [ 
            {
                "siteCode" : "c3505425a617d467e7085587e4407f1b",
                "pCode" : "1000000504",
                "td" : "20220216160312",
                "tdi" : "20220216160312"
            }, 
            {
                "siteCode" : "390ab1b0fcb31727199fcf187b0de9b4",
                "pCode" : "1000006070",
                "td" : "20220216160209",
                "tdi" : "20220216160209"
            }, 
            {
                "siteCode" : "f45aa71ae71b247f5c9257b05e3cb30a",
                "pCode" : "352",
                "td" : "20220216160108",
                "tdi" : "20220216160108"
            }
				]
			}
		}
}
)
```
- find $in
```
db.getCollection('CIData_05').find({_id:{$in : ["48106344e3a399c1-1fc54852170c45b1e9b1ec6", 
    "daad1ddf6622498953c322ca16b3085de28-2bbc", 
    "0f868c22c59738a38d3f8f71704c750415-405", 
    "8db935eb34c7e8a1-77e222db16a517da65f24a7"]}})
```

- 스크립트로 원하는 형태의 데이터 만들어서 출력
```
var findData = db.getCollection('CIData_01').find({'iSr':{$exists:true}},{'_id':true, 'iSr.data': 1}).sort({'last_update_datetime':1}).limit(50000);
var result = [];

while (findData.hasNext()){
    var allData = findData.next();
    var srData = allData.iSr.data;
    var jsonObject = {auId:'', data:[]};
    jsonObject.auId = allData._id;
    
    for(var i = 0; i< srData.length; i++ ) {
       var data = srData[i];
       var dataObj = {siteCode:'', pCode:'', tdi:''}
       dataObj.siteCode = data.siteCode;
       dataObj.pCode = data.pCode;
       dataObj.tdi = data.td;
       jsonObject.data.push(dataObj);
    } ;
    
    result.push(jsonObject);
    
};
```
- 특정 documnet의 object(iCr) 삭제
```
db.getCollection('CIData_05').updateOne(
{"_id" : "48106344e3a399c1-1fc54852170c45b1e9b1ec6"},
{$unset : {"iCr" : 1}}
)
```
- 특정 object(iCr)가 존재하는 경우 삭제
```
db.getCollection('CIData_00').updateMany(
{'iCr':{$exists:true}},
{$unset : {"iCr" : 1}}
)
```

- id 기준 다중 업데이트
```
db.getCollection('CIData_00').updateMany(
{_id:{$in : [
  "ff78af428ca3e5504f330cf315c6b258abd-7b3b",
  "df301a6e91bfe15378c7adc315c1c4a112b-4038"
    ]}},
{$unset : {"iCr" : 1}}
)
```

- 서버 모니터링
```
db.serverStatus().tcmalloc
```

- 배열의 카운트 조회
```
db.getCollection('CIData_00').find({last_update_datetime:{$gte:ISODate("2022-05-24T09:09:00"),$lt:ISODate("2022-05-24T09:27:00")},"CDP":{$exists:true},$where:"this.CDP.data.length > 2"})
```

- 배열 요소가 '' 인 데이터 조회
```
db.getCollection('CIData_00').find({last_update_datetime:{$gte:ISODate("2022-05-30T01:04:00"),$lt:ISODate("2022-05-30T01:24:00")},"CDP":{$exists:true},"iCr":{$exists:true},"iCr.data":{"$elemMatch":{"pCode":{$eq:''}}}},{"iCr":true,"CDP":true})
```
