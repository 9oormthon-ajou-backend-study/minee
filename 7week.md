# 7장. 고급 매핑

## 7.1 상속관계 매핑
- 객체지향의 상속 개념을 데이터베이스에 매핑하는 방법에 대해 설명합니다.
- JPA에서는 `@Inheritance` 어노테이션을 통해 세 가지 전략을 지원합니다.

### 7.1.1 조인 전략 (JOINED)
- 부모와 자식 클래스 각각에 테이블을 생성하고, 조인을 통해 상속 관계를 표현합니다.
- **장점**:
  - 테이블 정규화, 외래키 참조 무결성 제약 활용 가능.
- **단점**:
  - 조회 시 조인을 많이 사용하므로 성능 저하가 발생할 수 있습니다.
  - 데이터 저장 시 두 번의 `INSERT` SQL 호출이 필요합니다.

  ```java
  @Entity
  @Inheritance(strategy = InheritanceType.JOINED)
  public class Item { 
      // 속성 및 메서드
  }
  ```
  ![image](https://github.com/user-attachments/assets/f6834c9e-2b77-4166-bad4-786a7cbd7977)

### 7.1.2 단일 테이블 전략 (SINGLE_TABLE)
- 하나의 테이블에 모든 상속 관계를 저장하며, DTYPE 컬럼으로 자식 엔티티를 구분합니다.
- 장점:
  - 조인이 필요 없어 조회 성능이 빠르고 쿼리가 단순합니다.
- 단점:
  - 자식 엔티티의 매핑된 컬럼은 모두 null을 허용해야 하며, 테이블 크기가 커질 수 있어 성능 저하 가능성이 있습니다.
  ``` java
  @Entity
  @Inheritance(strategy = InheritanceType.SINGLE_TABLE)
  public class Item { 
      // 속성 및 메서드
  }
  ```
### 7.1.3 구현 클래스마다 테이블 전략 (TABLE_PER_CLASS)
- 각 자식 클래스마다 별도의 테이블을 생성하여 상속관계를 매핑합니다.
- 장점: 서브 타입을 명확히 구분하여 처리할 수 있습니다.
- 단점: 여러 자식 테이블을 함께 조회할 때 성능이 느려지며 (UNION 사용 필요),
  - 실무에서 거의 사용되지 않습니다.
```java
  
  @Entity
  @Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
  public class Item { 
      // 속성 및 메서드
  }
```
## 7.2 @MappedSuperclass
- 공통 매핑 정보가 필요할 때 사용하며, 테이블과 직접 매핑되지 않습니다.
- 여러 엔티티에서 공통 속성을 상속해 사용할 수 있습니다.
```java
  @MappedSuperclass
  public abstract class BaseEntity {
      @Id 
      @GeneratedValue
      private Long id;
      private LocalDate createdDate;
      private LocalDate lastModifiedDate;
  }
```

## 7.3 복합 키와 식별 관계 매핑
- 복합 키를 매핑할 때는 @IdClass와 @EmbeddedId를 사용합니다.
@IdClass: 별도의 기본 키 클래스를 정의하여 사용합니다.
@EmbeddedId: 내장된 기본 키 클래스를 사용하는 방식입니다.

```java
  @Entity
  public class Order {
      @EmbeddedId
      private OrderId id;
  }
  
  @Embeddable
  public class OrderId implements Serializable {
      private Long orderId;
      private Long productId;
  }
```
