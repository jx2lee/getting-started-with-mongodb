# _Insert Documents_
[https://www.mongodb.com/docs/v4.4/tutorial/insert-documents](https://www.mongodb.com/docs/v4.4/tutorial/insert-documents/)

- [단일 Document 삽입: Insert a Single Document](#단일-document-삽입-insert-a-single-document)
- [복수 Document 삽입: Insert Multiple Document](#복수-document-삽입-insert-multiple-document)
- [삽입 동작: Insert Behavior](#삽입-동작-insert-behavior)

이 페이지는 MongoDB 삽입 작업의 예를 제공한다.

> **Collection 을 생성하세요.**
> collection 이 존재하지 않는 경우 삽입 작업은 컬렉션을 생성한다.

## _단일 Document 삽입: Insert a Single Document_

`db.collection.insertOne()` 은 단일 document 를 collection 에 삽입한다.

다음 예제는 inventory collection 에 새 documnet 를 삽입한다. `_id` 를 지정하지 않으면 Mongo DB 는 Object ID 값이 있는 `_id` field 를 document 에 추가한다.

```javascript
db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
)
```

`insertOne()` 은 새로 삽입된 document 의 _id 필드 값을 포함하는 document 를 반환한다. 반환 예는 [db.collection.insertOne()](https://www.mongodb.com/docs/v4.4/reference/method/db.collection.insertOne/#std-label-insertOne-examples) 를 참조하자.

방금 삽입한 document 를 검색하기 위해 [collection 을 쿼리](https://www.mongodb.com/docs/v4.4/core/document/#std-label-document-query-filter) 하면 다음과 같다.

```javascript
db.inventory.find( { item: "canvas" } )
```

## _복수 Document 삽입: Insert Multiple Document_

_버전 3.2의 새로운 기능._

`db.collection.insertMany()` 는 collection 에 여러 document 를 삽입할 수 있다. document 배열을 메서드에 전달한다.

다음 예는 **inventory** collection 에 세 개 document 를 삽입한다. document 가 `_id` 를 지정하지 않으면 MongoDB 는 각 document 에 ObjectId 값이 있는 `_id` 를 추가한다.

```javascript
db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], size: { h: 14, w: 21, uom: "cm" } },
   { item: "mat", qty: 85, tags: ["gray"], size: { h: 27.9, w: 35.5, uom: "cm" } },
   { item: "mousepad", qty: 25, tags: ["gel", "blue"], size: { h: 19, w: 22.85, uom: "cm" } }
])
```

`insertMany()` 는 새로 삽입된 document(_id 필드 값을 포함하는)를 반환한다. [레퍼런스]() 를 참조하자.

document 를 검색하기 위해 아래와 같이 collection 을 쿼리한다.

```javascript
db.inventory.find( {} )
```

## _삽입 동작: Insert Behavior_
### _컬렉션 생성 (Collection Creation)_

collection 이 존재하지 않는 경우 삽입 작업은 collection 을 만든다.

### _`_id`  (`_id` Field)_

collection 에 저장된 각 document 는 기본 키([primary key](https://www.mongodb.com/docs/v4.4/reference/glossary/#std-term-primary-key)) 역할을 하는 고유 `_id` 가 필요하다. 삽입 document 의 `_id` 를 생략하면,
Mongo DB 드라이버는 `_id` 에 대한 [오브젝트 ID](https://www.mongodb.com/docs/v4.4/reference/bson-types/#std-label-objectid) 를 자동으로 생성한다.

이는 [`upsert: true`](https://www.mongodb.com/docs/v4.4/reference/method/db.collection.update/#std-label-update-upsert) 를 사용한 갱신 작업으로 삽입된 문서에도 적용된다.

### _원자성 (Atomicity)_

Mongo DB 의 모든 쓰기 작업은 단일 document 수준에서 원자적이다. Mongo DB 및 원자성에 대한 자세한 내용은 [원자성 및 트랜잭션](https://www.mongodb.com/docs/v4.4/core/write-operations-atomicity) 을 참조하자.

### _승인 (Write Acknowledgement)_

쓰기 문제가 있는 경우, 쓰기 작업을 위해 Mongo DB 에 요청한 승인 수준을 지정할 수 있다. 자세한 내용은 [Write Concern](https://www.mongodb.com/docs/v4.4/reference/write-concern/) 을 참조하자.

---
[Index 로 이동](https://github.com/jx2lee/getting-started-with-mongodb#index)
