# SqlSession를 DI한다.
![image](https://user-images.githubusercontent.com/62687865/143071549-556b4df0-28b0-4389-ab9e-87fc8fd9cf38.png)

메타데이타 :: 정보를 지니고 있는 데이타(properties, xml) </br>
Mybatis한다음 단위테스트를 끝내고 persistence layer로 넘어가야한다.</br>
1. persistence layer :: raw한 data(데이터에 직접 넘어가는 데이터, 가공되지 않은 데이터)
2. service layer :: data 가공한다. </br>

# jdbc환경설정
</br>
jdbc환경설정은 빈 설정문서에서 한다.</br>
메커니즘을 이해하기 위해 전에서는 sqlmapconfig.xml에서 environment태그를 이용해서 환경구축을 해주었다.</br>
DI연결하기 전의 단위테스트용이였다.</br>
원래는 주문서에서 해준다.</br>

```xml
변경전) SqlMapConfig.xml

<!-- 3. jdbc 환경구축 -->
	<!-- 단위테스트용..MyBatisFramework를 위한...DI연결하기전의.. -->
	<environments default="mulcam">
		<environment id="mulcam">
			<transactionManager type="JDBC" />
			<dataSource type="UNPOOLED">
				<property name="driver" value="${jdbc.mysql.driver}"/>
				<property name="url" value="${jdbc.mysql.url}"/>
				<property name="username" value="${jdbc.mysql.username}"/>
				<property name="password" value="${jdbc.mysql.password}"/>
			</dataSource>
		</environment>
	</environments>

변경후) userservice13.xml

<context:property-placeholder location="classpath:config/dbconn.properties"/>
```
jdbc환경구축을 주문서에서 하게되면 SqlMapConfig.xml을 간단하게 수정할수 있다.
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

	<!-- MyBatisFramework와 DI연결을 하는 설정문서...용도 -->

	<settings>
		<setting name="mapUnderscoreToCamelCase" value="true"/>
	</settings>
	
	<!-- 2. vo를 alias.... -->
	<typeAliases>
		<package name="ibatis.services.domain"/>
	</typeAliases>
	
	<!-- 3. sql mapper -->
	<mappers>	
		<mapper resource="sql/mybatis-userservice-mapping10.xml" />
	</mappers>
</configuration>
```
# Annotaion기반의 ~test
어노테이션 :: 빈설정문서를 줄이기 위해서 사용한다.</br>
mybatis-spring-1.2.2.jar에는 라이브러리에 추가하면 close가 이미들어있기 때문에 sqlSession.commit()을 하면 안된다.</br>
commit()자체가 이미 close가 들어있기때문에 사용하면 에러가 생긴다.</br>
</br>
@메이븐 :: 코드에 맞는 라이브러리를 찾아 넣는게 힘든데 데이터소스가 추가될때 마다 라이브러리를 추가해야한다.</br>
그럴때 어떤 라이브러리를 넣어야하는지 찾기가 힘들기 때문에 그러한 문제를 쉽게하기위해서 사용하는 것이 메이븐이다.</br>
```java
컴포넌트) MyBatisUserDAOImpl13.java

import java.util.List;

@Repository
public class MyBatisUserDAOImpl13 implements UserDAO{
	
	@Autowired
	private SqlSession sqlSession;
	

컴포넌트) MyBatisUserServiceImpl13.java


@Service
public class MyBatisUserServiceImpl13 implements UserService {
	
	@Autowired
	private UserDAO userDAO;
```
```xml
<?xml version="1.0" encoding="UTF-8"?>

<!-- MyBatis와 DI 연결 -->
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">
	
	<context:property-placeholder location="classpath:config/dbconn.properties"/>
	
	<!-- 1. DataSource API Bean -->
	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
		<property name="driverClassName" value="${jdbc.mysql.driver}" />
		<property name="url" value="${jdbc.mysql.url}" />
		<property name="username" value="${jdbc.mysql.username}" />
		<property name="password" value="${jdbc.mysql.password}" />
	</bean>
	
	<!-- 2. MyBatis API Bean :: SqlSessionFactoryBean -->
	<bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="configLocation" value="classpath:config/SqlMapConfig.xml" />
		<property name="dataSource" ref="dataSource" />
	</bean>
	
	<!-- 3. MyBatis API Bean :: SqlSession -->
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<!-- 생성자 주입 -->
		<constructor-arg ref="sqlSessionFactoryBean" />
	</bean>
	
	<!-- 4. DAOImpl12 사용자정의 Bean Annotation -->	
	<!-- 5. ServiceImpl12 사용자정의 Bean Annotation -->

