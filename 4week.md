# 4장: 엔티티 매핑

## 1. 엔티티 매핑 개요
- **@Entity**: JPA에서 클래스가 엔티티임을 선언.
  - 기본 생성자가 있어야 한다, final, enum, interface, inner 클래스에는 사용할 수 없다.
- **@Table**: 엔티티가 매핑될 테이블 정보를 지정.

## 2. 기본 키 매핑
- **@Id**: 기본 키를 설정.
- 애플리케이션에서 직접 할당하거나 대리키를 사용해 자동 생성할 수 있다.
- **기본 키 생성 전략** (자동 생성):
  - `IDENTITY`: 데이터베이스에서 자동 증가되는 값 사용.
  - `SEQUENCE`: 데이터베이스 시퀀스를 사용.
  - `TABLE`: 키 생성 전용 테이블을 사용.
  - `AUTO`: JPA가 자동으로 전략을 선택.

## 3. 필드와 컬럼 매핑
- **@Column**: 필드를 데이터베이스 컬럼과 매핑. 컬럼 이름, 길이, 타입 등을 설정 가능.
- **@Enumerated**: 열거형 타입을 매핑. `ORDINAL`(숫자) 또는 `STRING`(문자열)으로 저장.
- **@Temporal**: 날짜 타입 매핑. `DATE`, `TIME`, `TIMESTAMP`로 구분.
- **@Lob**: 큰 데이터 매핑. `BLOB` 또는 `CLOB` 타입으로 저장.
  - LOB: Large OBject
  - 데이터베이스에서 사용하는 자료형의 일종, 무려 4기가나 저장할 수 있다....
  - 지정할 수 있는 속성은 없다. 자동으로 String, char[], java.sql.CLOB 형식은 CLOB, 나머지는 BLOB으로 매핑한다.
  - DB에서는 Long보다 Lob의 사용을 권장한다. Long과 달리 Lob은 Object 타입을 지원한다.
  - Lob은 Lob만을 위한 세그먼트가 따로 있다. 칼럼에 데이터를 직접 넣지는 않고 칼럼에는 세그먼트의 location을 저장한다.
  - [자세한 설명...](https://www.ibm.com/docs/ko/db2/11.5?topic=list-large-objects-lobs)
- **@Transient**: 특정 필드를 매핑에서 제외. 임시값을 저장하거나... 할 때 쓴다.
- **@Access**: 접근 권한을 설정하는 notation
  - 필드 접근 (AccessType.FIELD)과 프로퍼티 접근 (AccessType.PROPERTY)을 지정할 수 있다.
  - 필드 접근: 필드에 직접 접근한다. 필드 접근 권한이 private이어도 접근할 수 있다.
  - 프로퍼티 접근: 접근자 (Getter)를 사용해서 접근한다.
  - @Access를 설정하지 않으면 @Id의 위치를 기준으로 접근 방식이 설정된다.
  - (Q) 필드 접근이 뭔데... [공부할 것](https://thorben-janssen.com/access-strategies-in-jpa-and-hibernate/#5_reasons_why_you_should_use_field-based_access)

## 4. 데이터베이스 스키마 자동 생성
- JPA는 애플리케이션 시작 시 엔티티 매핑 정보를 바탕으로 스키마를 자동으로 생성 가능.
- **운영 환경에서는 사용 지양**: 스키마 자동 생성 기능은 개발 환경에서만 사용 권장.
