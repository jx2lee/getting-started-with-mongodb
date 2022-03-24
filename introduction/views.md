# _Views_
[https://docs.mongodb.com/v4.4/core/views](https://docs.mongodb.com/v4.4/core/views/)

MongoDB View 는 다른 collection  또는 View 의 [집계 파이프라인](https://docs.mongodb.com/v4.4/core/aggregation-pipeline/#std-label-aggregation-pipeline) 에 의해 정의된 쿼리 가능 개체이다.
View 데이터는 디스크에 유지하지 않고, 클라이언트가 View 를 쿼리할 때 On-demand 로 계산한다.
클라이언트가 View 를 쿼리할 수 있는 [권한](https://docs.mongodb.com/v4.4/core/authorization/#std-label-authorization) 을 갖도록 요구할 수 있으며 View 쓰기 작업은 지원하지 않는다.

예를 들어 다음과 같이 사용할 수 있다.
- 개인 정보 또는 개인 정보(PII)를 제외하기 위해 직원 collection 의 View 를 생성한다.
- 애플리케이션은 PII 를 포함하지 않도록 View 를 이용해 쿼리할 수 있다. 수집된 센서 Collection 의 View 를 생성하여 계산된 필드 및 메트릭을 추가한다. 응용 프로그램은 간단한 `find` 연산자를 사용해 데이터를 쿼리할 수 있다.
- 각각 재고 및 주문 내역을 포함하는 두 개 collection 을 결합하는 View 를 만든다. 애플리케이션은 복잡한 기본 파이프라인을 관리하거나 이해하지 않고도 결합된 데이터만을 쿼리할 수 있다.

View 를 쿼리할 때, MongoDB 는 기본 파이프라인에 클라이언트 쿼리를 추가하고 결합된 파이프라인의 결과를 클라이언트에 반환한다.
MongoDB 는 파이프라인에 [집계 파이프라인 최적화](https://docs.mongodb.com/v4.4/core/aggregation-pipeline-optimization/) 를 적용할 수 있다.

## _View 생성 (Create Views)_
View 생성하거나 정의하려면,
- `db.createCollection()` 또는 [create](https://docs.mongodb.com/v4.4/reference/command/create/#mongodb-dbcommand-dbcmd.create) 명령을 사용한다.
    ```javascript
    db.createCollection(
        "<viewName>",
        {
            "viewOn" : "<source>",
            "pipeline" : [<pipeline>],
                "collation" : { <collation> }
        }
    )    
    ```
- `db.createView()` 를 사용한다.
    ```javascript
    db.createView(
      "<viewName>",
      "<source>",
      [<pipeline>],
      {
        "collation" : { <collation> }
      }
    )
    ```
  
> (주의) 원본 collection 과 동일한 데이터베이스에 View 를 생성해야 한다.

## _동작 (Behavior)_
### _읽기 전용 (Read Only)_
다음 읽기 작업은 View 에서 사용 가능하다.
- `db.collection.find()`
- `db.collection.findOne()`
- `db.collection.aggregate()`
- `db.collection.countDocuments()`
- `db.collection.estimatedDocumentCount()`
- `db.collection.count()`
- `db.collection.distinct()`

### _인덱스 사용 및 정렬 (Index Use and Sort Operations)_
- 뷰는 default 로 원본 collection 인덱스를 사용한다.
- 인덱스가 원본 collection 에 있으므로 View 에서 직접 인덱스를 생성, 삭제하거나 목록을 가져올 수 없다.
- MongoDB 4.4 부터 View 에서 find 실행 시 [`$natural`](https://docs.mongodb.com/v4.4/reference/operator/meta/natural/#mongodb-operator-metaOp.-natural) 을 지정할 수 있다. 이전 버전의 View 에서는 `$natural` 을 지원하지 않는다.
- View 집계 파이프라인에는 차단 정렬 (blocking sort) 및 차단 그룹 작업 (blocking group operation) 에 100MB 메모리 제한을 적용한다. MongoDB 4.4 부터 View 에 `allowDiskUse: true` 를 사용하여 정렬 및 그룹 작업을 차단하기 위해 **임시 파일**을 사용할 수 있도록 할 수 있다.
- MongoDB 4.4 이전에는 집계 명령만 allowDiskUse 옵션을 허용했습니다.

> 정렬 작업 메모리 제한 차단 [blocking sort operation memory limit](https://docs.mongodb.com/v4.4/reference/limits/#mongodb-limit-Sort-Operations) 에 대한 자세한 내용은 링크 참조

### _Projection Restrictions_
`find()` 는 아래 투영 연산자를 지원하지 않는다.
- `$`
- `$elemMatch`
- `$slice`
- `$meta`

### _불변성 (Immutable Name)_
view 의 이름을 변경할 수 없다.

### _View 생성 (View Creation)_
- 읽기 작업 중 요청 시 계산되고, 기본 집계 파이프라인의 일부로 읽기 작업을 실행한다. 따라서 view 는 다음과 같은 작업을 지원하지 않는다. 
  - `db.collection.mapReduce()`
  - `$text` 연산자 (집계의 $text 연산은 첫 번째 단계에서만 유효하므로)
  - $geoNear 파이프라인
- view 를 만드는 데 사용된 집계 파이프라인이 _id 필드를 표시하지 않는 경우, view 의 document 에는 _id 필드가 없다.

view 를 쿼리할 때, 
- `db.collection.find()` 에 대한 쿼리 필터, 프로젝션, 정렬, 건너뛰기(skip), 제한(limit) 및 기타 작업은 동등한 [집계 파이프라인 단계](https://docs.mongodb.com/v4.4/reference/operator/aggregation-pipeline/#std-label-aggregation-pipeline-operator-reference) 로 변환한다.
- 변환된 집계 파이프라인 단계 (aggregation pipeline stage) 는 view 의 집계 파이프라인 끝에 추가한다. 이는 view 생성 시 설정한 기본 파이프라인을 수정하지 않는다.
- [집계 파이프라인 최적화](https://docs.mongodb.com/v4.4/core/aggregation-pipeline-optimization/) 는 view 집계 파이프라인 단계를 재구성하여 성능을 개선한다. 쿼리 결과는 변경되지 않는다.

### _View 샤드 (Sharded View)_
원본 collection 이 분할된 경우 view 는 분할된 것으로 간주한다. 따라서 `$lookup` 및 `$graphLookup` 작업의 from 필드에 분할 view 를 지정할 수 없다.

### _View 정렬 (Views and Collation)_
> [Collation](https://mongodb.github.io/node-mongodb-native/2.2/tutorials/collations): 문자열을 비교할 때 특정 언어의 규칙을 준수하는 일련의 규칙, 이후 정렬이라고 표현할 예정이다.

- view 생성 시점에 **기본 정렬 (default collation)** 을 지정할 수 있다. **정렬 (collation)** 이 지정되지 않은 경우 view 의 기본 정렬은 "단순 (simple)" 이진 비교 정렬 (binary comparison collator) 이다. 즉, view 는 collection 의 기본 정렬을 상속하지 않는다.
- view 문자열 비교는 기본 정렬을 이용한다. 기본 정렬을 변경하거나 재정의하려는 작업은 실패한다.
- 다른 view 에서 view 를 만드는 경우, 원본 view 의 정렬과 다르게 지정할 수 없다.
- `$lookup` 또는 `$graphLookup` 과 같이 여러 view 에 대한 집계를 수행하는 경우, 동일한 정렬이 있어야 가능하다.

### _View 의 공개범위 (Public View Definition)_
`db.getCollectionInfos()` 및 `db.getCollectionNames()` 와 같은 collection 을 나열하는 작업 결과에는 view 를 포함한다.

> view 는 기본적으로 공개 (public) 이다. 즉, `db.getCollectionInfos()` 및 `explain` 에는 뷰를 정의하는 파이프라인을 포함한다. **따라서 view 생성 시 민감 필드와 값을 직접 참조하지 않도록 한다.**

### _View 삭제와 수정 (Drop a View & Modify a View)_
view 제거는 db.collection.drop() 메소드를 사용한다. view 수정은 삭제 후 다시 생성 하거나, `collMod` 을 사용하여 view 를 수정할 수 있다.

### _지원 연산 (Supported Operations)_

| Commands                    | Methods                                                                                                                                                                                                       |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| create                      | db.createCollection()<br/>db.createView()                                                                                                                                                                     |
| collMod                     |                                                                                                                                                                                                               |
|                             | db.getCollection()<br/>db.getCollectionInfos()<br/>db.getCollectionNames()                                                                                                                                    |
| find<br/>distinct<br/>count | db.collection.aggregate()<br>db.collection.find()<br>db.collection.findOne()<br>db.collection.countDocuments()<br>db.collection.estimatedDocumentCount()<br>db.collection.count()<br>db.collection.distinct() |

---
[Index 로 이동](https://github.com/jx2lee/getting-started-with-mongodb#index)
