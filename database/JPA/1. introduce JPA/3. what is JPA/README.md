## JPA 란 무엇인가?

* Java Persistence API 의 약자이며, 자바 진영의 ORM 기술 표준이다.
    * Object-Relation Mapping 은 객체와 관계형 데이터베이스를 매핑한다는 의미.
    * 객체와 테이블을 매핑해서 패러다임의 불일치 문제를 개발자 대신 해결해준다.
    * 예를들어 객체를 데이터베이스에 저장할 때, sql 을 직접 작성하는것이 아닌 자바 컬렉션에 저장하듯 ORM 프레임워크에 저장하면 ORM 프레임 워크가 적절한 insert sql 을 생성하여 데이터베이스에 객체를 저장해준다.
* application 과 JDBC 사이에서 동작한다.
* ORM 프레임워크는 SQL 을 개발자 대신 생성하여 데이터베이스에 전달할 뿐만 아니라 다양한 패러다임의 불일치 문제들도 해결해준다.

## JPA 소개

* 자바 진영은 과거에 Enterprise Java Beans(EJB) 라는 기술 표준에 엔티티 빈이라는 ORM 기술이 포함되어 있는 기술 표준을 만들었었다.
    * 너무 복잡하고 기술 성숙도가 떨어졌으며, J2EE 애플리케이션 서버에서만 동작했다.
* 하이버네이트라는 오픈소스 ORM 프레임워크가 등장하고, 이는 EJB 보다 가볍고 기술 성숙도도 높았으며 J2EE 없이도 동작했다.
    * 자연스럽게 EJB 3.0 에서 하이버네이트를 기반으로 새로운 자바 ORM 기술 표준이 만들어졌는데, 이것이 JPA 이다.
* JPA 는 자바 ORM 기술에 대한 APi 표준 명세이다.
    * 일반적이고 공통적인 기능의 모음이다.
    * JPA 를 사용하려면 JPA 를 구현한 ORM 프레임워크를 선택해야 한다.
    * JPA 를 구현한 ORM 프레임 워크로는 Hibernate, EclipseLink, DataNucleus 가 있는데 하이버네이트가 가장 대중적이다.
* 표준 명세이기 때문에 특정 구현 기술에 대한 의존도를 줄일 수 있으며, 다른 구현 기술로 손쉽게 이동할 수 있는 장점들이 있다.

## JPA 를 왜 사용하는가?

1. 생산성
    * 자바 컬렉션에 객체를 저장하듯 JPA 에게 저장할 객체를 전달하면, 해당 행위에 대한 sql 작성과 JDBC API 를 사용하는 반복적인 작업을 JPA 가 대신해준다.
    * 즉, 반복적인 코드와 CRUD 용 sql, JDBC API 를 개발자가 직접 작성하지 않아도 된다.
    * 또한 테이블 생성등과 같은 DDL 문을 자동으로 생성하는 기능도 존재한다.
    * 이런 기능들을 통해 데이터베이스 설계 중심의 패러다임을 객체 설계 중심으로 역전시킬 수 있다.
2. 유지보수
    * sql 을 직접 다루면 엔티티에 필드가 추가되면 해당 엔티티의 sql 쿼리와 JDBC API 를 모두 변경해줘야 한다.
    * jpa 는 이러한 행위를 대신 알아서 해주기 때문에 유지보수해야 하는 코드가 줄어들게 된다.
3. 패러다임의 불일치
    * jpa 는 상속, 연관관계, 객체 그래프 탐색, 비교 등과 같은 여러 패러다임의 불일치 문제를 해결해 주는 것들을 지금까지의 예를통해 알아봤다.
4. 성능
    * jpa 는 애플리케이션과 데이터베이스 사이에서 다양한 성능 최적화 기회를 제공한다.
    * jpa 는 애플리케이션 - 데이터베이스 사이에서 동작하고 다음과 같은 최적화를 시도할 수 있다.
        * 같은 트랜잭션 안에서 같은 데이터를 조회하는 경우, JDBC API 를 사용하면 같은 select sql 을 2번 데이터베이스에 전달한다.
        * jpa 의 경우에는 1번 데이터베이스에 전달한 후 트랜잭션 안에 같은 데이터가 존재한다면 해당 데이터를 재활용 한다.
5. 데이터 접근 추상화와 벤더 독립성
    * rdb 에서 같은 기능도 벤더마다 사용법이 다른 경우가 많다.
    * 예를들어 페이징 처리의 경우 데이터베이스 기술마다 다르고, 다른 데이터베이스로 변경을 한다면 이러한 다른 사용법에 맞춰서 모두 변경해줘야 한다.
    * 하지만, jpa 의 경우 애플리케이션과 데이터베이스 사이에 추상화된 데이터 접근 계층을 제공해서 애플리케이션이 특정 데이터베이스 기술에 종속되지 않도록 만들어 준다.
    * 즉, 다른 데이터베이스로 변경한다면 jpa 에 다른 데이터베이스를 사용한다고만 알려주면 알아서 해당 데이터베이스의 기술에 맞게 변경이 된다.
6. 표준
    * jpa 는 자바 진영의 ORM 기술 표준이다.
    * 기술 표준이기에 다른 구현 기술로 손쉽게 변경이 가능하다.
