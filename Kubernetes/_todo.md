클러스터 설계와 네트워크 이해
	노드 구성 → Pod 간 통신 → CNI → 네트워크 정책
Namespace와 멀티테넌시
	Namespace → RBAC → 멀티테넌시
리소스 관리
	리소스 요청과 제한 → HPA → VPA
클러스터 모니터링과 로깅
	Prometheus와 Grafana → ELK 스택 또는 Fluentd
스토리지와 데이터 관리
	PV와 PVC → 스토리지 클래스 → StatefulSet → 백업과 복구



### **Kubernetes 컨트롤 플레인 관련 항목**

1. **컨트롤 플레인(Control Plane)**
    - 컨트롤 플레인의 역할과 구성 요소
    - 클러스터 상태 관리 및 작업 조정

2. **API 서버(Kubernetes API Server)**    
    - Kubernetes API 서버의 역할
    - kubectl 명령어와 API 서버의 상호작용

3. **스케줄러(Scheduler)**    
    - 스케줄러의 역할 (Pod를 적절한 노드에 배치)
    - 스케줄링 정책과 우선순위

4. **컨트롤러 매니저(Controller Manager)**    
    - 다양한 컨트롤러의 역할 (ReplicationController, NodeController 등)
    - 클러스터 상태를 원하는 상태로 유지

5. **ETCD**    
    - Kubernetes의 분산 키-값 저장소
    - 클러스터 상태 데이터 저장 및 복구

6. **큐브 프록시(kube-proxy)**    
    - 네트워크 라우팅 및 서비스 통신 관리
    - iptables 또는 IPVS를 활용한 트래픽 처리

7. **노드 컴포넌트(Node Components)**    
    - kubelet: 각 노드에서 Pod 실행 및 관리
    - 컨테이너 런타임(Container Runtime)
    - kube-proxy: 네트워크 설정 및 서비스 관리


### **추가로 다뤄야 할 관련 내용**

1. **kubectl 사용법**
    - kubectl 명령어를 활용한 클러스터 관리
    - YAML 파일을 통한 선언형 리소스 관리

2. **Kubernetes 아키텍처**    
    - Master-Node 구조
    - 클러스터 내부 통신 흐름

3. **Kubernetes 인증 및 권한 관리**    
    - 인증(Authentication)과 권한 부여(Authorization)
    - TLS 인증, RBAC 설정

4. **클러스터 설정 및 설치**
    - kubeadm, Minikube, Kind를 이용한 클러스터 설치
    - 클라우드 제공 Kubernetes(예: AWS EKS, GCP GKE, Azure AKS)