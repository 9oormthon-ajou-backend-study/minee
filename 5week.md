## 5. 연관관계 매핑 기초 

### 객체 연관관계 vs 테이블 연관관계 

객체 | | 테이블 |
:--:|:--:|:--:|
단방향 | **방향성** | 양방향|
객체 간 참조관계 설정 (주소) | **연관관계 형성 방법** | 외래키 (테이블 간 조인으로 조회) | 

객체 간의 양방향 연관관계를 형성하려면 서로가 서로를 참조하도록 해야 한다.
```java
class B {
  A a; 
}
class A {
  B b; 
}
```
이렇게 하면 A로 B를 참조할 수 있고 B로 A를 참조할 수 있는, 양방향인 것 같은 연관 관계를 형성할 수 있다.
(사실 단방향 두 개 A -> B, B -> A임) 

---
### JPA로 객체와 테이블 매핑 
- 어노테이션 사용
  - 다중성 표현
    - @ManyToOne 다대일 (N:1)
    - @OneToMany 일대다 (1:N)
    - @ManyToMany 다대다 (M:N)
    - @OneToOne 일대일 (1:1)
  - 외래 키 매핑
    - @JoinColumn(name = "columnname")
    - @JoinColumn은 생략할 수도 있는데, 생략하면 기본 전략을 써서 외래 키를 찾는다.
    - 필드명 + _ + 참조하는 테이블의 컬럼명

### 단방향 연관관계 매핑 
#### 엔티티 저장 
``` java
// 멤버 객체에 팀 참조를 저장하는 예제
Team team1 = new Team();
em.persist(team1);

Member member1 = new Member();
member.setTeam(team1);
em.persist(member1);
```
이렇게 하면 team을 저장하는 테이블과 조인할 때 사용하는 외래키 필드에 team1을 Insert하는 쿼리가 실행된다.
> ``` em.persist() ``` 가 필요한 이유: 엔티티를 저장할 때 모든 엔티티는 영속 상태여야 한다.

#### 엔티티 조회
- 객체 그래프 탐색
  - member.getTeam() 과 같이 객체의 참조 관계를 이용해 조회한다.
- JPQL 사용
  - jpql로 테이블을 조인해 조회할 수 있다.
    ``` String jpql = "select m from Member m join m.team t where" + "t.name=:teamName";``` 
    → ```SQL SELECT M.* FROM MEMBER MEMBER INNER JOIN TEAM TEAM ON MEMBER.TEAM_ID = TEAM1_.ID WHERE TEAM1_.NAME='TEAM1'```
  - 콜론 (:) 으로 시작하는 부분은 파라미터를 바인딩받는 문법이다.
  - m.team은 회원과 팀이 관계를 가지고 있는 필드, alias가 t
  - t의 name 필드를 검색 조건으로 해서 teamName과 일치하는 회원만 검색한다.
  - (Q) JPQL 문법... teamName은 객체의 필드인가 테이블의 칼럼인가...  

#### 엔티티 연관관계 수정 / 삭제 
- 엔티티의 참조관계를 수정하거나 null로 설정한 뒤 persist 하면 된다. 알아서 변경사항을 감지하고 뚝딱 고치는 쿼리를 날려 준다.
```java
// 수정
member1.setTeam(team2);
em.persist(member1);

// 삭제
member1.setTeam(null);
em.persist(member1);
```
#### 엔티티 삭제 
- 연관된 엔티티를 삭제하려면 연관관계를 미리 제거한 뒤에 삭제해야 한다.
- 그렇지 않으면 데이터베이스에서 오류가 생긴다.

### 양방향 연관관계 매핑 
- 객체끼리의 참조 관계를 단방향일 때와 달라진다.
- 테이블끼리는 원래 양방향 연관관계이므로 뭐가 달라지지는 않는다.
- 일대다 - 다대일과 같이 매핑할 때는 collections를 사용할 수 있다. (List, Map, ... 여러 가지를 지원함)

