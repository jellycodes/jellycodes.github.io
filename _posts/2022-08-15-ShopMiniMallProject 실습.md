# ShopMiniMallProject 실습

- src
    - main
        - java
            - com.config
                - MySqlSessionFactory.java

<script src="https://gist.github.com/jellycodes/6f62d8f629fc420d6d3268bc27f9cc1d.js"></script>
                    
                - Configuration.xml
                    
<script src="https://gist.github.com/jellycodes/7953fef4d082c93f666311684752476f.js"></script>
                    
                - jdbc.properties
                    
                    ```
                    jdbc.driver=oracle.jdbc.driver.OracleDriver
                    jdbc.url=jdbc:oracle:thin:@localhost:1521:xe
                    jdbc.userid=shop
                    jdbc.passwd=shop
                    ```
                    
                - MemberMapper.xml
                    
<script src="https://gist.github.com/jellycodes/7d546c2ce637813b907b7a52a78c09ab.js"></script>
                    
            - com.controller
                - LoginController.java
                    
<script src="https://gist.github.com/jellycodes/fa40dcaeee0533677df9130c5219fe6d.js"></script>
                    
                - LoginUIController.java
                    
<script src="https://gist.github.com/jellycodes/ec396b2ab058682afd11a184d891a87d.js"></script>
                    
                - LogoutController.java
                    
<script src="https://gist.github.com/jellycodes/a9e366c436297947c94e37b614636042.js"></script>
                    
                - MainController.java
                    
<script src="https://gist.github.com/jellycodes/726a03ffe3f351a7034f193adbccde5a.js"></script>
                    
                - MemberAddController.java
                    
<script src="https://gist.github.com/jellycodes/b2e3103088aa3df8298305d97d1c34be.js"></script>
                    
                - MemberUIController.java
                    
<script src="https://gist.github.com/jellycodes/84764d9fb92eaedc690793d81515460c.js"></script>    
                    
            - com.dao
                - MemberDAO.java
                    
<script src="https://gist.github.com/jellycodes/8b51ce96289ed9610f256d0fcad01a03.js"></script>
                    
            - com.dto
                - MemberDTO.java
                    
<script src="https://gist.github.com/jellycodes/0ef4719298daffde3234594ddac667c7.js"></script>
                    
            - com.filter
                - UTFEncodingFilter.java
                    
<script src="https://gist.github.com/jellycodes/2d3d762224a28712ab6f2921e4319822.js"></script>    
                    
            - com.service
                - MemberService.java
                    
<script src="https://gist.github.com/jellycodes/4ee5a6e598301ac672ca32689fdccf4c.js"></script>    
                    
                - MemberServiceImpl.java
                    
<script src="https://gist.github.com/jellycodes/32df6fc7532b587d10421b90a80b60e2.js"></script>    
                    
        - webapp
            - WEB-INF
                - web.xml
                    
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" id="WebApp_ID" version="3.1">
        
        <filter>
        	<filter-name>UTFEncodingFilter</filter-name>
        	<filter-class>com.filter.UTFEncodingFilter</filter-class>	
        </filter>
                    	
        <filter-mapping>
        	<filter-name>UTFEncodingFilter</filter-name>
        	<url-pattern>/*</url-pattern>
        </filter-mapping>
     	
    </web-app>
     ```
                    
            
            ---
            
            - loginForm.jsp
                
```jsx
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!-- webapp 폴더 안의 memberForm.jsp -->
 <!DOCTYPE html>
 <html>
 <head>
 <meta charset="UTF-8">
 <title>Insert title here</title>
 </head>
 <body>
 <h1>로그인 폼 화면입니다.</h1>
 <jsp:include page="common/top.jsp" flush="true" /><br>
 <jsp:include page="common/menu.jsp" flush="true" />
 <hr>
 <jsp:include page="member/loginForm.jsp" flush="true" />
</body>
 </html>
 ```
                
            - main.jsp
                
```jsx
 <%@ page language="java" contentType="text/html; charset=UTF-8"
     pageEncoding="UTF-8"%>
 <!DOCTYPE html>
 <html>
 <head>
 <meta charset="UTF-8">
 <title>Insert title here</title>
 </head>
 <body>
 <h1>Main 화면입니다.</h1>
 <jsp:include page="common/top.jsp" flush="true" /><br>
 <jsp:include page="common/menu.jsp" flush="true" />
 <hr>
 </body>
 </html>
 ```
                
            - memberForm.jsp
                
```jsx
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!-- webapp 폴더 안의 memberForm.jsp -->
   <!DOCTYPE html>
  <html>
  <head>
 <meta charset="UTF-8">
 <title>Insert title here</title>
 </head>
 <body>
 <h1>회원등록 화면입니다.</h1>
 <jsp:include page="common/top.jsp" flush="true" /><br>
 <jsp:include page="common/menu.jsp" flush="true" />
 <hr>
  <jsp:include page="member/memberForm.jsp" flush="true" />
  </body>
 </html>
```