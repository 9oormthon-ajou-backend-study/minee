## 2장 JPA 시작 

### 객체 매핑 
- 매핑 어노테이션: @Entity, @Table (name="TABLENAME"), @Id, @Column(name="COLUMNNAME")
  - @Entity: 이 클래스를 테이블과 매핑한다고 JPA에게 알려준다.
  - @Table: 매핑할 테이블 정보를 알려준다 - name 속성을 쓰면 테이블 이름으로 매핑할 수 있다. 이 어노테이션을 생략하면 클래스 이름을 테이블 이름으로 매핑한다.
  - @Id: 엔티티 클래스의 필드를 기본 키로 매핑한다.
  - @Column: 클래스의 필드를 테이블의 컬럼에 매핑한다. name 속성을 사용하면 컬럼의 이름으로 매핑할 수 있다.
  - 매핑 정보가 없는 필드: 필드명을 컬럼명으로 매핑한다.


### persistence.xml 
- JPA는 persistence.xml을 사용해서 필요한 설정 정보를 관리한다.
- 이 파일이 META-INF/persistencce.xmml 경로에 있으면 별도의 설정 없이 JPA가 인식할 수 있다.

### 데이터베이스 방언
- SQL 표준을 지키지 않거나 특정 데이터베이스만의 고유한 기능을 JPA에서는 방언 (Dialect)라 한다.
- persistence.xml 에서 어떤 방언을 사용할 것인지 설정할 수 있다.

### 엔티티 매니저 
``` EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook");```
- 이렇게 하면 이름이 jpabook인 영속성 유닛을 찾아서 엔티티 매니저 팩토리를 생성한다.
- 엔티티 매니저 팩토리는 애플리케이션 전체에서 딱 한번만 생성하고 공유해서 사용해야 한다.

``` EntityManager em = emf.createEntityManager();```
- 엔티티 매니저 팩토리에서 엔티티 매니저를 생성
- 엔티티 매니저는 스레드 간에 공유하거나 재사용하면 안된다.

```
em.close();
emf.close();
```
- 종료: 사용이 끝난 엔티티 매니저/엔티티 매니저 팩토리는 반드시 종료해야 한다.


### JPQL과 SQL
- JPQL은 엔티티 객체를 대상으로 쿼리한다. SQL은 데이터베이스 테이블을 대상으로 쿼리한다.
- JPQL은 대소문자를 명확하게 구분한다.


### 궁금한 것 
- EntityManager는 왜 바로 생성하지 못하고 EntityManagerFactory를 거처야 하는 걸까?

