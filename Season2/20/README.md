# 20장 리버스 프록시를 이용해 컨테이너 HTTP 트래픽 제어하기

## 1. 리버스 프록시란?

- 웹 어플리케이션을 통하는 관문 역할
- 외부에서 들어오는 모든 트래픽은 리버스 프록시를 거치므로 애플리케이션 컨테이너는 포트를 외부에 공개하지 않아도 된다.
- 도메인과 IP주소의 짝으로 이루어진 사이트별 설정 파일을 추가해야한다.

<br/>

## 2. 리버스 프록시의 라우팅과 SSL 적용하기

- 새로운 애플리케이션이 추가될 때마다 (애플리케이션 컨테이너 시작 → 사이트 설정 파일 Nginx 컨테이너에 복사 → Nginx 컨테이너 재시작)의 과정을 반복하는데 재시작할 때 해당 설정의 업스트림이 모두 접근 가능한지 확인한다.
  - 업스트림이 모두 접근 가능하다면 호스트명과 IP주소를 연결한 내부 라우팅 리스트를 만든다. 업스트림 컨테이너가 여러 개 존재한다면 이들 간의 로드 밸런싱도 처리해준다.
  - 업스트림 중 접근이 불가능한 것이 있다면 Nginx는 종료된다.
- SSL 설정
  - 설정에 지정된 인증서와 키 파일을 컨테이너 파일 시스템에서 읽어 HTTPS 사이트 설정에 사용한다.

<br/>

## 3. 프록시를 이용한 성능 및 신뢰성 개선

- 캐싱 프록시
  - 업스트림에서 받아온 콘텐츠를 디스크나 메모리에 캐시한다.
  - 요청 처리 시간이 줄어들고 애플리케이션을 오가는 트래픽을 줄여 더 많은 요청을 처리할 수 있다.

<br/>

## 4. 클라우드 네이티브 리버스 프록시(Traefik)

- 애플리케이션별로 설정 파일을 따로 둘 필요 없이 컨테이너에 레이블을 추가하기만 하면 된다. 도커 API를 통해 컨테이너와 컨테이너 레이블을 감지하고 이를 토대로 자동으로 설정을 구성한다.(예제 20-4)
- 동적 설정을 구성할 수 있다. 트래픽 실행 전에 모든 애플리케이션을 실행해 두지 않아도 된다.
- 웹 UI로 구성된 설정을 확인할 수 있다.
- 라우팅, 로드밸런싱, SSL 적용, 캐시
- 스티키 세션
  - 같은 사용자의 요청을 같은 컨테이너로 라우팅할 때 적용
  - 컨테이너를 식별할 수 있는 쿠키가 부여되므로 해당 사용자의 요청을 계속 같은 컨테이너로 라우팅

<br/>

## 5. 리버스 프록시를 활용한 패턴의 이해

- 한 클러스터에서 각각 도메인 이름을 갖는 여러 개의 애플리케이션을 호스팅
- 마이크로서비스 아키텍처
- 모놀리식 설계를 가진 애플리케이션을 점진적으로 여러 개의 서비스로 분할
