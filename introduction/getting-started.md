# _Getting Started_
[https://docs.mongodb.com/v3.4/tutorial/getting-started](https://docs.mongodb.com/v3.4/tutorial/getting-started)

## _Document 저장_
`db.collection.insertMany()` 는 여러 document 를 collection 에 저장할 수 있다. document 배열을 메서드에 전달한다. 다음은 `inventory` collection 에 document 를 저장한다.

```javascript
db.inventory.insertMany([
   // MongoDB 데이터 삽입 시 _id field 가 없는 경우, _id 를 임의의 값으로 저장한다.
   { item: "journal", qty: 25, status: "A",
       size: { h: 14, w: 21, uom: "cm" }, tags: [ "blank", "red" ] },
   { item: "notebook", qty: 50, status: "A",
       size: { h: 8.5, w: 11, uom: "in" }, tags: [ "red", "blank" ] },
   { item: "paper", qty: 100, status: "D",
       size: { h: 8.5, w: 11, uom: "in" }, tags: [ "red", "blank", "plain" ] },
   { item: "planner", qty: 75, status: "D",
       size: { h: 22.85, w: 30, uom: "cm" }, tags: [ "blank", "red" ] },
   { item: "postcard", qty: 45, status: "A",
       size: { h: 10, w: 15.25, uom: "cm" }, tags: [ "blue" ] }
]);
```

`insertMany()` 는 새로 삽입된 document _id field 값을 포함하는 document 를 반환한다. `db.collection.insertOne()` 을 사용하여 단일 document 를 저장할 수 있다. 자세한 내용과 예는 CRUD 섹션에 Insert Documents 을 참조한다.

## _document 조회 (Query Documents)_
### _모든 document 검색 (Select All Documents)_
collection 의 모든 document 를 찾으려면 빈 document 를 쿼리 필터 document (query filter document) 로 `db.collection.find()` 메서드에 전달한다.

```javascript
db.inventory.find( {} )
```

특정 같음 조건과 일치하는 document 를 조회하려면, 찾기() 메서드에 원하는 document `<field>: <value>` 을 다음과 함께 쿼리 필터 document 를 전달한다. 다음는 inventory 컬렉션에서 상태가 "D"와 같은 모든 document 를 찾는다.

```javascript
db.inventory.find( { status: "D" } )
```

### _Embedded document 찾기 (Match an Embedded Document)_
포함된 전체 document의 일치항목에서는 value field 순서를 포함하여 지정된 document 가 정확히 일치해야한다. 예를들어, 다음 쿼리는 size field 값이 `{ h: 14, w: 21, uom: "cm" }` 인 모든 document 를 찾는다.

```javascript
db.inventory.find( { size: { h: 14, w: 21, uom: "cm" } } )
```

### _Embedded document 의 Field 조건으로 찾기 (Match a Field in an Embedded Document)_
다음은 size 필드에 중첩된 field `uom` 이 `in` 과 동일한 모든 document 를 찾는다.

```javascript
db.inventory.find( { "size.uom": "in" } )
```

### _배열 조건에 맞는 document 찾기 (Match an Element in an Array)_
배열 태그가 해당 요소 중 하나로 `빨간색` 을 포함하는 모든 document 를 찾는다.

```javascript
db.inventory.find( { tags: "red" } )
```

### _배열 조건에 정확히 맞는 document 찾기 (Match an Array Exactly)_
배열 태그 값이 정확히 조건 배열과 동일한 모든 document 를 찾는다. 다음 쿼리는 tags 값이 "red" and "blank" 인 배열을 반환한다.

```javascript
db.inventory.find( { tags: ["red", "blank"] } )
```

자세한 쿼리 예제는 CRUD 섹션의 쿼리 document 를 참조한다. document 를 업데이트하거나 삭제하려면 document 업데이트 및 document 삭제를 참조한다.
