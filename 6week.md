# 6장. 다양한 연관관계 매핑

## 6.1 다대일(N:1)
- **다대일 관계**는 가장 자주 사용되는 매핑 방식.
  - 테이블 간 1:N 관계에서 외래 키는 N쪽에 위치.
  - 예시:
    ```java
    @Entity
    public class Member {
        @ManyToOne
        @JoinColumn(name = "team_id")
        private Team team;
    }
    ```
  - `Member` 엔티티가 `team_id` 외래 키를 통해 `Team`과 연관관계를 맺음.
  
![image](https://github.com/user-attachments/assets/6e840bd5-f7c4-47bb-b046-d5e1d7d3818c)

## 6.2 일대다(1:N)
- **일대다 관계**는 다대일의 반대.
  - 다대일 관계에서 읽기 전용으로 일대다를 매핑할 수 있음.
  - 성능 문제로 인해 실무에서는 거의 사용되지 않음.

![image](https://github.com/user-attachments/assets/4bfe5067-a95d-40e5-b8f5-ce8a79eb44a2)
![image](https://github.com/user-attachments/assets/10d5fbb6-6572-4101-be82-4d0655b28ec4)

## 6.3 일대일(1:1)
- **일대일 관계**는 양쪽 엔티티가 하나의 엔티티만 참조하는 관계.
  - 외래 키를 주 테이블이나 대상 테이블에 둘 수 있음.
  - 주 테이블에 외래 키를 두면 조회 성능이 좋아짐.
  - 예시:
    ```java
    @OneToOne
    @JoinColumn(name = "locker_id")
    private Locker locker;
    ```
  - 이 경우 `locker_id`는 주 테이블인 `Member`가 외래 키를 관리.
    
![image](https://github.com/user-attachments/assets/6250df55-9067-4e4b-a6da-81d9bea29d9a)

## 6.4 다대다(N:N)
- **다대다 관계**는 연결 테이블을 통해 N:N 관계를 1:N, N:1로 풀어냄.
  - JPA에서는 `@ManyToMany`로 다대다 관계를 직접 매핑할 수 있음.
  - 연결 테이블에 추가적인 속성을 넣어야 할 때는 비효율적임.
  - 예시:
    ```java
    @ManyToMany
    @JoinTable(name = "member_product",
               joinColumns = @JoinColumn(name = "member_id"),
               inverseJoinColumns = @JoinColumn(name = "product_id"))
    private List<Product> products = new ArrayList<>();
    ```

## 6.5 다대다 매핑의 한계와 극복
- `@ManyToMany` 매핑은 편리하지만 한계가 있음.
  - 연결 테이블에 추가적인 컬럼이 필요할 때는 **연결 엔티티**를 사용해 다대다 관계를 1:N, N:1로 풀어내는 방식이 권장됨.
