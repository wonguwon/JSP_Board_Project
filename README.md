# JSP 커뮤니티 게시판

Servlet + JSP MVC 패턴으로 구현한 커뮤니티 게시판 학습 프로젝트입니다.  
회원 관리(가입·로그인·정보수정·탈퇴)가 완성되어 있으며, 게시판 CRUD 뷰 골격이 준비되어 있습니다.

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| Frontend | HTML, CSS, JavaScript, JSP, JSTL/EL |
| Backend | Java 17, Servlet (Jakarta EE 6) |
| Server | Apache Tomcat 11 |
| Database | Oracle XE |
| Libraries | Lombok, Bootstrap 5.3.3 |
| Build/IDE | Eclipse |

---

## 프로젝트 구조

```
src/
└── main/
    ├── java/
    │   ├── com/kh/jsp/
    │   │   ├── common/
    │   │   │   └── JDBCTemplate.java          # JDBC 연결 유틸 (static 메서드)
    │   │   ├── controller/
    │   │   │   ├── member/
    │   │   │   │   ├── EnrollFormController.java  # GET /enrollForm.me
    │   │   │   │   ├── InsertController.java      # POST /insert.me (회원가입 처리)
    │   │   │   │   ├── LoginController.java       # POST /login.me
    │   │   │   │   ├── LogoutController.java      # GET /logout.me
    │   │   │   │   ├── MyPageController.java      # GET /myPage.me
    │   │   │   │   ├── UpdateController.java      # POST /update.me
    │   │   │   │   ├── UpdatePwdController.java   # POST /updatePwd.me
    │   │   │   │   └── DeleteController.java      # POST /delete.me
    │   │   │   └── board/
    │   │   │       └── ListController.java        # GET /list.bo (목록 조회)
    │   │   ├── model/
    │   │   │   ├── dao/
    │   │   │   │   └── MemberDao.java             # 회원 DB 접근 (PreparedStatement)
    │   │   │   └── vo/
    │   │   │       └── Member.java                # 회원 VO (Lombok)
    │   │   └── service/
    │   │       └── MemberService.java             # 회원 비즈니스 로직 + 트랜잭션
    │   └── db/
    │       ├── driver/
    │       │   └── driver.properties              # Oracle 연결 정보
    │       └── sql/
    │           └── member-mapper.xml              # SQL 쿼리 (Properties XML)
    └── webapp/
        ├── index.jsp                              # 메인 페이지
        └── views/
            ├── common/
            │   ├── menubar.jsp                    # 공통 헤더 (로그인/네비게이션)
            │   └── error.jsp                      # 에러 페이지
            ├── member/
            │   ├── enrollForm.jsp                 # 회원가입 폼
            │   └── myPage.jsp                     # 마이페이지 (정보수정·비번변경·탈퇴)
            └── board/
                ├── listView.jsp                   # 게시글 목록
                ├── enrollForm.jsp                 # 게시글 작성 폼
                ├── detailView.jsp                 # 게시글 상세보기
                └── updateForm.jsp                 # 게시글 수정 폼
```

---

## 실행 방법

### 1. Oracle DB 설정

```sql
-- 사용자 생성
CREATE USER c##jsp IDENTIFIED BY jsp;
GRANT CONNECT, RESOURCE, CREATE SESSION TO c##jsp;

-- MEMBER 테이블 생성
CREATE TABLE MEMBER (
    MEMBER_NO   NUMBER          PRIMARY KEY,
    MEMBER_ID   VARCHAR2(20)    UNIQUE NOT NULL,
    MEMBER_PWD  VARCHAR2(20)    NOT NULL,
    MEMBER_NAME VARCHAR2(20)    NOT NULL,
    PHONE       VARCHAR2(15),
    EMAIL       VARCHAR2(50),
    ADDRESS     VARCHAR2(100),
    INTEREST    VARCHAR2(200),
    ENROLL_DATE DATE            DEFAULT SYSDATE,
    MODIFY_DATE DATE            DEFAULT SYSDATE,
    STATUS      VARCHAR2(1)     DEFAULT 'Y' CHECK(STATUS IN ('Y','N'))
);

-- 시퀀스 생성
CREATE SEQUENCE SEQ_MNO
    START WITH 1
    INCREMENT BY 1
    NOCACHE;
```

### 2. Eclipse에서 프로젝트 Import

```
File > Import > Existing Projects into Workspace
복제한 폴더 선택 후 Import
```

### 3. JDBC 연결 정보 수정

`src/main/java/db/driver/driver.properties` 파일을 열어 실제 연결 정보로 수정합니다.

```properties
driver=oracle.jdbc.driver.OracleDriver
url=jdbc:oracle:thin:@localhost:1521:xe
username=c##jsp
password=jsp
```

### 4. Tomcat 서버 실행

```
Eclipse > Servers > New Server > Apache Tomcat 11
프로젝트 Add > Start
브라우저: http://localhost:8080/JSP_Board_Project
```

---

## URL 매핑

