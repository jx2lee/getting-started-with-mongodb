# _Capped Collections_
[https://www.mongodb.com/docs/v4.4/core/capped-collections](https://www.mongodb.com/docs/v4.4/core/capped-collections/)

## _개요 (Overview)_
[Capped collection](https://www.mongodb.com/docs/v4.4/reference/glossary/#std-term-capped-collection) 은 삽입 순서에 따라 document 를 삽입하고 검색하는 `높은 처리 작업을 지원하는 고정 크기 collection` 이다.
Capped collection 은 **순환 버퍼 (circular buffer)** 와 유사한 방식으로 동작한다.
collection 이 할당된 공간을 모두 채우면, collection 에서 가장 오래된 document 를 덮어써 새 document 를 저장한다.
Capped collection 생성에 대한 자세한 내용은 [createCollection()](https://www.mongodb.com/docs/v4.4/reference/method/db.createCollection/#mongodb-method-db.createCollection) 을 참조하거나 [create](https://www.mongodb.com/docs/v4.4/reference/command/create/#mongodb-dbcommand-dbcmd.create) 를 참조하자.

> Capped collection 대안으로 MongoDB 의 TTL(Time To Live) 인덱스를 고려할 수 있다.
> TTL 을 설정하여 [데이터 만료](https://www.mongodb.com/docs/v4.4/tutorial/expire-data) 설명대로 index 를 사용하면, 날짜 필드 값과 index TTL 을 기반으로 collection 에 데이터를 만료 및 제거할 수 있다.

> [순환 버퍼(_circular buffer_)](https://ko.wikipedia.org/wiki/%EC%9B%90%ED%98%95_%EB%B2%84%ED%8D%BC)
>  
> 고정된 크기의 버퍼를 양 끝이 연결된 것처럼 사용하는 자료 구조이다. 원형 버퍼를 이용하면 단순 배열을 deque (양쪽 끝에 삽입/삭제가 가능한 자료구조) 처럼 사용할 수 있다.

## _동작 (Behaviour)_
### _삽입 순서 (Insertion Order)_
Capped collection 은 삽입 순서를 보장한다.
쿼리는 삽입 순서로 문서를 반환하기 위해 인덱스를 사용하지 않는다.
이 인덱싱 오버헤드가 없으면 Capped collection 이 더 높은 삽입 처리량을 지원할 수 있다.

### _가장 오래된 document 자동 삭제 (Automatic Removal of Oldest Documents)_
새 document 를 위한 공간을 만들기 위해 Capped collection 은 스크립트나 명시적 작업 없이 collection 에서 가장 오래된 document 를 자동으로 제거한다.
Capped collection 사용에 대한 사례는 다음과 같다.
- 대용량 시스템에서 발생한 로그 정보를 저장한다. index 없이 Capped collection 에 document 를 저장하는 것은 로그를 파일 시스템에 직접 쓰는 속도에 가깝다. 또한 내장된 선입 선출 (_FIFO_) 특징은 이벤트 순서를 유지하면서 스토리지 사용을 관리한다.
- 소량의 데이터를 캐시한다. 캐시는 많이 쓰기보다는 읽기 때문에, collection 이 항상 작업 세트 (working set, e.g RAM) 에 남아 있는지 확인하거나 필요한 인덱스 쓰기 패널티 (write penalty) 를 인정해야한다.

예로, 로그를 replica set 에 저장하는 [oplog.rs](https://www.mongodb.com/docs/v4.4/reference/glossary/#std-term-oplog) collection 은 capped 컬렉션을 사용한다.
MongoDB 4.0부터 다른 capped 컬렉션과 달리 oplog는 대다수 커밋 지점을 삭제하지 않도록 구성된 크기 제한을 초과하여 커질 수 있습니다.

### __id 인덱스 (id Index)_
Capped collection 에는 기본적으로 _id 필드와 _id 에 대한 인덱스가 존재한다.

## _제약 & 권장 (Restrictions and Recommendations)_
### _업데이트 (Update)_
Capped collection document 를 업데이트하려는 경우, **collection 스캔을 사용하지 않도록 인덱스를 만드는 것이 좋다.**

### _문서 크기 (Document size)_
버전 3.2에서 변경되었다. 업데이트 또는 교체 작업 (replacement operation) 으로 문서 크기가 변경되면, 작업을 실패한다.

### _문서 삭제 (Document Deletion)_ 
Capped collection 에서 document 를 삭제할 수 없다. collection 의 모든 document 를 제거하려면 `drop()` 메서드를 사용하여 컬렉션을 삭제하고 다시 만들어야한다.

### _샤딩 (Sharding)_
Capped collection 은 샤딩할 수 없다.

### _쿼리 효율성 (Query Efficiency)_
collection 에서 가장 최근 삽입된 데이터를 효율적으로 검색한다. 이는 로그 파일에서 tail 명령을 사용하는 것과 유사하다.

### _집계 $out (Aggregation $out)_
집계 파이프라인 (Aggregation pipeline stage) $out 은 Capped collection 에 결과를 저장할 수 없다.

### _트랜잭션 (Transaction)_
MongoDB 4.2 부터 [트랜잭션](https://www.mongodb.com/docs/v4.4/core/transactions) 안에서 Capped collection 에 데이터를 저장할 수 없지만, 읽기는 지원한다.

## _프로시저 (Procedures)_

### _Capped Collection 생성 (Create a Capped Collection)_
`db.createCollection()` 메서드를 사용하여 명시적으로 capped collection 을 만들어야 한다.
컬렉션의 최대 크기를 바이트 단위로 지정해야 하며, MongoDB 는 컬렉션에 이 크기를 미리 할당한다.
capped collection 크기는 내부 오버헤드를 위한 약간의 공간을 포함한다.

```javascript
db.createCollection( "log", { capped: true, size: 100000 } )
```

크기 필드가 4096보다 작거나 같으면 한도는 4096 바이트이다. 그렇지 않으면 MongoDB는 제공된 크기를 256의 정수 배수로 만든다.
또한 다음 문서에서와 같이 max 필드를 사용하여 컬렉션에 대한 최대 문서 수를 지정할 수도 있다.

```javascript
db.createCollection("log", { capped : true, size : 5242880, max : 5000 } )
```

> 최대 문서 수를 지정하는 경우에도 size 값은 항상 필수다. MongoDB 는 컬렉션이 최대 문서 수(max)에 도달하기 전 최대 크기 제한(size)에 도달하면, 오래된 문서를 제거합니다.

### _Capped Collection 쿼리 (Query a Capped Collection)_
쿼리 순서가 지정되지 않은 capped collection 에 `find()` 를 수행하면, MongoDB 는 결과의 순서가 삽입 순서와 동일함을 보장한다.
역 삽입 순서로 문서를 검색하려면 다음 예제와 같이 `$natural` 매개변수가 -1로 설정된 sort() 메서드와 함께 `find()` 를 사용한다.

```javascript
db.cappedCollection.find().sort( { $natural: -1 } )
```

### _제한 확인 (Check if a Collection is Capped)_
`isCapped()` 로 다음과 같이 컬렉션에 제한이 있는지 확인한다.

```javascript
db.collection.isCapped()
```

### _Collection -> Capped Collection 으로 변환 (Convert a Collection to Capped Collection)_
`convertToCapped` 명령을 사용하여 capped collection 으로 변경할 수 있다.

```javascript
db.runCommand({"convertToCapped": "mycoll", size: 100000});
```

size 매개변수는 capped collection 크기를 바이트 단위로 지정한다.

이것은 작업 기간 동안 데이터베이스 배타적 잠금 (exclusive lock) 을 유지한다.
동일 데이터베이스를 잠그는 다른 작업은 변경 작업이 완료될 때까지 차단한다. 몇 가지 클라이언트 작업에서 어떤 잠금을 사용하는지는
[링크](https://www.mongodb.com/docs/v4.4/faq/concurrency/#std-label-faq-concurrency-operations-locks) 를 참조하자.

> [exclusive lock](https://jaeseongdev.github.io/development/2021/06/16/Lock%EC%9D%98-%EC%A2%85%EB%A5%98-(Shared-Lock,-Exclusive-Lock,-Record-Lock,-Gap-Lock,-Next-key-Lock)/): 특정 row 를 변경할 때 사용되는 lock 으로, 해당 lock 이 해제될 때까지 다른 트랜잭션은 read 작업을 위해 Shared lock 을 걸거나 쓰기 작업을 위한 Exclusive lock 을 걸 수 없다. 

### _테일러블 커서 (Tailable Cursor)_
capped collection 과 함께 tailable cursor 를 사용할 수 있다.
Unix `tail -f` 명령과 유사하게 tailable cursor 는 capped collection 끝을 "뒤따라 (tail)" 간다.
새 document 가 삽입되면 tailable cursor 를 사용하여 문서 검색을 계속할 수 있다.
Tailable cursor 생성에 대한 정보는 [Tailable cursor](https://www.mongodb.com/docs/v4.4/core/tailable-cursors/)를 참고하자.

> [cursor](https://www.mongodb.com/docs/v4.4/reference/glossary/#cursor):
> 쿼리 결과 집합에 대한 포인.  클라이언트는 커서를 통해 반복하여 결과를 검색할 수 있다. 기본적으로 세션 내 열리지 않는 커서는 10분 동안 활동이 없으면, 자동으로 timeout 한다.

---
[Index 로 이동](https://github.com/jx2lee/getting-started-with-mongodb#index)
