# _MongoDB CRUD Operations_
[https://www.mongodb.com/docs/v4.4/crud](https://www.mongodb.com/docs/v4.4/crud)

- [생성 연산: Create Operations](#생성-연산-create-operations)
- [읽기 연산: Read Operations](#읽기-연산-read-operations)
- [갱신 연산: Update Operations](#갱신-연산-update-operations)
- [삭제 연산: Delete Operations](#삭제-연산-delete-operations)
- [대량 쓰기: Bulk write](#대량-쓰기-bulk-write)

CRUD 작업은 document 를 생성, 읽기, 업데이트 및 삭제한다.

## _생성 연산: Create Operations_
<br>

생성 또는 삽입은 collection 에 새 document 를 추가한다. collection 이 존재하지 않는 경우 document 삽입 시 컬렉션을 생성한다.

MongoDB 는 document 를 collection 에 삽입하기 위해 다음과 같은 방법을 제공한다.
- `db.collection.insertOne()` _New in version 3.2_ 
- `db.collection.insertMany()` _New in version 3.2_

MongoDB 에서 삽입은 단일 collection 대상이다. MongoDB의 모든 쓰기 작업은 단일 document 수준에서 [원자적 (atomic)](https://www.mongodb.com/docs/v4.4/core/write-operations-atomicity/) 이다.

![https://www.mongodb.com/docs/v4.4/images/crud-annotated-mongodb-insertOne.bakedsvg.svg](https://www.mongodb.com/docs/v4.4/images/crud-annotated-mongodb-insertOne.bakedsvg.svg)

더 상세한 내용은 [Insert Documents]() 를 참고하자.

## _읽기 연산: Read Operations_
<br>

읽기 작업은 collection 내 document 를 검색한다. 즉, document 를 위해 collection 을 쿼리한다. MongoDB 는 collection 내 문서를 읽는 방법을 다음과 같이 제공한다.
- `db.collection.find()`

반환 document 를 식별하는 [쿼리 필터 또는 기준 (criteria)](https://www.mongodb.com/docs/v4.4/tutorial/query-documents/#std-label-read-operations-query-argument) 을 지정할 수 있다.

![https://www.mongodb.com/docs/v4.4/images/crud-annotated-mongodb-find.bakedsvg.svg](https://www.mongodb.com/docs/v4.4/images/crud-annotated-mongodb-find.bakedsvg.svg)

자세한 내용은 아래 내용을 살펴보자.
- [Query Documents](https://www.mongodb.com/docs/v4.4/tutorial/query-documents) 
- [Query on Embedded/Nested Documents](https://www.mongodb.com/docs/v4.4/tutorial/query-embedded-documents) 
- [Query an Array](https://www.mongodb.com/docs/v4.4/tutorial/query-arrays)
- [Query an Array of Embedded Documents](https://www.mongodb.com/docs/v4.4/tutorial/query-array-of-documents)

## _갱신 연산: Update Operations_
<br>

갱신 작업은 collection 의 기존 document 를 수정한다. MongoDB 는 document 를 갱신하기 위해 다음 메서드를 제공한다.
- `db.collection.updateOne()` _버전 3.2의 새로운 기능_
- `db.collection.updateMany()` _버전 3.2의 새로운 기능_
- `db.collection.replaceOne()` _버전 3.2의 새로운 기능_

MongoDB 에서 업데이트 작업은 단일 collection 대상이다. MongoDB 의 모든 쓰기 작업은 단일 document 수준에서 원자적(atomic)이다.

업데이트할 document 를 식별하는 기준(criteria) 또는 필터(filter)를 지정할 수 있다. 이러한 필터는 읽기 작업과 동일한 syntax 을 사용한다.

![https://www.mongodb.com/docs/v4.4/images/crud-annotated-mongodb-updateMany.bakedsvg.svg](https://www.mongodb.com/docs/v4.4/images/crud-annotated-mongodb-updateMany.bakedsvg.svg)

자세한 내용은 [Update Documents](https://www.mongodb.com/docs/v4.4/tutorial/update-documents) 를 참고하자.

## _삭제 연산: Delete Operations_
<br>

삭제 작업은 collection 의 document 를 제거한다. MongoDB 는 document 를 삭제하기 위해 다음과 같은 방법을 제공한다.
- `db.collection.deleteOne()` _버전 3.2의 새로운 기능_
- `db.collection.deleteMany()` _버전 3.2의 새로운 기능_

MongoDB 삭제 작업은 단일 collection 대상이다. MongoDB 의 모든 쓰기 작업은 단일 document 수준에서 원자적(atomic)이다.

제거할 document 를 식별하는 기준(criteria) 또는 필터(filter)를 지정할 수 있다. 이러한 필터는 읽기 작업과 동일한 syntax 을 사용한다.

![https://www.mongodb.com/docs/v4.4/images/crud-annotated-mongodb-deleteMany.bakedsvg.svg](https://www.mongodb.com/docs/v4.4/images/crud-annotated-mongodb-deleteMany.bakedsvg.svg)

자세한 내용은 [Delete Documents](https://www.mongodb.com/docs/v4.4/tutorial/remove-documents) 를 참고하자.

## _대량 쓰기: Bulk write_
<br>

MongoDB 는 대량으로 쓰기 작업을 수행하는 기능을 제공한다(a.k.a bulk). 자세한 내용은 대량 쓰기 작업을 참조하십시오.

---
[Index 로 이동](https://github.com/jx2lee/getting-started-with-mongodb#index)