### 연관관계의 주인
엔티티끼리의 관계는 양방향이라고 해도 단방향 두개로 구현한다. 반면에 테이블은 양방향이면 양방향 관계 하나를 뜻한다. 그래서 둘 간에 차이가 생긴다. 
엔티티들이 양방향 관계를 갖도록 하면 엔티티에서는 연관관계를 관리하는 포인트가 (참조를 가지고 있는 엔티티가) 두 개가 된다. 테이블은 여전히 외래키 하나만 가지고 있다.
그래서 외래키와 객체끼리의 참조관계를 정확히 매핑할 수 없다. → 이걸 개선하기 위해서 두 엔티티 중 한 쪽이 외래키를 관리하도록 하고, 관리하는 쪽의 엔티티가 연관관계의 주인이 된다. 

- 양방향 연관관계 매핑 시 연관관계의 주인을 꼭 정해야 한다. 연관관계의 주인만이 외래키를 관리 (등록/수정/삭제) 할 수 있다.
- 주인이 아닌 쪽은 외래키로 상대방을 읽기만 할 수 있다.
- 어떤 연관관계를 주인으로 설정할지는 mappedBy 속성으로 결정할 수 있다.
  - 주인이 아닌 쪽에 mappedBy 속성을 사용한다.
  - 연관관계의 주인 - 외래 키 관리자를 설정하는 것이다.
  - 주인이 아닌 쪽은 inverse (반대편), non-owning side라고 부른다.

- **연관관계의 주인은 외래 키가 있는 곳으로 고르자 **
  - 예제에서는 member가 team과의 연관관계를 가지고 있다. member가 주인이 되어야 한다.
  - 이렇게 하면 member는 외래키를 변경하거나 수정하거나 삭제할 수 있지만 team은 조회밖에 하지 못한다.
  - 데이터베이스의 다대일-일대다 관계에서는 항상 '다' 쪽이 외래 키를 가진다.
    - @ManyToOne은 항상 연관관계의 주인이 되므로 (N:1, 이게 붙는 쪽이 N) mappedBy 속성을 사용하지 않는다 (존재하지 않는다).

- **양방향 연관관계를 설정할 때 주의할 점 **
  - 외래키의 수정 (값을 변경함, 값을 저장함, ...) 은 연관관계의 주인인 쪽만 가능하다.
  - 엔티티끼리의 관계를 설정할 때에도 주인인 쪽이 다른 쪽을 참조하도록 설정해야 한다.
  - member.setTeam()을 하지 않으면, team.setMember()를 아무리 해도 테이블의 연관 관계는 설정되지 않는다.
- 하지만...
  - 사실 객체 관점에서는 양쪽 방향 모두에 참조 관계를 설정해 두는 게 안전하다.
  - JPA를 쓰지 않는 순수한 객체 상태에서 문제가 발생할 수 있기 때문에...
  - 하지만 이건 너무 귀찮고 번거롭고 어쩌고 저쩌고 문제가 생기기 쉽다 → 연관관계 편의 메소드를 만들어서 쓰자 

#### 연관관계 편의 메소드 
```java
team.setMember();
team.getMember().setTeam();
```
이렇게 두 문장을 매번 수행하면 너무 귀찮고... 실수할 확률도 높다. 
하나의 메소드처럼 동작하도록 합치면 좀 더 효율적으로 코딩할 수 있다. 
```java
public class Member {
  private Team team;
  public void setTeam(Team team){
  
    if (this.team != null)
      this.team.getMembers().remove(this);
      // 만약 수정하는 코드라면: team → member 연관관계를 없애 둬야 한다. 
    this.team = team;
    team.getMembers().add(this);
  }
}

public void test_양방향(){
  Team team1 = new Team();
  em.persist(team1);

  Member member1 = new Member();
  member1.setTeam(team1);
  em.persist(member1);
}
```
이렇게 하면 member.setTeam() 하나로 양방향 연관관계를 객체에서도 안전하게 설정할 수 있다. 

