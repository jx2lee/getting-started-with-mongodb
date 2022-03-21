# _Databases and Collections_
[https://docs.mongodb.com/v3.4/core/databases-and-collections](https://docs.mongodb.com/v3.4/core/databases-and-collections/)

MongoDB 는 **BSON document**, 데이터를 collection 에 저장한다.
![https://docs.mongodb.com/v3.4/_images/crud-annotated-collection.bakedsvg.svg](https://docs.mongodb.com/v3.4/_images/crud-annotated-collection.bakedsvg.svg)

## _Databases_
MongoDB 에서 databases 는 collection 을 가진다. 사용할 database 를 선택하려면 mongo 셸에서 다음과 같이 use 문을 이용한다.

```javascript
use myDB
```

### _Create a Databases_
데이터베이스가 존재하지 않는 경우, **MongoDB 는 해당 데이터베이스에 데이터를 처음 저장할 때 생성**한다. 
존재하지 않는 데이터베이스로 전환하고, mongo 셸에서 다음 작업을 수행하면 데이터베이스를 생성한다.

```javascript
use myNewDB
db.myNewCollection1.insertOne( { x: 1 } )
```

`insertOne()` 은 myNewDB 데이터베이스와 myNewCollection1 collection 이 없는 경우, 둘 다 생성한다.
데이터베이스 & collection 이름이 모두 [MongoDB 네이밍 제한](https://docs.mongodb.com/v3.4/reference/limits/#restrictions-on-db-names) 을 따르는지 확인하자.

## _Collections_

MongoDB는 document 를 collection 에 저장한다.
collection 은 관계형 데이터베이스의 테이블과 유사하다.

### _Collection 생성_

collection 이 존재하지 않는 경우,
MongoDB는 해당 collection 에 대한 데이터를 처음 저장 시 collection 을 생성합니다.

```javascript
db.myNewCollection2.insertOne( { x: 1 } )
db.myNewCollection3.createIndex( { y: 1 } )
```

`insertOne()` 및 `createIndex()` 은 collection 이 아직 없는 경우 해당 collection 을 생성한다.
collection 이름이 [MongoDB Naming Restrictions](https://docs.mongodb.com/v3.4/reference/limits/#restrictions-on-db-names) 를 따라야 한다.

### _명시적 생성 (Explicit Creation)_

`db.createCollection()` 메서드를 제공하여 다양한 옵션으로 collection 을 명시적으로 생성할 수 있다.
옵션을 지정하지 않으면 collection 에 대한 데이터를 처음 저장할 때, 새 collection 을 생성하므로 명시적으로 collection 을 만들 필요가 없다.
이러한 collection  옵션은 [collMod](https://docs.mongodb.com/v3.4/reference/command/collMod/#dbcmd.collMod) 를 참조하자

### _Document 검증 (Document validation): v3.2~_

collection 의 document 에는 동일 스키마가 필요하지 않다.
즉, 단일 collection 의 document 는 동일한 필드 집합을 가질 필요가 없으며 필드의 데이터 유형은 collection  내의 document  간에 다를 수 있다.
MongoDB 3.2 부터 업데이트 및 삽입 작업 중에 collection 에 대한 Document 유효성 검사 규칙을 시행할 수 있습니다.
자세한 내용은 [document  검증](https://docs.mongodb.com/v3.4/core/document-validation/) 을 참조하자

### _Document 구조 변경 (Modifying Document Structure)_

새 field 추가, 기존 field 제거, 값을 새 유형으로 변경 같이 collection 의 document  구조를 변경하려면
**Document 를 새 구조로 업데이트한다.**
