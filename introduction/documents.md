# _Documents_
[https://www.mongodb.com/docs/v4.4/core/document](https://www.mongodb.com/docs/v4.4/core/document)

- [Document 구조: Document Structure](#document-구조-document-structure)
- [점 표기법: Dot Notation](#점-표기법-dot-notation)
- [Document 제한: Document Limitations](#document-제한-document-limitations)
- [Document 구조의 다른 쓰임: Other Uses of the Document Structure](#document-구조의-다른-쓰임-other-uses-of-the-document-structure)

MongoDB 는 데이터 레코드를 BSON 문서로 저장한다. BSON 은 JSON 문서의 이진 표현이지만 JSON 보다 더 많은 데이터 유형을 포함한다.
BSON 은 [bsonspec.org](https://bsonspec.org/) 를, BSON 유형은 다음 [링크](https://www.mongodb.com/docs/v4.4/reference/bson-types/) 를 참조하자.

> BSON(Binary JSON): JSON 과 유사한 **바이너리 인코딩 직렬화** (_binary-encoded serialization_)
> JSON 과 같이 문서의 embeddings 을 지원하며, JSON 사양의 일부가 아닌 데이터 유형을 허용한다. 예를 들어, 날짜 유형과 BinData 유형이 있다.

![](https://www.mongodb.com/docs/v4.4/images/crud-annotated-document.bakedsvg.svg)

## _Document 구조: Document Structure_
<br>

Document 는 field  및 값 쌍으로 구성되며 다음과 같은 구조를 갖는다.

```javascript
{
   field1: value1,
   field2: value2,
   field3: value3,
   ...
   fieldN: valueN
}
```

field  값은 1) document, 2) 배열 (array) 및 3) document 배열 (array of document) 을 포함한 모든 [BSON 데이터 유형](https://www.mongodb.com/docs/v4.4/reference/bson-types/) 이 될 수 있다.
예를 들어, 다음 document 에는 다양한 유형의 값을 포함한다.

```javascript
var mydoc = {
   _id: ObjectId("5099803df3f4948bd2f98391"),
   name: { first: "Alan", last: "Turing" },
   birth: new Date('Jun 23, 1912'),
   death: new Date('Jun 07, 1954'),
   contribs: [ "Turing machine", "Turing test", "Turingery" ],
   views : NumberLong(1250000)
            }
```

- `_id`: ObjectId 를 갖는다
- `name`: first & last field 를 가지는 document 를 갖는다.
- `birth & death`: 날짜 유형의 값을 갖는다.
- `contribs`: 문자 배열을 갖는다.
- `views`: NumberLong 유형의 값을 갖는다.

### _field  명 (Field Names)_
<br>

field 명은 문자이다. Document field  명에 대한 몇가지 제약사항이 존재한다.

- field  이름 `_id` 는 기본 키로 사용한다. collection 에서 고유해야 하고 변경할 수 없으며, **배열이 아닌 모든 유형**은 _id 로 설정할 수 있다.
- field  이름에는 null 문자가 포함될 수 없다.
- 최상위 field  이름은 달러 기호($) 문자로 시작할 수 없다. MongoDB 3.6부터 점(예: .)과 달러 기호(예: $)가 포함된 field  이름을 허용한다.

> MongoDB 쿼리 언어는 field  이름에 [이러한 문자](https://jira.mongodb.org/browse/SERVER-30575)
> 가 포함된 document 쿼리를 항상 의미 있게 표현할 수는 없다(SERVER-30575 참조). **지원이 완료될 때까지 $ 및 . in field  이름은 권장하지 않는다.**

BSON Document 에는 이름이 같은 field 가 두 개 이상 있을 수 있다. 그러나 대부분의 MongoDB 인터페이스는 중복 field  이름을 지원하지 않는 구조(예: 해시 테이블) 입니다.
이름이 같은 field 가 두 개 이상 있는 document 를 조작해야 하는 경우, 해당 [드라이버 설명서](https://www.mongodb.com/docs/drivers) 를 참조하자.

내부 MongoDB 프로세스에 의해 생성된 일부 document 에는 중복 field 가 있을 수 있지만, 어떤 MongoDB 프로세스도 기존 사용자 document 에 중복 field 를 추가하지 않는다.

### _field  값 제한 (Field Value Limit)_
<br>

fCV (featureCompatibilityVersion) "4.0" 이하로 설정된 MongoDB 버전 혹은 2.6 이전 버전에 해당

> 인덱싱된 collection 의 경우 인덱싱된 field 값에 최대 인덱스 키 길이가 있다.
> 자세한 내용은 [최대 인덱스 키 길이](https://www.mongodb.com/docs/v4.4/reference/limits/#mongodb-limit-Index-Key-Limit) 를 참조하자.

## _점 표기법 (Dot Notation)_
<br>

MongoDB 는 점 표기법 (Dot Notation) 을 사용하여 배열 요소에 접근하고 포함된 document field 에 접근한다.

### _배열 (Array)_
<br>

0부터 시작하는 인덱스 위치로 배열 요소를 지정하거나 접근하려면, 배열 이름을 점(.) 및 0부터 시작하는 인덱스 위치와 연결하고 따옴표로 묶는다.

```
"<array>.<index>"
```

`contribs` 배열의 세 번째 요소를 지정하려면 `contribs.2` 를 사용한다.

```javascript
{
   ...
   contribs: [ "Turing machine", "Turing test", "Turingery" ],
   ...
}
```

배열 query 에 대한 자세한 내용은 하기 링크를 참조한다.
- [Query an Array](https://www.mongodb.com/docs/v4.4/tutorial/query-arrays)
- [Query an Array of Embedded Documents](https://www.mongodb.com/docs/v4.4/tutorial/query-array-of-documents)

> - [`$[]`](https://www.mongodb.com/docs/v4.4/reference/operator/update/positional-all/#mongodb-update-up.---): 업데이트를 위한 모든 위치 연산자 (positional operations)
> - [`$[<identifier>]`](https://www.mongodb.com/docs/v4.4/reference/operator/update/positional-filtered/#mongodb-update-up.---identifier--): 업데이트를 위한 필터링된 위치 연산자
 > - [`$`](https://www.mongodb.com/docs/v4.4/reference/operator/update/positional/#mongodb-update-up.-): 업데이트를 위한 위치 연산자, 배열 인덱스 위치를 알 수 없는 경우 프로젝션 연산자 (projection operator)
> - [Query an Array](https://www.mongodb.com/docs/v4.4/tutorial/query-arrays/#std-label-read-operations-arrays)

### _Embedded Documents_
<br>

점 표기법을 사용하여 embedded document 의 field 를 지정하거나 액세스하려면, embedded document 이름을 점(.) 및 field 이름과 연결하고 따옴표로 묶는다.

```javascript
"<embedded document>.<field>"
```

```javascript
{
   ...
   name: { first: "Alan", last: "Turing" },
   contact: { phone: { type: "cell", number: "111-222-3333" } },
   ...
}
```

- 이름 field 에 last 라는 field 를 지정하려면, 점 표기법을 이용한다. `name.last`
- contact field 의 phone document: phone number 를 지정하려면 점 표기법 `contact.phone.number` 를 사용한다.

embedded document 쿼리에 대한 자세한 내용은 하기 링크를 참조한다.
- [Query on Embedded/Nested Documents](https://www.mongodb.com/docs/v4.4/tutorial/query-embedded-documents/)
- [Query an Array of Embedded Documents](https://www.mongodb.com/docs/v4.4/tutorial/query-array-of-documents/)

## _Document 제한: Document Limitations_
<br>

document 는 다음과 같은 속성을 갖는다.

### _Document 크기 제한 (Document Size Limit)_
<br>

최대 BSON document 크기는 16MB 이다.

최대 document 크기는 단일 document 가 과도한 양의 RAM 을 사용하거나 전송 중 과도한 대역폭을 사용할 수 없도록 한다. 최대 크기보다 큰 문서를 저장하기 위해 MongoDB 는 GridFS API 를 제공한다.
GridFS에 대한 자세한 내용은 [mongofiles](https://docs.mongodb.com/database-tools/mongofiles/?_ga=2.221649631.697258237.1650379181-1731699702.1645669690&_gac=1.252970747.1649164879.CjwKCAjw0a-SBhBkEiwApljU0iqvVwn-VOxGDlNpg-04oD88xKdlhqpXnsKIPrJuGz7tMz6WV2JVAxoCjcUQAvD_BwE#mongodb-binary-bin.mongofiles)
및 [드라이버 설명서](https://www.mongodb.com/docs/drivers/) 를 참조하자.

### _Document Field 순서 (Document Field Order)_
<br>

JavaScript 객체와 달리 BSON document field 는 순서가 지정된다.

### _쿼리의 Field 순서 (Field Order in Queries)_
<br>

- document 비교 시 field 순서가 중요하다. 예를 들어, 쿼리에서 field 와 b가 있는 document 를 비교할 때
  - `{a: 1, b: 1}` 은 `{a: 1, b: 1}`과 같다.
  - `{a: 1, b: 1}` 은 `{b: 1, a: 1}` 과 같지 않다.
- 효율적인 쿼리 실행을 위해 쿼리 엔진은 쿼리 처리 중 field 를 재정렬할 수 있다. 특히 `$project`, `$addFields`, `$set` 및 `$unset` 과 같은 프로젝션 연산자를 처리할 때 field 재정렬이 발생할 수 있다.
  - field 재정렬은 쿼리에서 반환된 최종 결과뿐만 아니라 중간 결과에서도 발생할 수 있다.
  - 일부 작업은 field 를 재정렬할 수 있으므로, 프로젝션 연산자를 사용하는 쿼리에서 반환된 결과 내 특정 field 순서에 의존해서는 안된다.

### _쓰기 작업의 Field 순서 (Field Order in Write Operations)_
<br>

쓰기 작업의 경우 MongoDB 는 **다음 경우를 제외하고 field 의 순서를 유지한다.**
- _id field 는 항상 첫 번째 field 다.
- field 이름 변경을 포함하는 업데이트로 인해 field 를 재정렬할 수 있다.

### _\_id Field (The \_id Field)_
<br>

MongoDB collection 에 저장된 각 document 는 [기본 키](https://www.mongodb.com/docs/v4.4/reference/glossary/#std-term-primary-key) 로 동작하는 고유한 [_id field](https://www.mongodb.com/docs/v4.4/reference/glossary/#std-term-_id) 가 필요하다.
삽입된 document 가 `_id` 를 생략하면, MongoDB 드라이버는 _id 에 [ObjectId](https://www.mongodb.com/docs/v4.4/reference/bson-types/#std-label-objectid) 를 자동으로 생성한다.

이는 [`upsert: true`](https://www.mongodb.com/docs/v4.4/reference/method/db.collection.update/#std-label-upsert-parameter) 를 사용해 업데이트 작업으로 삽입된 document 에도 적용된다. _id 는 다음과 같은 동작 및 제약 조건이 있다.
- 기본적으로 collection 을 생성하는 동안 `_id` 에 고유 인덱스를 생성한다.
- `_id` 는 항상 문서의 첫 번째 field 다. 만약 MongoDB 가 `_id` 없는 document 를 먼저 수신하면, 서버는 `_id` 를 field 시작 부분으로 이동한다.
- `_id` 는 배열, 정규식 또는 정의되지 않은 BSON 데이터를 포함할 수 있다.

> replication 기능을 보장하려면, _id 를 BSON 정규식 유형의 값으로 사용하지 말자.

`_id` 값을 저장하기 위한 옵션은 다음과 같다.
- [ObjectId](https://www.mongodb.com/docs/v4.4/reference/bson-types/#std-label-objectid) 를 이용한다. 
- 사용 가능한 경우 자연 고유 식별자 (natural unique identifier) 를 사용한다. 이렇게 하면 공간을 절약하고 추가 인덱스를 방지한다. 
- 자동 증가수 (auto-incrementing number) 를 이용한다.
- 응용 프로그램 코드에서 UUID 를 생성한다. collection 및 `_id` 인덱스에 UUID 값을 효율적으로 저장하려면, UUID 를 BSON Bin 데이터로 저장한다. Bin 데이터 형식의 인덱스 키는 다음과 같은 경우 보통 인덱스 보다 효율적으로 저장한다:
  - 이진 하위 유형 값은 0-7 또는 128-135 의 범위에 있고,
  - 바이트 배열의 길이가 0, 1, 2, 3, 4, 5, 6, 7, 8, 10, 12, 14, 16, 20, 24, 또는 32 인 경우 효율적이다.
- 사용하고 있는 driver 의 BSON UUID 기능을 사용해 UUID 를 생성한다. 드라이버 구현은 UUID 직렬화 및 역직렬화 과정을 다르게 구현할 수 있으며, 다른 드라이버와 완전히 호환되지 않을 수 있다.
  - UUID 상호 운용성에 대한 정보는 [드라이버 설명서](https://api.mongodb.com/?_ga=2.123903690.1731365139.1650634956-1731699702.1645669690&_gac=1.12676421.1649164879.CjwKCAjw0a-SBhBkEiwApljU0iqvVwn-VOxGDlNpg-04oD88xKdlhqpXnsKIPrJuGz7tMz6WV2JVAxoCjcUQAvD_BwE) 를 참조하자.

> 대부분의 MongoDB 드라이버 클라이언트는 `_id` 를 포함하고 MongoDB 삽입 작업을 보내기 전 ObjectId 를 생성할 것이다. 만약 클라이언트가 _id field 가 없는 document 를 보내면 _id field 를 추가하고 ObjectId를 생성한다.

## _Document 구조의 다른 쓰임: Other Uses of the Document Structure_
<br>

Mongo DB 는 데이터 레코드를 정의하는 것 외에 [쿼리 필터](https://www.mongodb.com/docs/v4.4/core/document/#std-label-document-query-filter),
[특정 Documents 업데이트](https://www.mongodb.com/docs/v4.4/core/document/#std-label-document-update-specification) 및
[특정 Document Index](https://www.mongodb.com/docs/v4.4/core/document/#std-label-document-index-specification) 를 제공한다.

### _Document 쿼리 필터 (Query Filter Documents)_
<br>

쿼리 필터 document 는 읽기, 업데이트 및 삭제 작업을 위한 레코드를 결정하는 조건을 지정한다.

`<field>:<value>` 표현식을 사용하여 같음 조건 및 쿼리 연산자 표현식을 지정할 수 있다.

```javascript
{
  <field1>: <value1>,
  <field2>: { <operator>: <value> },
  ...
}
```
- [Query Documents](https://www.mongodb.com/docs/v4.4/tutorial/query-documents/)
- [Query on Embedded/Nested Documents](https://www.mongodb.com/docs/v4.4/tutorial/query-embedded-documents/)
- [Query an Array](https://www.mongodb.com/docs/v4.4/tutorial/query-arrays/)
- [Query an Array of Embedded Documents](https://www.mongodb.com/docs/v4.4/tutorial/query-array-of-documents/)

### _특정 Documents 업데이트 (Update Specification Documents)_
<br>

특정 document 업데이트는 [업데이트 연산자](https://www.mongodb.com/docs/v4.4/reference/operator/update/#std-label-update-operators) 를 사용하여 `db.collection.update()` 동안 특정 field 의 데이터를 수정한다.

```javascript
{
  <operator1>: { <field1>: <value1>, ... },
  <operator2>: { <field2>: <value2>, ... },
  ...
}
```

자세한 내용은 [Update specification](https://www.mongodb.com/docs/v4.4/tutorial/update-documents/#std-label-update-documents-modifiers) 를 참고하자.

### _특정 Document Index (Index Specification Documents)_
<br>

Index specification document 는 인덱스할 field 와 인덱스 유형을 정의한다.

```javascript
{ <field1>: <type1>, <field2>: <type2>, ...  }
```

---
[Index 로 이동](https://github.com/jx2lee/getting-started-with-mongodb#index)
