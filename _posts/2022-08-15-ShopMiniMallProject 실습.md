# ShopMiniMallProject 실습

- src
    - main
        - java
            - com.config
                - MySqlSessionFactory.java
                    
                    ```java
                    package com.config;
                    
                    import java.io.IOException;
                    import java.io.InputStream;
                    
                    import org.apache.ibatis.io.Resources;
                    import org.apache.ibatis.session.SqlSession;
                    import org.apache.ibatis.session.SqlSessionFactory;
                    import org.apache.ibatis.session.SqlSessionFactoryBuilder;
                    
                    public class MySqlSessionFactory {
                    
                    	static SqlSessionFactory x = null;
                    	static {
                    		//1. Configuration.xml 읽기
                    		String resource = "com/config/Configuration.xml";
                    		InputStream inputStream =null;
                    		try {
                    			inputStream = Resources.getResourceAsStream(resource);
                    		} catch (IOException e) {
                    			e.printStackTrace();
                    		}
                    		
                    		x = new SqlSessionFactoryBuilder().build(inputStream);
                    		//static이 먼저 실행되고 사라지므로 SqlSessionFactory x를 static으로 만들어준다.
                    		// 프로그램 생성시 실행되는 것이 static, new 생성했을 때 인스턴스 변수
                    		
                    	}//end static{}
                    	
                    	public static SqlSession getSession() {
                    		return x.openSession();
                    	}
                    }//end class
                    ```
                    
                - Configuration.xml
                    
                    ```xml
                    <?xml version="1.0" encoding="UTF-8" ?>
                    <!DOCTYPE configuration
                      PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
                      "http://mybatis.org/dtd/mybatis-3-config.dtd">
                    <configuration>
                    
                    	<!-- jdbc.properties 파일 설정 -->
                    	<properties resource="com/config/jdbc.properties"></properties>
                    	<!-- DTO 별칭 -->
                    	<typeAliases>
                    	 <typeAlias type="com.dto.MemberDTO" alias="Member"/>
                    	</typeAliases>
                    	<environments default="development">
                        	<environment id="development">
                          		<transactionManager type="JDBC"/>
                          		<dataSource type="POOLED">
                            		<property name="driver" value="${jdbc.driver}"/>
                            		<property name="url" value="${jdbc.url}"/>
                            		<property name="username" value="${jdbc.userid}"/>
                            		<property name="password" value="${jdbc.passwd}"/>
                            <!-- property value값 잘 넣자. 
                            ${} 외부 파일의 변수 가져와야 한다.
                            패키지 경로를 .가 아니라 /으로 줘야한다.-->
                          		</dataSource>
                        	</environment>
                      	</environments>
                      <mappers>
                        <mapper resource="com/config/MemberMapper.xml"/>
                      </mappers>
                    </configuration>
                    ```
                    
                - jdbc.properties
                    
                    ```
                    jdbc.driver=oracle.jdbc.driver.OracleDriver
                    jdbc.url=jdbc:oracle:thin:@localhost:1521:xe
                    jdbc.userid=shop
                    jdbc.passwd=shop
                    ```
                    
                - MemberMapper.xml
                    
                    ```xml
                    <?xml version="1.0" encoding="UTF-8" ?>
                    <!DOCTYPE mapper
                      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
                    <mapper namespace="com.mybatis.member.MemberMapper">
                    
                    	<insert id="memberAdd" parameterType="Member">
                    		insert into member(userid, passwd, username, post, addr1,
                    						   addr2, phone1, phone2, phone3, email1, email2)
                    		values (#{userid},#{passwd},#{username},#{post},#{addr1},
                    				#{addr2},#{phone1},#{phone2},#{phone3},#{email1},#{email2})
                    
                    	</insert>
                    	
                    	<select id="memberLogin" parameterType="hashmap" resultType="Member">
                    		select userid, passwd, username, post, addr1,
                    			   addr2, phone1, phone2, phone3, email1, email2
                    		from member
                    		where userid = #{x} and passwd = #{y}
                    	</select>
                    	
                    </mapper>
                    ```
                    
            - com.controller
                - LoginController.java
                    
                    ```java
                    package com.controller;
                    
                    import java.io.IOException;
                    import java.util.HashMap;
                    
                    import javax.servlet.ServletException;
                    import javax.servlet.annotation.WebServlet;
                    import javax.servlet.http.HttpServlet;
                    import javax.servlet.http.HttpServletRequest;
                    import javax.servlet.http.HttpServletResponse;
                    import javax.servlet.http.HttpSession;
                    
                    import com.dto.MemberDTO;
                    import com.service.MemberService;
                    import com.service.MemberServiceImpl;
                    
                    @WebServlet("/login")
                    public class LoginController extends HttpServlet {
                    	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    	
                    		String userid = request.getParameter("userid");
                    		String passwd = request.getParameter("passwd");
                    		
                    		//해시맵으로 구현
                    		HashMap<String, String> map =
                    				new HashMap<String, String>();
                    		map.put("x", userid);
                    		map.put("y", passwd);
                    		
                    		MemberService service = new MemberServiceImpl();
                    		String nextPage="";
                    		
                    		try {
                    			MemberDTO dto = service.memberLogin(map);
                    			if(dto != null) {
                    				nextPage="member/loginSuccess.jsp";
                    				
                    				//세션에 저장
                    				HttpSession session = request.getSession();
                    				session.setAttribute("login", dto);
                    				
                    			}else {
                    				nextPage="member/loginFail.jsp";
                    			}
                    			
                    		} catch (Exception e) {
                    			e.printStackTrace();
                    			nextPage="error/error.jsp";
                    		}
                    		
                    		response.sendRedirect(nextPage);
                    	}
                    
                    	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    		doGet(request, response);
                    	}
                    
                    }
                    ```
                    
                - LoginUIController.java
                    
                    ```java
                    package com.controller;
                    
                    import java.io.IOException;
                    import javax.servlet.ServletException;
                    import javax.servlet.annotation.WebServlet;
                    import javax.servlet.http.HttpServlet;
                    import javax.servlet.http.HttpServletRequest;
                    import javax.servlet.http.HttpServletResponse;
                    
                    @WebServlet("/loginUI")
                    public class LoginUIController extends HttpServlet {
                    	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    	
                    		// main.jsp 위임 - url에 main.jsp 보고싶지 않기 위해서
                    		request.getRequestDispatcher("loginForm.jsp").forward(request, response);
                    	}
                    
                    	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    		doGet(request, response);
                    	}
                    
                    }
                    ```
                    
                - LogoutController.java
                    
                    ```java
                    package com.controller;
                    
                    import java.io.IOException;
                    import java.util.HashMap;
                    
                    import javax.servlet.ServletException;
                    import javax.servlet.annotation.WebServlet;
                    import javax.servlet.http.HttpServlet;
                    import javax.servlet.http.HttpServletRequest;
                    import javax.servlet.http.HttpServletResponse;
                    import javax.servlet.http.HttpSession;
                    
                    import com.dto.MemberDTO;
                    import com.service.MemberService;
                    import com.service.MemberServiceImpl;
                    
                    @WebServlet("/logout")
                    public class LogoutController extends HttpServlet {
                    	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    	
                    		//로그인 이후의 작업들은 모두 세션이 존재여부 확인
                    		HttpSession session = request.getSession();
                    		MemberDTO dto = (MemberDTO)session.getAttribute("login");
                    		
                    		String nextPage = "";
                    		
                    		if(dto != null) {
                    			session.invalidate(); //로그아웃 코드
                    			nextPage = "member/logoutSuccess.jsp";
                    		}else {
                    			//1.로그인 실패
                    			//2.로그인 했는데 timeout 당한 경우
                    			nextPage = "member/logoutFail.jsp";
                    		}
                    		
                    		response.sendRedirect(nextPage);
                    		
                    	}//end doGet
                    
                    	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    		doGet(request, response);
                    	}
                    
                    }
                    ```
                    
                - MainController.java
                    
                    ```java
                    package com.controller;
                    
                    import java.io.IOException;
                    import javax.servlet.ServletException;
                    import javax.servlet.annotation.WebServlet;
                    import javax.servlet.http.HttpServlet;
                    import javax.servlet.http.HttpServletRequest;
                    import javax.servlet.http.HttpServletResponse;
                    
                    @WebServlet("/main")
                    public class MainController extends HttpServlet {
                    	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    	
                    		// main.jsp 위임 - url에 main.jsp 보고싶지 않기 위해서
                    		request.getRequestDispatcher("main.jsp").forward(request, response);
                    	}
                    
                    	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    		doGet(request, response);
                    	}
                    
                    }
                    ```
                    
                - MemberAddController.java
                    
                    ```java
                    package com.controller;
                    
                    import java.io.IOException;
                    import javax.servlet.ServletException;
                    import javax.servlet.annotation.WebServlet;
                    import javax.servlet.http.HttpServlet;
                    import javax.servlet.http.HttpServletRequest;
                    import javax.servlet.http.HttpServletResponse;
                    
                    import com.dto.MemberDTO;
                    import com.service.MemberService;
                    import com.service.MemberServiceImpl;
                    
                    @WebServlet("/memberAdd")
                    public class MemberAddController extends HttpServlet {
                    	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    	
                    		/*
                    			?userid=aaa&
                    			passwd=1234&
                    			passwd2=1234&
                    			username=AAA&
                    			post=05678&
                    			addr1=서울+송파구+송파대로36가길+15-16+%28송파동%2C+성보팰리채%29&
                    			addr2=1&
                    			phone1=010&
                    			phone2=1234&
                    			phone3=5678&
                    			email1=aaaaa&
                    			email2=naver.com
                    		
                    		*/
                    		String userid = request.getParameter("userid");
                    		String passwd = request.getParameter("passwd");
                    		//String passwd2 = request.getParameter("passwd2");
                    		String username = request.getParameter("username");
                    		String post = request.getParameter("post");
                    		String addr1 = request.getParameter("addr1");
                    		String addr2 = request.getParameter("addr2");
                    		String phone1 = request.getParameter("phone1");
                    		String phone2 = request.getParameter("phone2");
                    		String phone3 = request.getParameter("phone3");
                    		String email1 = request.getParameter("email1");
                    		String email2 = request.getParameter("email2");
                    		
                    		MemberDTO dto = new MemberDTO();
                    		dto.setUserid(userid);
                    		dto.setPasswd(passwd);
                    		dto.setUsername(username);
                    		dto.setPost(post);
                    		dto.setAddr1(addr1);
                    		dto.setAddr2(addr2);
                    		dto.setPhone1(phone1);
                    		dto.setPhone2(phone2);
                    		dto.setPhone3(phone3);
                    		dto.setEmail1(email1);
                    		dto.setEmail2(email2);
                    		
                    		MemberService service = new MemberServiceImpl();
                    		String nextPage = "";
                    		try {
                    			//성공
                    			int num = service.memberAdd(dto);
                    			nextPage= "member/memberAddSuccess.jsp";
                    		} catch (Exception e) {
                    			//실패
                    			nextPage= "error/error.jsp";
                    			e.printStackTrace();
                    		}
                    		
                    		response.sendRedirect(nextPage);
                    		
                    	}
                    
                    	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    		
                    		doGet(request, response);
                    	}
                    
                    }
                    ```
                    
                - MemberUIController.java
                    
                    ```java
                    package com.controller;
                    
                    import java.io.IOException;
                    import javax.servlet.ServletException;
                    import javax.servlet.annotation.WebServlet;
                    import javax.servlet.http.HttpServlet;
                    import javax.servlet.http.HttpServletRequest;
                    import javax.servlet.http.HttpServletResponse;
                    
                    @WebServlet("/memberUI")
                    public class MemberUIController extends HttpServlet {
                    	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    	
                    		// main.jsp 위임 - url에 main.jsp 보고싶지 않기 위해서
                    		request.getRequestDispatcher("memberForm.jsp").forward(request, response);
                    	}
                    
                    	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    		doGet(request, response);
                    	}
                    
                    }
                    ```
                    
            - com.dao
                - MemberDAO.java
                    
                    ```java
                    package com.dao;
                    
                    import java.util.HashMap;
                    
                    import org.apache.ibatis.session.SqlSession;
                    
                    import com.dto.MemberDTO;
                    
                    public class MemberDAO {
                    
                    	//회원 저장
                    	public int memberAdd(SqlSession session, MemberDTO dto) {
                    		return session.insert("com.mybatis.member.MemberMapper.memberAdd", dto);
                    	}
                    	
                    	//회원 로그인
                    	public MemberDTO memberLogin(SqlSession session, HashMap<String, String> map) {
                    		return session.selectOne("com.mybatis.member.MemberMapper.memberLogin", map);
                    	}
                    	
                    	//주의: collection does not contain value - 아이디값이 다른 경우
                    }
                    ```
                    
            - com.dto
                - MemberDTO.java
                    
                    ```java
                    package com.dto;
                    
                    public class MemberDTO {
                    
                    	String userid;
                    	String passwd;
                    	String username;
                    	String post;
                    	String addr1;
                    	String addr2;
                    	String phone1;
                    	String phone2;
                    	String phone3;
                    	String email1;
                    	String email2;
                    	
                    	public MemberDTO() {
                    	}
                    
                    	public MemberDTO(String userid, String passwd, String username, String post, String addr1, String addr2,
                    			String phone1, String phone2, String phone3, String email1, String email2) {
                    		this.userid = userid;
                    		this.passwd = passwd;
                    		this.username = username;
                    		this.post = post;
                    		this.addr1 = addr1;
                    		this.addr2 = addr2;
                    		this.phone1 = phone1;
                    		this.phone2 = phone2;
                    		this.phone3 = phone3;
                    		this.email1 = email1;
                    		this.email2 = email2;
                    	}
                    
                    	public String getUserid() {
                    		return userid;
                    	}
                    
                    	public void setUserid(String userid) {
                    		this.userid = userid;
                    	}
                    
                    	public String getPasswd() {
                    		return passwd;
                    	}
                    
                    	public void setPasswd(String passwd) {
                    		this.passwd = passwd;
                    	}
                    
                    	public String getUsername() {
                    		return username;
                    	}
                    
                    	public void setUsername(String username) {
                    		this.username = username;
                    	}
                    
                    	public String getPost() {
                    		return post;
                    	}
                    
                    	public void setPost(String post) {
                    		this.post = post;
                    	}
                    
                    	public String getAddr1() {
                    		return addr1;
                    	}
                    
                    	public void setAddr1(String addr1) {
                    		this.addr1 = addr1;
                    	}
                    
                    	public String getAddr2() {
                    		return addr2;
                    	}
                    
                    	public void setAddr2(String addr2) {
                    		this.addr2 = addr2;
                    	}
                    
                    	public String getPhone1() {
                    		return phone1;
                    	}
                    
                    	public void setPhone1(String phone1) {
                    		this.phone1 = phone1;
                    	}
                    
                    	public String getPhone2() {
                    		return phone2;
                    	}
                    
                    	public void setPhone2(String phone2) {
                    		this.phone2 = phone2;
                    	}
                    
                    	public String getPhone3() {
                    		return phone3;
                    	}
                    
                    	public void setPhone3(String phone3) {
                    		this.phone3 = phone3;
                    	}
                    
                    	public String getEmail1() {
                    		return email1;
                    	}
                    
                    	public void setEmail1(String email1) {
                    		this.email1 = email1;
                    	}
                    
                    	public String getEmail2() {
                    		return email2;
                    	}
                    
                    	public void setEmail2(String email2) {
                    		this.email2 = email2;
                    	}
                    
                    	@Override
                    	public String toString() {
                    		return "MemberDTO [userid=" + userid + ", passwd=" + passwd + ", username=" + username + ", post=" + post
                    				+ ", addr1=" + addr1 + ", addr2=" + addr2 + ", phone1=" + phone1 + ", phone2=" + phone2 + ", phone3="
                    				+ phone3 + ", email1=" + email1 + ", email2=" + email2 + "]";
                    	}
                    	
                    	
                    	
                    }
                    ```
                    
            - com.filter
                - UTFEncodingFilter.java
                    
                    ```java
                    package com.filter;
                    
                    import java.io.IOException;
                    import javax.servlet.Filter;
                    import javax.servlet.FilterChain;
                    import javax.servlet.FilterConfig;
                    import javax.servlet.ServletException;
                    import javax.servlet.ServletRequest;
                    import javax.servlet.ServletResponse;
                    import javax.servlet.annotation.WebFilter;
                    
                    // 모든 요칭시 사용하는 필터
                    public class UTFEncodingFilter implements Filter {
                    
                    	public void destroy() {
                    		System.out.println("UTFEncodingFilter destroy");
                    	}
                    
                    	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                    		System.out.println("UTFEncodingFilter doFilter 전~~~~~~~~~");
                    		
                    		//POST 한글처리
                    		request.setCharacterEncoding("utf-8");
                    		
                    		chain.doFilter(request, response);
                    		System.out.println("UTFEncodingFilter doFilter 후~~~~~~~~~");
                    		//Servlet의 doget과 유사한 역할
                    	}
                    
                    	public void init(FilterConfig fConfig) throws ServletException {
                    		System.out.println("init");
                    	}
                    
                    }
                    ```
                    
            - com.service
                - MemberService.java
                    
                    ```java
                    package com.service;
                    
                    import java.util.HashMap;
                    
                    import com.dto.MemberDTO;
                    
                    public interface MemberService {
                    
                    	public int memberAdd(MemberDTO dto) throws Exception;
                    	
                    	public MemberDTO memberLogin(HashMap<String, String> map)
                    			throws Exception;
                    }
                    ```
                    
                - MemberServiceImpl.java
                    
                    ```java
                    package com.service;
                    
                    import java.util.HashMap;
                    
                    import org.apache.ibatis.session.SqlSession;
                    
                    import com.config.MySqlSessionFactory;
                    import com.dao.MemberDAO;
                    import com.dto.MemberDTO;
                    
                    public class MemberServiceImpl implements MemberService {
                    
                    	/*
                    		Sqlsession session = MySqlSessionFactory.getSession();
                    		try{
                    		
                    		}finally{
                    			session.close();
                    		}
                    	
                    	*/
                    	@Override
                    	public int memberAdd(MemberDTO dto) throws Exception {
                    		int num = 0;
                    		SqlSession session = MySqlSessionFactory.getSession();
                    		
                    		try{
                    			MemberDAO dao = new MemberDAO();
                    			num = dao.memberAdd(session, dto);
                    			session.commit();
                    			
                    		}finally{
                    			session.close();
                    		}
                    		
                    		return num;
                    	}//end memberAdd
                    
                    	@Override
                    	public MemberDTO memberLogin(HashMap<String, String> map) throws Exception {
                    		MemberDTO dto = null;
                    		SqlSession session = MySqlSessionFactory.getSession();
                    		
                    		try{
                    			MemberDAO dao = new MemberDAO();
                    			dto = dao.memberLogin(session, map);
                    		}finally{
                    			session.close();
                    		}
                    		
                    		return dto;
                    	}
                    
                    }//end class
                    ```
                    
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