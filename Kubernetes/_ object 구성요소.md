### 구성 요소

- **Name**:
	- 이름은 사람이 읽을 수 있는 식별자로, 주로 API 호출이나 리소스 참조 시 사용됩니다.
	- `/api/v1/pods/some-name`과 같이, 리소스 URL에서 오브젝트를 가리키는 클라이언트 제공 문자열입니다.
	- 클러스터의 각 오브젝트는 해당 유형의 리소스에 대하여 고유한 Name을 가지고 있습니다. 예를 들어, 이름이 myapp-1234인 파드는 동일한 네임스페이스 내에서 하나만 존재할 수 있지만, 이름이 myapp-1234인 파드와 디플로이먼트는 각각 존재할 수 있습니다.
	- 대부분의 리소스 유형에는 [RFC 1123](https://tools.ietf.org/html/rfc1123)에 정의된 대로 DNS 서브도메인 이름으로 사용할 수 있는 이름이 필요합니다. 이것은 해당 리소스 이름이 다음을 충족해야 한다는 것을 의미합니다.
		- 253자를 넘지 말아야 한다.
		- 소문자와 영숫자 `-` 또는 `.` 만 포함한다.
		- 영숫자로 시작한다.
		- 영숫자로 끝난다.
	- 일부 리소스 유형은 [RFC 1123](https://tools.ietf.org/html/rfc1123)에 정의된 대로 DNS 레이블 표준을 따라야 합니다. 이것은 해당 리소스 이름이 다음을 충족해야 한다는 것을 의미합니다.
		- 최대 63자이다.
		- 소문자와 영숫자 또는 `-` 만 포함한다.
		- 영숫자로 시작한다.
		- 영숫자로 끝난다.
	- 몇몇 리소스 타입은 자신의 이름을 [RFC 1035](https://tools.ietf.org/html/rfc1035)에 정의된 DNS 레이블 표준을 따르도록 요구합니다. 이것은 해당 리소스 이름이 다음을 만족해야 한다는 의미입니다.
		- 최대 63개 문자를 포함
		- 소문자 영숫자 또는 '-'만 포함
		- 알파벳 문자로 시작
		- 영숫자로 끝남
	- 일부 리소스 유형에서는 이름을 경로 세그먼트로 안전하게 인코딩 할 수 있어야 합니다. 즉 해당 리소스 이름이 "." 또는 ".."이 아니어야 하며 이름에는 "/" 또는 "%"가 포함될 수 없습니다.

- **UID (Unique Identifier)**:  
	- 이름과는 별개로, 오브젝트를 중복 없이 식별하기 위해 오브젝트가 생성될 때 클러스터에 의해 할당되는 고유한 ID입니다.
	- UID는 내부적으로 사용되며, 오브젝트가 재생성되거나 이름이 변경되더라도 고유성을 보장합니다.
	- 쿠버네티스 클러스터가 구동되는 전체 시간에 걸쳐 생성되는 모든 오브젝트는 서로 구분되는 UID를 갖습니다. 이는 기록상 유사한 오브젝트의 출현을 서로 구분하기 위함입니다.

- **레이블**:  
	- 오브젝트에 부여할 수 있는 키-값 쌍입니다. 이를 통해 오브젝트를 카테고리화하거나 필요한 정보를 담을 수 있습니다.
	- 예를 들어, 애플리케이션 버전, 환경(개발, 테스트, 프로덕션) 등을 레이블로 관리할 수 있습니다.
	- 레이블은 오브젝트의 특성을 식별하는 데 사용되어 사용자에게 중요하지만, 코어 시스템에 직접적인 의미는 없습니다.
	- 레이블로 오브젝트의 하위 집합을 선택하고, 구성하는데 사용할 수 있습니다.
	- 레이블은 오브젝트를 생성할 때에 붙이거나 생성 이후에 붙이거나 언제든지 수정이 가능합니다.
	- 오브젝트의 키는 고유한 값이어야 합니다.
	- **레이블 키**:
	    - 선택적으로 접두사(prefix)와 이름(name)으로 구성되며, 접두사와 이름은 슬래시(/)로 구분됩니다.
	    - 이름은 필수이며 최대 63자입니다.
	    - 이름은 알파벳(소문자, 대문자) 및 숫자로 시작하거나 끝나야 하며, 그 사이에 대시(-), 언더스코어(_), 점(.) 등이 포함될 수 있습니다.
	    - 접두사가 있는 경우, 접두사는 DNS 하위 도메인 형식을 따라야 하며, 총 길이는 253자를 넘을 수 없습니다.
	- **레이블 값**:
	    - 최대 63자이며 비어있을 수도 있습니다.
	    - 값이 비어있지 않다면 역시 알파벳 및 숫자로 시작하고 끝나야 하며, 중간에 대시(-), 언더스코어(_), 점(.) 등이 올 수 있습니다.
	- **중요:** Kubernetes 핵심 구성 요소로 추가되는 레이블은 반드시 "kubernetes.io/" 또는 "k8s.io/"와 같은 예약된 접두사를 사용해야 합니다.
	- 예를 들어, Pod에 다음과 같이 레이블을 추가할 수 있습니다:
```json
"metadata": {
  "labels": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```

- **셀렉터**:  
	- 레이블 셀렉터는 레이블을 이용하여 오브젝트 집합을 식별하는 방법입니다.
	- 여러 오브젝트가 동일한 레이블을 가질 수 있으므로, 이를 통해 특정 기준에 맞는 오브젝트들을 그룹핑할 수 있습니다.
	- Kubernetes API는 두 가지 타입의 Label Selector를 지원합니다.
		1. **동등성 기반(EQUALITY-BASED) Selector**
		    - 레이블 키와 값에 대해 등호(`=` 또는 `==`) 또는 부등호(`!=`)를 사용하여 필터링 합니다.
		    - 모든 지정된 조건을 만족하는 오브젝트가 선택됩니다.
		    - 예시:
		        - `environment = production`  
		            → key가 "environment"이고 값이 "production"인 리소스 선택
		        - `tier != frontend`  
		            → key가 "tier"이고 값이 "frontend"가 아닌 리소스 선택 (해당 레이블이 없는 경우도 선택됨)
		    - 여러 조건이 있을 경우 쉼표(,)는 AND(논리적 곱) 역할을 하여 모두 만족해야 선택됩니다.
		        - 예: `environment=production,tier!=frontend`
		2. **집합 기반(SET-BASED) Selector**
		    - 레이블의 값이 특정 집합에 속하거나 속하지 않음을 기반으로 필터링 합니다.
		    - 지원하는 연산자:
		        - `in`: 지정한 집합에 포함되는 경우
		        - `notin`: 지정한 집합에 포함되지 않는 경우
		        - `exists`: 해당 key의 존재 여부를 체크 (값은 고려하지 않음)
		        - `!` : 해당 key의 부재를 의미 (key가 없는 경우)
		    - 예시:
		        - `environment in (production, qa)`  
		            → key가 "environment"이고 값이 "production" 또는 "qa"인 리소스를 선택
		        - `tier notin (frontend, backend)`  
		            → key가 "tier"인 리소스 중 값이 "frontend"나 "backend"가 아닌 경우, 또는 key가 없는 경우 선택
		        - `partition`  
		            → key가 "partition"인 리소스 모두 선택 (값은 무시)
		        - `!partition`  
		            → key가 "partition"이 없는 리소스 선택
		    - 집합 기반 조건 역시 쉼표(,)를 사용하여 AND 조건을 적용할 수 있습니다.
		        - 예: `partition,environment notin (qa)`  
		            → key로 "partition"이 있는 리소스 중에서, "environment"의 값이 "qa"가 아닌 경우 선택
	- **혼합 사용 가능**: 동등성 기반 조건과 집합 기반 조건을 조합하여 사용할 수 있습니다.  
		- 예: `partition in (customerA, customerB),environment!=qa`
	- Kubernetes의 LIST와 WATCH API에서 레이블 셀렉터를 사용하면 원하는 조건에 따라 리소스를 필터링하여 반환할 수 있고, 이를 통해 kubectl 명령어로도 쉽게 확인할 수 있습니다. 또한, 서비스나 컨트롤러와 같은 오브젝트에서 파드의 집합을 지정할 때 동일한 방식의 레이블 셀렉터가 활용되며, 일부 새로운 리소스에서는 집합성 기준 요건까지 지원하여 더욱 정교한 선택이 가능합니다.
	- 예를 들어, 아래 샘플 파드는 "`accelerator=nvidia-tesla-p100`" 레이블을 가진 노드를 선택합니다.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cuda-test
spec:
  containers:
    - name: cuda-test
      image: "registry.k8s.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1
  nodeSelector:
    accelerator: nvidia-tesla-p100
```

- **네임스페이스(Namespace)**
    - 네임스페이스는 하나의 클러스터 내에서 오브젝트들을 논리적으로 분리하기 위한 방법입니다.
    - 네임스페이스 내에서 리소스의 이름은 유일해야 하지만, 서로 다른 네임스페이스간에는 동일한 이름의 리소스가 존재할 수 있습니다.
    - 주로 리소스를 격리하거나 관리의 편의성을 위해, 여러 팀이나 프로젝트 환경에서 사용하게 됩니다.
	- 클러스터 내에서 리소스의 범위를 구분함으로써, 특정 네임스페이스에서만 적용되는 설정(예: DNS 엔트리, 접근 제어 등)을 쉽게 관리할 수 있습니다.
	- **특징**:
		- **스코프 제한**: 네임스페이스 기반 스코핑은 디플로이먼트, 서비스, 파드 등 네임스페이스에 속하는 오브젝트에만 적용됩니다. 클러스터 범위의 리소스(예: 스토리지클래스, 노드, 퍼시스턴트볼륨 등)는 네임스페이스의 영향을 받지 않습니다.
		- **리소스 이름의 유일성**: 각 네임스페이스 안에서는 리소스 이름이 유일해야 합니다. 그러나 서로 다른 네임스페이스에서는 동일한 이름의 리소스가 허용됩니다.
		- **네임스페이스의 상호 배타성**: 하나의 쿠버네티스 리소스는 오직 하나의 네임스페이스에만 속할 수 있으며, 네임스페이스는 서로 중첩될 수 없습니다.
		- **자동 레이블링** (Kubernetes 1.22 [stable]): 네임스페이스가 생성되면, 컨트롤 플레인에서 변경할 수 없는(immutable) 레이블인 `kubernetes.io/metadata.name`이 네임스페이스 이름으로 자동 설정됩니다.
	- **사용**:
		- **대규모 사용자/팀 환경**: 수십 명 이상의 사용자가 있는 환경에서는 팀 또는 프로젝트별로 네임스페이스를 분리하여 운영하는 것이 좋습니다.
		- **리소스 자원 관리**: 리소스 쿼터를 사용해 서로 다른 네임스페이스에 속한 사용자에게 클러스터 자원을 할당할 수 있습니다.
		- **DNS 네임 해석**:
			- 쿠버네티스에서는 서비스 생성 시 DNS 엔트리가 자동으로 생성됩니다. 이 DNS는 `<서비스-이름>.<네임스페이스-이름>.svc.cluster.local` 형식을 가지며, 짧은 이름은 해당 네임스페이스 내에서만 유효합니다.
			- 따라서, 여러 네임스페이스에서 같은 서비스 이름이 존재하더라도 네임스페이스 경계를 통해 서로 구분됩니다.
	- **초기 네임스페이스**: 쿠버네티스 클러스터에는 초기 상태에서 기본 네임스페이스가 존재합니다.
		- **default**: 클러스터 생성 시 기본으로 제공되며, 별도의 네임스페이스를 생성하지 않아도 사용 가능함.
		- **kube-node-lease**: 각 노드와 연관된 리스(lease) 오브젝트가 저장되는 네임스페이스로, 노드의 상태 모니터링에 사용됨.
		- **kube-public**: 모든 클라이언트(심지어 인증되지 않은 클라이언트도 접근 가능)가 읽기 권한을 가지며, 공개적인 리소스를 위한 네임스페이스.
		- **kube-system**: 쿠버네티스 시스템 구성 요소 및 내부 시스템 오브젝트가 위치하는 네임스페이스.
	- **참고**: "kube-" 접두사가 붙은 네임스페이스는 쿠버네티스 시스템에서 예약되어 있으므로, 사용자가 새로 생성하는 것은 피해야 합니다.
	- **관리:**
		- **네임스페이스 조회**:
			- 현재 클러스터에 존재하는 네임스페이스 목록을 확인할 때 사용
			    `kubectl get namespace`
		- **네임스페이스 지정하여 실행**:
			- 특정 네임스페이스에서 작업을 수행하려면 `--namespace` 플래그 사용:
				`kubectl get pods --namespace=<네임스페이스 이름>`
		- **기본 네임스페이스 설정**:
			- 현재 컨텍스트에 기본 네임스페이스를 설정하여 모든 명령에 자동 적용:  
			    `kubectl config set-context --current --namespace=<네임스페이스 이름>`
			- 확인:
				`kubectl config view --minify | grep namespace:`
	- 네임스페이스에 속하는 리소스와 속하지 않는 리소스를 각각 조회할 수 있습니다:
	    - 네임스페이스에 속하는 리소스:  
	        `kubectl api-resources --namespaced=true`
	    - 네임스페이스에 속하지 않는 리소스:  
	        `kubectl api-resources --namespaced=false`
	- 서비스의 DNS 엔트리는 `<서비스-이름>.<네임스페이스-이름>.svc.cluster.local` 형식을 가지므로, 네임스페이스 이름이 공개 최상위 도메인(TLD)와 동일할 경우, 내부 DNS 이름과 외부 DNS 레코드가 충돌할 수 있으므로 주의해야 합니다.
	- 보안을 위해, 신뢰할 수 있는 사용자만 네임스페이스를 생성하도록 제한하거나 어드미션 웹훅을 통해 네임스페이스 이름 규칙을 강제할 수 있습니다.

- **어노테이션 (Annotation)**
	- 어노테이션은 Kubernetes 객체에 식별 용도가 아닌 임의의 메타데이터를 첨부할 수 있는 방법입니다.
	- **용도**:
		- 객체의 동작이나 관리를 위해 추가적인 정보를 기록하고자 할 때 사용합니다.
		- 레이블(Label)과 달리 어노테이션은 객체를 선별하거나 필터링하는 데 사용되지 않습니다.
		- 클라이언트나 도구(예: 디버깅, 감사, 모니터링)를 위해 필요한 정보를 제공하는 역할을 합니다.
	- **특징:**
		- **키/값 구조**: 어노테이션은 레이블과 같이 키/값의 형태를 가지며, JSON 또는 YAML 형식의 객체 내 metadata 부분에 기록됩니다.
		- **문자열 제한**: 어노테이션의 키와 값은 반드시 문자열이어야 합니다. 숫자, 불리언, 리스트 등 다른 자료형은 허용되지 않습니다.
		- **키의 구조 (Prefix와 이름)**:
		    - 키는 선택적인 prefix와 실제 이름으로 구성되며, 이 둘은 슬래시(`/`)로 구분됩니다.
		    - **이름**: 필수로 63자 이하이어야 하며, 앞과 뒤는 영문자나 숫자로 시작하고 끝나야 하고, 중간에 대시(-), 밑줄(_), 점(.) 등이 올 수 있습니다.
		    - **프리픽스**: 선택적 요소로, 지정하는 경우 DNS 서브도메인 규칙에 따라 253자 이하의 문자열이어야 합니다. 예를 들어: `example.com/key`에서 `example.com/`은 프리픽스, `key`는 이름입니다.
		- **예약어**: 시스템 자동화 도구나 코어 컴포넌트가 사용하는 어노테이션의 경우, `kubernetes.io/` 또는 `k8s.io/` 같은 프리픽스가 예약되어 있으므로 사용자가 해당 프리픽스를 사용하지 않도록 주의해야 합니다.
	- 아래는 `imageregistry` 라는 어노테이션을 포함한 Pod 매니페스트 예시입니다.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: annotations-demo
  annotations:
    imageregistry: "https://hub.docker.com/"
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

### 예시

쿠버네티스의 선언형 구성은 특히 **확장성** 측면에서 강점을 제공합니다.

1. **수평적 확장(Horizontal Scaling)**
	- 선언형 구성 파일에서 **replicas** 값을 변경하면, 쿠버네티스는 자동으로 파드의 수를 조정합니다.
	- 예를 들어, 트래픽 증가로 인해 애플리케이션의 처리 용량을 늘려야 할 경우, YAML 파일의 `replicas` 값을 증가시키기만 하면 됩니다. 쿠버네티스 컨트롤러는 이를 감지하고 필요한 만큼의 파드를 추가로 생성합니다.
```yaml
# Deployment 예시: 수평적 확장
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-app
spec:
  replicas: 10  # 기존 3에서 10으로 확장
  selector:
    matchLabels:
      app: example-app
  template:
    metadata:
      labels:
        app: example-app
    spec:
      containers:
      - name: example-container
        image: example-image:latest
```

2. **오토스케일링(Autoscaling)**
	- **Horizontal Pod Autoscaler**(HPA)와 같은 기능을 선언형 구성으로 설정하여, 애플리케이션이 CPU 및 메모리 사용량에 따라 자동으로 확장 또는 축소되도록 구성할 수 있습니다.
	- HPA는 선언형 구성으로 정의되며, 클러스터의 리소스 사용량을 지속적으로 모니터링하고, 요구 사항에 따라 파드 수를 동적으로 조정합니다.
```yaml
# HPA 예시: CPU 사용량 기반 자동 확장
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: example-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: example-app
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70  # CPU 사용량 70% 이상일 때 확장
```

3. 환경 변수 주입
	- 선언형 구성 파일은 개발, 스테이징, 프로덕션 등 다양한 환경에서 동일하게 재사용될 수 있습니다. 환경별로 차이가 나는 변수는 `ConfigMap` 또는 `Secret`을 활용하여 동적으로 주입할 수 있습니다.
```yaml
# ConfigMap 예시: 환경 변수 관리
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  DATABASE_URL: "postgresql://db.example.com:5432/appdb"
  LOG_LEVEL: "INFO"
```

```yaml
# Deployment와 ConfigMap 연동
spec:
  containers:
  - name: example-container
    image: example-image:latest
    envFrom:
    - configMapRef:
        name: app-config
```

4. 워크로드 관리
	- 선언형 구성은 단순히 애플리케이션 배포뿐만 아니라, 복잡한 워크로드(예: 배치 작업, 크론잡 등)도 관리할 수 있습니다. 예를 들어, 특정 시간에 실행되어야 하는 작업은 `CronJob` 리소스를 활용하여 선언적으로 설정할 수 있습니다.
```yaml
# CronJob 예시: 매일 자정에 실행되는 작업
apiVersion: batch/v1
kind: CronJob
metadata:
  name: daily-job
spec:
  schedule: "0 0 * * *"  # 매일 자정
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: example-job
            image: example-job-image:latest
          restartPolicy: OnFailure
```

