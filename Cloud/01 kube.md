# 도입
도커 != 컨테이너
- 도커는 컨테이너를 사용할 수 있게 도와주는 플랫폼  
- 컨테이너를 환경에 구애받지 않고 애플리케이션을 신속하게 배포 및 확장가능하게 도와주는 플랫폼  
- 컨테이너를 만들고 관리하기 쉽게 도와주는 플랫폼

## Docker Container Flow
<img src = 'img/01 kube/img1.png'></img>

Docker File -> 인프라도 코드로 관리하는 시대 / 명세서같은 것
Docker commit -> 현재 돌아가는 컨테이너를 이미지로 저장가능

## Docker Layer
<img src = 'img/01 kube/img2.png'></img>

# Kubernetes
Container를 위한 Open Source Orchestration System

- Kubernetes 특징
    1. Automatic bin packing
    2. Self-Healing
    3. Horizontal scaling
    4. Service discovery and Load balancing
    5. Automated rollouts and rollbacks
    6. Secret and Configuration management
    7. Storage orchestration

## Container Orchestration
- MSA로 인해 관리해야할 Container가 증가. 이를 관리해줄 플랫폼이 Container Orchestration  
- Container Orchestration platform 추세
<img src = 'img/01 kube/img3.png'></img>

## Kubernetes 주요 구조 및 기능

<img src = 'img/01 kube/img4.png'></img>
- Kube Arch.  
    Master Worker 구조  
    - Master  
    Cluster 라고함 Control Plan  
        - API Server  
        쿠버네티스 클러스터 안에있는 모든 component의 허브역할 / Restful API제공으로 각각이랑 통신 가능 
        - Controller Manager (kube controller)  
        노드정보 갱신이나, Object Controller 관리
        - Scheduler
        어떤 파드가 실행될지 결정하는 주체
        기준은 CPU memory 그노드에 얼마나 많은 컨테이너가 떠있는지 등을 기반으로하여 알고리즘을 통해 배치.
        - etcd
        저장소역할, 따로 구성한 클러스터 백업 / 복구를 해야할 때 사용하는 데이터들 저장

    - Worker  
        - Kubelet  
        Agent, API서버로 주기적으로 자기 상태를 보고
        - Kube-Proxy  
        외부에서 요청이 들어왔을 때, 
        - CAdvisor
        동작중인 컨테이너 리소스나 퍼포먼스를 모니터링하여 결과를 kubelet을 통해 전달(수집체 역할)  
        - Pod
        kube에서 배포할수 있는 최소 단위
        pod != contatiner
        파드는 한개이상의 컨테이너를 가질 수 있음
        - Plugin Network ( eq Fiannel, Weavenet,etc...)  
        클러스터 내부에서 Overlay 네트웍을 구성할 수 있도록 도와줌.

## Kubernetes 주요 resource

- Namespace  
클러스터 내부의 논리적인 분리 단위  
```zsh
# service 정보 조회
$ kubectl get service -n <namespace>
```
<img src = 'img/01 kube/img6.png'></img>

- Pod
```zsh
# pod 정보 조회
$ kubectl get pod -n <namespace>
```
<img src = 'img/01 kube/img7.png'></img>  
Kube에서 배포할 수 있는 최소 배포 단위(여러개의 컨테이너가 한 pod안에 떠 있을 수 도 있고, 저장소도 포함될 수 있음)  
하나의 IP를 공유하며 이를 통해 Pod로 접속후 각각의 Container에 접속가능하다.  
mortal한 존재 (영구적이지 않음)

- Object Controller
<img src = 'img/01 kube/img8.png'></img>  
Pod 등의 Resource를 모니터링하여 내가 원하는 상태와 현재 상태를 동일하게 만들어 주기 위한 Object

- Object Controller (Deployment)  
대표적인 Controller
<img src = 'img/01 kube/img9.png'></img>  
Deployment를 만들면 ReplicaSet이 자동으로 생성되고 ReplicaSet은 Pod들을 관리한다.
<img src = 'img/01 kube/img10.png'></img>  
Deployment를 만들 때는, replicaset과 Pod Spec을 담는 Manifest를 정의.  

- Manifest
    - Selector  
        Replicaseet에 대한 내용 정의
        - replicas : 띄울 pod가 몇개일지 정의  
    - Template  
        Pod에 대한 내용 정의  


## Service (ClusterIP)  
어떠한 서비스를 제공할 때 Pod랑 통신 할 때 필요한 Endpoint( 영원히 유지하는 Endpoint)
<img src = 'img/01 kube/img11.png'></img>

- Service 종류
    - ClusterIP  
    <img src = 'img/01 kube/img12.png'></img>  
    내부용으로만 접속  
    클라이언트가 클러스터 내부에 있을 때

    - NodePort  
    <img src = 'img/01 kube/img13.png'></img>  
    노드의 Port 외부 접속  
    Private Node의 특정Port를 열어서 외부와 연결

    - Headless  
    StatefulSet과 같이 사용

    - Loadbalancer(외부)  
    <img src = 'img/01 kube/img14.png'></img>  
    상용 LB

- ingress 기능  
<img src = 'img/01 kube/img15.png'></img>  
외부에서 요청이 오면 어떻게 처리할지 규칙
    - SSL 처리

    - URL path 기반 라우팅 처리

    - Method 제어
    (get post put...) 등 이런 요청은 어떻게 처리해 줘