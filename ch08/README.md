### 캐시 기본 동작
캐시를 적용시키지 않으면,
- 동일 요청에 대해 항상 똑같은 데이터를 다운로드 받아야한다.

캐시를 적용 시킬 경우
- 캐시가 유효할 경우, 해당 요청에 대한 데이터를 내려받지 않는다.

캐시를 적용했을 때의 장점은 다음과 같다.
- 캐시 가능 시간동안 네트워크를 사용하지 않아도 된다.
- 브라우저 로딩 속도가 매우 빨라진다.

캐시 시간이 초과하면 
- 서버를 통해 데이터를 다시 조회하고, 캐시를 갱신해야한다.
- 네트워크 다운로드가 발생한다.


### 검증 헤더, 조건부 요청 1
캐시 시간이 초과하면 2가지 경우가 발생할 수 있다.
- 서버에서 기존 데이터를 변경했을 경우
- 기존 데이터를 변경하지 않을 경우
   - 클라이언트의 데이터와 서버의 데이터가 같다는 사실을 확인하면 캐시 저장소에 있는 데이터를 사용할 수 있다.


데이터의 수정 시간을 확인하기 위해서 검증 헤더를 사용한다.

1. 서버에서는 데이터가 수정된 날짜가 기입한 `Last-Modified` 헤더를 사용해 데이터를 전송한다.
2. 클라이언트에서 캐시 시간이 만료되고 서버에 요청을 보낼 때, 데이터의 수정 날짜가 기입된 `if-modified-since` 헤더를 포함하여 서버로 전달한다.
3. 만약, 데이터를 수정하지 않았다면 304 Not Modified + 헤더 메타 데이터 정보만 응답한다.

- 검증 헤더 : `Last-modified`
- 조건부 요청 : `if-modified-since`


### 검증 헤더, 조건부 요청 2
- 검증 헤더
  - 캐시 데이터와 서버 데이터가 같은지 검증하는 데이터
  - ex) `Last-Modified`, ETag
- 조건부 요청 헤더
  - 검증 헤더로 조건에 따른 분기
  - `if-Modified-Since`: `Last-Modified` 사용할때 사용.
  - `if-None-Match`: `ETag` 사용할때 사용.

#### Last-Modified, if-Modified-Since 단점
- 1초 미만으로 캐시 조정이 불가능하다.
- 날짜 기반 로직을 사용한다.
- 데이터를 수정해서 날짜가 다르지만, 데이터의 결과가 똑같을 때

#### ETag, If-None-Match
- ETag(Entity Tag)
- 캐시용 데이터에 이름을 달아둔 것
- 데이터가 변경되면, 이름을 바꿔서 변경
- ETag가 같으면 유지하고, 다르면 다시 받는다.


### 캐시와 조건부 요청 헤더
- Cache-Control: 캐시 제어
  - max-age : 초 단위로 입력한다.
  - no-cache : 데이터는 캐시해도 되지만, 항상 origin 서버에 검증하고 사용
  - no-store : 메모리에서 사용하고 저장하면 안된다.
  - public : 응답이 public 캐시에 저장되어도 됨.
  - private : 응답이 해당 사용자를 위한 것이다.
  - s-maxage : 프록시 캐시에만 적용되는 max-age
  - Age : 오리진 서버에서 응답 후 프록시 캐시내 머문 시간
  - must-revalidate : 캐시 만료후 최초 조회시 원 서버에 검증해야한다.
  
### 프록시 캐시
항상 Origin 서버로 요청을 주고받으면 매우 느리다.

그래서 Origin 과 클라이언트 사이에 프록시 캐시 서버를 두고 사용한다.

### 캐시 무효화
- Cache-Control: no-cache, no-store, no-revalidate
- Pragma: no-cache

확실하게 캐시 무효화를 하기 위해서 2가지 헤더를 사용한다.

#### no-cache vs must-revalidate
- no-cache
  - 프록시 캐시 서버에서 처리하지 못해 Origin 서버로 요청을 한다.
  - Origin 서버에 접근할 수 없다면 프록시 캐시 서버에 존재하는 캐시를 반환할 수도 있다.
- must-revalidate
  - 원 서버에 접근할 수 없는 경우, 항상 오류가 발생해야한다.
