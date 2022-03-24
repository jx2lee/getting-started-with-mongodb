# _Introduction to MongoDB_
[https://docs.mongodb.com/v3.4/introduction/](https://docs.mongodb.com/v3.4/introduction/)

MongoDB 는 고성능, 고가용성(HA) 및 자동 확장(auto scaling) 을 제공하는 오픈 소스 document 데이터베이스 이다.

## _Document Database_

MongoDB 의 레코드는 필드 (field) 와 값 (value) 쌍으로 구성된 데이터 구조(document)이다. MongoDB 문서는 JSON 객체와 유사하다. value 에는 다른 document, 배열 (array) 및 document 배열이 포함될 수 있다.

![](https://docs.mongodb.com/v3.4/_images/crud-annotated-document.bakedsvg.svg)

document 의 장점은 다음과 같다.
- 많은 프로그래밍 언어의 기본 데이터 유형
- Embedded document (document in document) 및 배열로 인한 불필요한 join 감소
  - join 작업은 굉장히 비쌈 (expensive)
- 동적 스키마 (Dynamic schema)를 통한 다형성 지원

## _주요 특징 (Key Features)_
### _높은 성능 (High Performance)_

MongoDB 는 고성능 데이터 지속성 (persistence) 을 제공한다.
- **Embedded 데이터 모델**은 데이터베이스의 I/O 를 줄임
  - **Embedded 데이터 모델**: 관계를 갖는 데이터 집합을 하나의 document 로 표현하는 모델로, 엑셀에서 학생정보와 학생이 수강하는 강의정보를 통합하여 1개의 시트로 관리하는 방식과 유사
- 인덱스는 더 빠른 쿼리를 지원하며 포함된 document 및 array 의 키를 포함할 수 있음

### _풍부한 쿼리언어 (Rich Query Language)_

MongoDB 는 읽기 및 쓰기 작업 ([CRUD](https://docs.mongodb.com/v3.4/crud/)) 과 다음을 지원하는 **풍부한 쿼리 언어**를 지원한다.
- 데이터 집계 (aggregation)
- 텍스트 검색 및 지리 공간 쿼리

### _고가용성 (HA)_

replica set 이라고 하는 MongoDB 복제 기능은 다음을 제공한다.
- 자동 장애 조치 (failover)
- 데이터 중복 (data redundancy)
  - 동일한 데이터를 두 위치에 보관하는 데이터베이스 또는 저장 기술 내에서 사용하는 용어
  - [https://www.techopedia.com/definition/18707/data-redundancy](https://www.techopedia.com/definition/18707/data-redundancy)

replica set 은 동일한 데이터를 유지/관리하는 서버 그룹으로 **중복성을 제공하고 데이터 가용성**을 높인다.

### _수평 확장 (Horizontal Scalability)_

MongoDB는 핵심 기능의 일부로 수평 확장 (Horizontal Scalability) 을 제공한다.
- 샤딩 (sharding) 은 클러스터에 데이터를 분산
- 샤드 키 기반 데이터 영역 (zone) 생성
- MongoDB는 읽기 및 쓰기를 특정 zone 의 샤드로만 설정 가능

### _다양한 스토리지 엔진 제공_

- [WiredTiger Storage Engine (including support for Encryption at Rest)](https://docs.mongodb.com/v3.4/core/wiredtiger/)
- [In-Memory Storage Engine](https://docs.mongodb.com/v3.4/core/inmemory/)
- [MMAPv1 Storage Engine](https://docs.mongodb.com/v3.4/core/mmapv1/)

또한, 타사가 MongoDB 용 스토리지 엔진을 개발할 수 있도록 하는 플러그형 스토리지 엔진 API 를 제공한다.

---
[Index 로 이동](https://github.com/jx2lee/getting-started-with-mongodb#index)
