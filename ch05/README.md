HTTP 메서드 활용
==

### 클라이언트에서 서버로 데이터 전송
- 쿼리 파라미터를 통한 데이터 전송
  - GET
- 메시지 바디를 통한 데이터 전송
  - POST, PUT, PATCH
  - 회원 가입, 상품 주문, 리소스 등록, 변경

#### 정적 데이터 조회(이미지, 정적 텍스트 문서)
- 쿼리 파라미터를 사용하지 않을 때
  - 정적 데이터인 경우 일반적으로 리소스 경로로 조회 가능하다.
  - GET 사용

#### 동적 데이터 조회(정렬된 데이터 등)
쿼리 파라미터를 전달해서 정렬이나, 필터 조건을 전송한다. 
  - 주로 필터 조건이 있거나, 조회 결과를 정렬하고자 할 때 사용한다.
  - 조회는 GET 사용하고 데이터를 전달할 때 쿼리 파라미터로 전달한다.

#### HTML Form 데이터 전송
- Form Submit시 POST로 전송한다.
- Content-Type: application/x-www-form-urlencoded 사용한다.
  - form의 내용을 메시지 바디를 통해 전송(key=value)
  - 전송 데이터를 url encoding 처리를 한다.
- GET 전송도 가능하지만, 메시지 바디가 아닌 쿼리 파라미터를 통해 전송한다.
- Content-Type: multipart/form-data
  - 파일 업로드 같은 바이너리 데이터 전송시 사용
  - 다른 종류의 여러 파일과 폼의 내용 함께 전송 가능

#### HTTP API 데이터 전송
- 서버 to 서버 통신에서 사용
  - 백엔드 시스템 통신
- 앱 클라이언트
  - 아이폰, 안드로이드
- 웹 클라이언트
  - Form 전송 대신, AJAX를 사용
- POST, PUT, PATCH: 메세지 바디를 통해 데이터 전송
- GET: 조회, 쿼리 파라미터로 전달
- Content-Type: application/json 을 주로 사용한다.


### HTTP API 설계 예시
- HTTP API - 컬렉션
  - POST 기반 등록
- HTTP API - 스토어
  - PUT 기반 등록
- HTML FORM 사용할 때

#### POST 기반 등록
항상 URI는 리소스를 식별해야 한다는 것을 염두하고 설계한다.

예시
- 회원 목록: /members -> GET
- 회원 등록: /members -> POST 
- 단일 회원 조회: /members/{id} -> GET
- 단일 회원 수정: /members/{id} -> POST, PATCH, PUT
- 회원 삭제: /members/{id} -> DELETE

POST 기반으로 등록할 땐, 클라이언트는 등록될 리소스의 URI를 모른다.

서버는 클라이언트의 데이터를 등록한 후 등록된 리소스 URI를 생성한 후 응답 메시지에 담아 전송한다.
- 해당 정보는 Location header에서 확인할 수 있다.

이렇게 서버가 리소스 디렉터리를 관리하는 방식을 `컬렉션`이라고 한다.
- /members가 컬렉션

#### PUT 기반 등록
- 파일 목록 : /files -> GET
- 파일 조회 : /files/{fileName} -> GET
- 파일 등록 : /files/{fileName} -> PUT
- 파일 삭제 : /files/{fileName} -> DELETE
- 파일 대량 등록 : /files -> POST

PUT 등록을 하기 위해서는 클라이언트가 리소스의 URI를 알고 있어야 한다.
- ex) /files/ss.jpg

즉, 클라이언트가 리소스를 직접 식별해야하는 방식을 `스토어`라고 한다.
- /files 가 스토어

#### HTML FORM 사용
HTML FORM을 사용할 땐, GET, POST만 지원한다.
그렇기 때문에, URI에 행위를 나타내는 컨트롤 URI를 사용한다.
- 컨트롤 URI : HTTP 메서드로 해결하기 애매할 때, 동사로 된 리소스 경로를 사용한다.

예시
- 회원 목록 : /members -> get
- 회원 등록 폼 : /members/new -> get
- 회원 등록 : /members/new -> post
- 회원 조회 : /members/{id} -> get
- 회원 수정 폼 : /members/{id}/edit -> get
- 회원 수정 : /members/{id}/edit -> post
- 회원 삭제 : /members/{id}/delete -> post

#### 참고하면 좋은 URI 개념
- **문서(document)**
  - 단일 개념(파일 하나, 객체 인스턴스, 데이터베이스 row)
  - ex) /members/100 -> 객체 인스턴스, /files/start.jpg -> 
- **컬렉션(collection)**
  - 서버가 관리하는 리소스 디렉터리
  - 서버가 리소스의 URI를 생성하고 관리
  - ex) /members
- **스토어(store)**
  - 클라이언트가 관리하는 자원 저장소
  - 리소스의 URI를 클라이언트가 알고 관리
  - ex) /files
- **컨트롤러(controller), 컨트롤 URI**
  - 컬렉션, 문서, 스토어로 해결이 안되는 경우 사용한다.
  - URI에 직접 동사를 사용한다.
  - /members/{id}/delete
  