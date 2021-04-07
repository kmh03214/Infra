# 8.1 인그레스(Ingress) 참고하기 : https://bcho.tistory.com/1263?category=731548 
    주로 클러스터 외부에서 안에 있는 파드에 접근할 때 사용하는 방법
    서비스와 차이점은 L7 영역의 통신을 담당하여 처리

- 인그레스란?
    클러스터 외부 -> 내부로 접근하는 요청을 어떻게 처리할지 정의해둔 규칙 모음
    실제로 동작시키는 것은 Ingress Congroller 이다.

    ex) mcm-fronted-ingress.yaml <-> mcm-airflow-ingress.yaml 비교분석

# 8.2 Ingress-nginx 컨트롤러
    인그레스는 사실 설정일 뿐이고, 설정 내용대로 동작하는 실제 주체는 인그레스 컨트롤러다. 컨트롤러는 여러가지가 있다.

- 공식 인그레스 컨트롤러 종류
    - Google 컴퓨트 엔진용 
        ingress-gce
    - nginx용 
        ingress-nginx

# 8.3 Ingress SSL 설정

# 8.4 무중단 배포
헬스체크 방법 : Readiness / liveness -> https://bcho.tistory.com/1264 
- Liveness probe
    컨테이너가 살아 있는지 아닌지를 체크
- Readiness probe
    컨테이너가 서비스 가능한 상태인지 체크
    
    컨테이너가 비정상적으로 작동이 불가능한 경우도 있지만, Configuration을 로딩하거나, 데이타를 로딩하거나 외부서비스를 호출하는 경우에는 일시적으로 서비스가 불가능한 상태가 될 수 있다.
    이런경우 컨테이너를 재시작한다 하더라도 정상적으로 서비스가 불가능 할 수 있음

    따라서, 컨테이너를 일시적으로 서비스가 불가능한 상태로 마킹해주면됨. 쿠버네티스 서비스와 함께 사용하면 유용하게 이용가능
- Liveness vs Readiness 둘의 차이점
    Liveness probe는 컨테이너 상태가 비정상이라고 판단하면, pod를 재시작
    Readiness 는 해당 pod를 사용할 수 없음으로 표시 후, 서비스등에서 제외한다.



