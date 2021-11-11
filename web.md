### 이클립스- IED(통합개발환경) Tool이란?
 개발자가 개발에만 전념할 수 있도록 파워풀한 서비스를 지원함.
 다양한 Powerful한 서비스를 이클립스에서 연결시킴으로써 여러가지 서비스를 자동적으로 받을수 있게끔하는 tool.
 컴파일, 서버로 코드를 배포, 서버를 실행, STOP시키는 등의 일들을 이클립스가 알아서 자동으로 해준다.
 
 1. 소스코드를 작성, 저장 -> 컴파일
 2. Run On Server를 클릭 실행 -- 서버 실행
    1) 서버를 StartUp
    2) 작성한 코드를 Context Path단위로 서버에 배포. 이때 서버는 WebApps라는 최상단 폴더 하위에 배포
    3) 서버에 배포된 파일을 브라우저로 요청
    4) 응답결과를 다시 브라우저로 띄워줌.

- CGI : 서버상에서 실행되는 모든 프로그램 -- ASP, PHP, Servlet(JAVA기반서버)

- src : class path system...package system..소스코드가 저장됨..동적인문서...서블릿...D.D

- WebContent : file path system...html, jsp, css....파일저장...정적인문서...html, jsp, css, 미디어파일..S.D
   
### 정리
1. 클라이언트는 브라우저기반이다.
2. 어떤 값이 들어오는데에 따라서 결과값이 다르게 뱉어내는게 프로그램이다 --> dynamic document(D.D)
3. 어떤 값이 들어오던 결과값이 같게 나오는 것은 static doucment(S.D)이다.
4. 

- form 태그 
	1. 다양한 입력양식 폼을 하나로 묶는 역할을 한다.
	2. 크게 2가지 속성이 있다.
	   필수 속성은 action 으로 연결된 서버상의 페이지(S.D / D.D)를 지정한다.   
	  Optional한 속성으로 method가 있다....값으로 GET/POST방식으로 나뉜다...둘중 하나사용한다
  디폴트는 get방식이다. get방식을 사용하면 서블릿의 doGet()메소드로 돌아간다...get방식은 key,value...
   
 ### WAS(Server)가 하는일
 1. 서버가 start되면 D.D(Dynamic Document)파일을 읽어들인다.
 2.  servlet생성한다 -> 생성자가 호출된다.
 3.  클라이언트가 요청을 하면 요청정보를 바탕으로 request,response,thread객체를 생성한다.
 4.  service()메소드를 호출한뒤 클라이언트에 요청을 처리하기 위해 doGet() or doPost()를 재호출한다...이때 위에서 생성한
       request, response가 인자값으로 passinginto(투입)되어진다.
 5. doGet() or doPost()를 실행시킨다.(동작시킨다)
 6. request, response, thread가 Death된다.
   
   --> 1,2번은 ready-on-state작업이다... 단, 한번만 일어난다.
   
   --> 3,4,5,6번은 반복된다...repeat












 
