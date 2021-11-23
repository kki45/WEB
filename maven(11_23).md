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


















