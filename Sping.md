# Spring
1. DI(Dependency Injection) :: DAO -DAOImpl의 재사용성을 높이기 위해 사용되는 것이다.
2. MyBatis :: DAO에서 DB로 가는 sql도 프레임워크로 바꿔서 재사용성을 높인다.
3. spring만드는순서 :: DI -> MyBatis -> Spring MVC
4. src폴더를 다각화시킨다. ex)src/main/java, src/main/resources, src/test/java
5. 다양한 meta-data중 xml을 일반적으로 사용하며, instance 생성, 초기화, 객체의 상호관계를 xml에 선언적으로 기술한다.
6. 사용자는 meta-data를 변경, 수정하는 것만으로 code의 수정없이 instance를 사용가능하다.


- framework란?

개발을 수월하게 하기 위해 소프트웨어의 구체적 기능들에 해당하는 부분의 설계와 구현을 재사용 가능하도록 
협업화된 형태로 제공하는 소프트웨어 환경

라이브러리를 사용해서 완벽하게 구현해놓은 것.

- 결합도
1. 결합도가 높은 타이트한 coupling은 실행Test에서 수정한 결과를 ~Test가 즉각적으로 영향을 받을 때를 말한다.
2. 결합도가 높으면 좋지않다 -> 실행테스트를 수정하면 단위테스트도 수정해야하기 때문이다.
3. framework는 coupling을 낮추려하는 기술이다.
4. Spingframework에서 가장 기본적인 기술은 CoreContainer이다....DI...DAO

- POJO(Plain Old Java Object)
1. package/ private field(property)/ constructor/ getter, setter / 필요한 method를 갖는 빈 규약을 준한 일반적인 Bean이다.
2. 어떤것으로부터 상속받지 않고, 구현받지 않은 클래스를 POJO라고 한다.

# DI Container(Core Container, Bean Container)
1. 코드에서 new를 사용하지 않는 방법, 즉 객체생성을 직접 하지않는 방법은 자바클래스(컴포넌트)를 생성하는 컨테이너를 사용하는 것이다. 
2. 그것이바로 DI이다.
3. Bean Factory, XML Bean Factory == DI Container
4. Bean의 종류는 2가지 :: 사용자정의 Bean, API Bean

- DI Container이 하는일.
1. 주문서를 읽어들인다.(~XML Bean Configuration File) --> 개발자 역할
2. Bean을 생성                                        --> 컨테이너 역할
3. Bean을 저장

# ApplicationContext
1. BeanFactory는 객체를 getBean( )하는 순간에 생성한다.
2. 클라이언트가 서비스를 요청할때 Bean을 급하게 생성하고 요청을 처리하는 것과 동일한 결과이다.
3. Lazing Loading :: DB에서 load-on-startup안했을 때랑 같은 상황이다.
4. ApplicationContext은 메타데이타를 읽어올때 미리 인스턴스를 생성해서 읽어온다.
5. PreLoding :: WAS의 경우에서 처럼, 서버 구동할때 DD파일을 읽어서 한번에 서블릿을 다 만들어 놓는다. 
6. BeanFactory보다 ApplicationContext가 더 좋다.
7. BeanFactort는 src를 인식하지 못해서 .src부터 적어줘야하고 ApplicationContext는 src를 기본적으로 장착하고 있어서 src를 넣으면 중복되기 때문에 빼야한다.


# MyBatis Framework
- XML MetaData
1. SqlMapConfig.xml :: MyBatis Framework의 핵심 환경설정 파일(MetaData)
2. mybatis-userservice-mapping.xml : SQL를 갖는 설정파일(MetaData)

- MyBatis Framework의 장점
1. 작고 간단하다.(다른 라이브러리와 의존관계 없다)
2. 기존 애플리케이션/데이터베이스 변경이 불필요하다.
3. 생산성/성능/작업의 분배(소스코드와 SQL의 분리)
4. 관심사의 분리(비즈니스객체만 가지고 작업가능하다)
5. JDBC절차를 간결과한 lib이다.












