	<context:component-scan base-package="ibatis.services.user.impl" />

</beans>
```
![image](https://user-images.githubusercontent.com/62687865/143077114-0ccc7519-caf7-4ec7-aa6b-40b6ca45d930.png)

설명</br>
@Autowired을쓰면 sqlSession이 MyBatisUserDAOImpl13에 자동적으로 wiring된다.(hasing된다)</br>
세터를 날릴수 있다.</br>
<context:component-scan base-package="ibatis.services.user.impl" /></br>
어노테이션을 한 대신에 이태그를 반드시 달아줘야한다.</br>
주문서에달아줘야한다.(빈설정문서 bean configuration file)</br>
</br>
MyBatisUserDAOImpl13는 어떤 layer의 컴포넌트일까</br>
persistence layer component이다.</br>
컴포넌트를 구분하기위해서 마킹한다.</br>
@component -> @repository라고 마킹한다.</br>
@service -> @service라고 마킹한다.
</br>
mybatis는 layer라고 하지않는다.</br>
서버에는 layer가 세개가 있다.</br>
persistence layer, service layer,presentation layer </br>
persistence layer :: DAO -DAOImpl 인터페이스기반의 재사용가능한 자바클래스 == 컴포넌트</br>
service layer :: Service -ServiceImpl 인터페이스기반의 재사용가능한 자바클래스 == 컴포넌트</br>
presentation layer :: controller기반의 factory 위와같음</br>
</br>
layer에 따른 컴포넌트를 구분하는 이름.</br>
다컴포넌트임.</br>
persistence -> repository</br>
service -> service</br>
presentation -> controller

# Maven(메이븐)
1. 패키지계층구조가 3단계이상되어야한다.</br>
3개미만으로 잡으면 finish버튼이 활성화가 되지않는다.</br>
ex) com.edu.spring</br>
com.edu --> groupid</br>
spring --> artifact id 실제이름</br>
</br>
2. src의 다각화</br>
java code</br>
설정문서</br>
unittest code</br>
</br>
프레임워크 :: 라이브러리의모음</br>
라이브러리가 모듈화되어있음.</br>
라이브러리는 결론적으로 .jar파일 하나이다.</br>
.jar하나가 dependency하나이다.</br>
</br>
3. 라이브러리를 일일이 추가하지않아도된다 -> pom.xml</br>
처음프로젝트를 생성할떄 시간이 오래걸린다.</br>
pom.xml에 dependency태그로 각각의 라이브러리를 원격으로 가져오기 때문이다.</br>
C:\Users\egmin\.m2\repository\junit\junit\4.7에 라이브러리 생성된다.</br>
대량의 데이터를 로딩할떄 손실되는 데이터가 있을수있다.</br>
라이브러리중 몇개가 손실될수있기때문에 그럴떄는 repository파일(로컬)을 삭제한 후 
다시 프로젝트를 로딩해주면 해결된다.</br>
그뒤 다른 프로젝트를 생성할때는 오래걸리지않는다.</br>
그이유는 .m2에서 가져오기때문이다.</br>
</br>
4. sts기능 </br>
sts spring configuration file을 직접 생성시킬수있다. 
</br>

# [정리]</br>
presentation layer</br>
클라이언트의 모든 요청은 하나의서블릿이 받음.</br>
요청을 분배하기때문에 - dispatcher서블릿이라고 부름.</br>
받은 요청값을 controllerfactory에게 줬다. </br>
controllerfactory이름이 handlermapping이다.</br>
하는일은 컨트롤러를 생성하는 일이다.</br>
contorller인터페이스의기반의 컴포넌트 생성한다. indexcontroller</br>
indexcontroller는 handlermapping에서 만든다.</br>
만들어서 다시 dispatcher에게 준다. 만든애의 부모를 준다.</br>
controller를 리턴받는다. 그뒤 디스패처가 controller를 요청한다. handlerequest</br>
자식의 오버라이딩된메소드 indexcontroller가 돌아간다. </br>
그후 model and view를 리턴한다.</br>
컴포넌트의 결과페이지우리가 만듬. forward방식으로 리턴받음</br>
</br>

# web.xml
was가 가동되고 하는일(순서)</br>
1. <param-value>/WEB-INF/spring/root-context.xml</param-value></br>
이 경로의 파일을 찾음.</br>
2. org.springframework.web.servlet.DispatcherServlet</br>
라이브러리 클래스를 appServlet이름으로 생성한다.</br>
3. /WEB-INF/spring/appServlet/servlet-context.xml 파일을 찾음.</br>
4. servlet-context.xml 가 빈설정문서이다.</br>
<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"></br>
	<beans:property name="prefix" value="/WEB-INF/views/" /></br>
	<beans:property name="suffix" value=".jsp" /></br>
</beans:bean></br>
결과페이지의 위치와 확장자를 등록하는 일을 한다.</br>
</br>
사용자정의빈을 등록한다.</br>
<!-- 사용자정의 빈 등록 : IndexController --></br>
<beans:bean name="/" class="com.edu.spring.controller.IndexController" /></br>
</br>
정리</br>
1. d.d파일을 읽는다.(web.xml)</br>
2. root-context.xml를 찾음.</br>
3. 디스패처 서블릿을 생성</br>
4. servlet-context.xml를 찾는다.</br>
</br>
WAS -> web.xml</br>
1)서블릿 생성</br>
2) bean 설정문서 wiring</br>
dispatcher-servlet.xml
