### 회원 (*.me)

| URL | HTTP | 설명 |
|-----|------|------|
| `/enrollForm.me` | GET | 회원가입 폼 |
| `/insert.me` | POST | 회원가입 처리 |
| `/login.me` | POST | 로그인 |
| `/logout.me` | GET | 로그아웃 |
| `/myPage.me` | GET | 마이페이지 |
| `/update.me` | POST | 회원 정보 수정 |
| `/updatePwd.me` | POST | 비밀번호 변경 |
| `/delete.me` | POST | 회원 탈퇴 (소프트 삭제) |

### 게시판 (*.bo)

| URL | HTTP | 설명 |
|-----|------|------|
| `/list.bo` | GET | 게시글 목록 조회 |
| `/enrollBoardForm.bo` | GET | 게시글 작성 폼 (미구현) |
| `/insertBoard.bo` | POST | 게시글 등록 (미구현) |
| `/detailBoard.bo` | GET | 게시글 상세 조회 (미구현) |
| `/updateBoardForm.bo` | GET | 게시글 수정 폼 (미구현) |
| `/updateBoard.bo` | POST | 게시글 수정 처리 (미구현) |
| `/deleteBoard.bo` | GET | 게시글 삭제 (미구현) |

---

## 구현 현황

| 기능 | 상태 |
|------|------|
| 회원가입 (이메일·전화·관심사) | ✅ 완료 |
| 로그인 / 로그아웃 | ✅ 완료 |
| 마이페이지 (정보수정) | ✅ 완료 |
| 비밀번호 변경 | ✅ 완료 |
| 회원 탈퇴 (소프트 삭제) | ✅ 완료 |
| 게시글 목록 뷰 | ✅ HTML 완료 |
| 게시글 작성 뷰 | ✅ HTML 완료 |
| 게시글 상세 뷰 | ✅ HTML 완료 |
| 게시글 수정 뷰 | ✅ HTML 완료 |
| 게시판 CRUD 백엔드 | ⬜ 미구현 (학습 실습 영역) |

---

## 학습 포인트

### MVC 패턴
```
요청 → Servlet(Controller) → Service(비즈니스 로직) → DAO(DB) → 응답 JSP(View)
```

### Forward vs Redirect
```java
// Forward: URL 유지, request 속성 전달 가능 (실패 시 에러 페이지)
request.getRequestDispatcher("views/common/error.jsp").forward(request, response);

// Redirect: URL 변경, 새로운 요청 (성공 후 페이지 이동)
response.sendRedirect(request.getContextPath());
```

### JDBC 트랜잭션 패턴
```java
Connection conn = JDBCTemplate.getConnection(); // autoCommit=false
int result = dao.insertMember(m, conn);
if (result > 0) {
    JDBCTemplate.commit(conn);
} else {
    JDBCTemplate.rollback(conn);
}
JDBCTemplate.close(conn);
```

### SQL 쿼리 외부화 (member-mapper.xml)
```xml
<!-- SQL을 Java 코드에서 분리하여 XML에 관리 -->
<entry key="insertMember">
    INSERT INTO MEMBER (MEMBER_ID, MEMBER_PWD, ...) VALUES (?, ?, ...)
</entry>
```

### Session 활용
```java
// 로그인 성공
session.setAttribute("loginMember", member);

// JSP에서 EL로 접근
${loginMember.memberName}님 환영합니다.

// 로그아웃
session.removeAttribute("loginMember");
```

### JSTL 조건부 렌더링
```jsp
<c:choose>
    <c:when test="${empty sessionScope.loginMember}">
        <!-- 로그인 전 UI -->
    </c:when>
    <c:otherwise>
        <!-- 로그인 후 UI -->
    </c:otherwise>
</c:choose>
```

---

## 게시판 실습 가이드

게시판 백엔드는 학습 목적으로 미구현 상태입니다.  
아래 순서로 구현해보세요.

1. **BOARD 테이블 설계 및 생성**
   - 게시글번호, 카테고리, 제목, 내용, 작성자ID(FK), 조회수, 작성일, 수정일, 상태

2. **Board VO 작성** (`model/vo/Board.java`)

3. **board-mapper.xml 작성** — SELECT / INSERT / UPDATE / DELETE SQL

4. **BoardDao 작성** (`model/dao/BoardDao.java`) — PreparedStatement 활용

5. **BoardService 작성** (`service/BoardService.java`) — 트랜잭션 관리

6. **Servlet 컨트롤러 작성** (`controller/board/`)
   - `ListController`: DB에서 목록 조회 후 listView.jsp에 전달
   - `InsertBoardController`: 게시글 저장
   - `DetailController`: 단건 조회 + 조회수 증가
   - `UpdateBoardController`: 수정 처리
   - `DeleteBoardController`: 소프트 삭제

7. **listView.jsp 수정** — `<c:forEach>`로 게시글 목록 동적 렌더링

8. **detailView.jsp 수정** — `${board.title}` 등 EL로 데이터 출력
