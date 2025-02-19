**Secret**은 Kubernetes에서 비밀번호, API 키, 인증 토큰 등 민감한 데이터를 안전하게 저장하고 전달하기 위해 사용되는 리소스 객체입니다. ConfigMap과 유사한 방식으로 데이터를 Pod 및 컨테이너에 전달하지만, 민감한 데이터를 다룬다는 점에서 보안이 강화된 설계가 적용됩니다.
### Secret의 필요성

1. **보안 강화**
    - 민감한 데이터를 코드에 직접 포함하거나 ConfigMap에 저장하면 보안 위험이 증가합니다.
    - Secret은 데이터를 암호화된 상태로 저장하고, 필요 시 안전하게 Pod에 전달하여 보안성을 높입니다.

2. **설정과 데이터의 분리**
    - ConfigMap과 마찬가지로 애플리케이션의 민감한 설정 데이터를 코드와 분리하여 관리할 수 있습니다.
    - 이를 통해 민감한 데이터를 안전하게 관리하면서 애플리케이션 배포의 유연성과 재사용성을 유지할 수 있습니다.

3. **환경 간 민감 데이터 관리**
    - 개발, 테스트, 프로덕션 환경마다 다른 민감 데이터를 사용할 수 있습니다.
    - Secret은 환경별 데이터를 독립적으로 관리하고, 필요 시 쉽게 교체할 수 있도록 지원합니다.

4. **Pod와의 안전한 통신**
    - Secret은 Pod와 통신 시 데이터를 암호화하여 전달하며, 권한이 있는 사용자 및 애플리케이션만 접근할 수 있도록 설계되었습니다.

### 동작 방식

- Kubernetes는 Secret 데이터를 **etcd**라는 분산 키-값 저장소에 저장합니다.
- 기본적으로 etcd에 저장된 Secret 데이터는 Base64로 인코딩된 상태로 저장되지만, 이는 보안 수준이 낮습니다.
- **Encryption at Rest**를 활성화하면, etcd에 저장되는 Secret 데이터를 AES-256 같은 암호화 알고리즘으로 암호화하여 보안성을 강화합니다.

#### 1. 데이터 저장 과정

1. **Secret 생성**
    - 사용자가 Secret을 생성하거나 업데이트하면, Secret 데이터는 평문(Plain Text) 상태로 Kubernetes API 서버에 전달됩니다.
    - 예: `kubectl create secret generic my-secret --from-literal=username=admin`

2. **암호화 처리**
    - Kubernetes API 서버는 Encryption at Rest 설정에 따라 데이터를 암호화합니다.
    - API 서버는 암호화 프로바이더(예: AES-256)를 사용하여 데이터를 암호화한 후 etcd에 저장합니다.

3. **etcd에 저장**
    - 암호화된 Secret 데이터는 etcd에 저장됩니다.
    - 이 단계에서 데이터는 암호화되어 있으므로, etcd가 노출되더라도 데이터를 복호화하지 않는 한 민감한 정보를 알 수 없습니다.

#### 2. 데이터 읽기 과정

1. **Secret 요청**
    - Pod, 사용자, 또는 애플리케이션이 Secret 데이터를 요청하면, Kubernetes API 서버는 etcd에서 해당 데이터를 조회합니다.
    - 예: Pod가 환경 변수 또는 볼륨 마운트를 통해 Secret을 요청.

2. **복호화 처리**
    - API 서버는 etcd에서 암호화된 데이터를 가져와 복호화합니다.
    - 복호화는 Encryption at Rest 설정에 따라 자동으로 수행됩니다.

3. **평문 데이터 전달**
    - 복호화된 평문 데이터는 요청한 클라이언트(Pod, 사용자 등)에게 전달됩니다.
    - 이 과정에서 Pod 또는 애플리케이션은 복호화 작업을 따로 수행할 필요가 없습니다.

#### 3. Encryption at Rest의 주요 구성 요소

Encryption at Rest를 활성화하려면 Kubernetes 설정 파일에 암호화 설정을 정의해야 합니다. 주요 구성 요소는 다음과 같습니다:

1. 암호화 프로바이더
- Kubernetes는 암호화 알고리즘을 정의하기 위해 암호화 프로바이더를 사용합니다.
- 예: AES-256-GCM, AES-256-CBC, Secretbox 등.

2. 키(Key) 관리
- 암호화에 사용되는 키는 Kubernetes API 서버 설정에서 정의됩니다.
- 키는 로컬 파일에 저장하거나 외부 키 관리 시스템(KMS)과 통합할 수 있습니다.

3. 암호화 구성 파일
- 암호화 구성 파일은 Kubernetes API 서버가 데이터를 암호화/복호화하는 방식을 정의합니다.
- 파일 예시:

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
  - secrets
  providers:
  - aescbc:
      keys:
      - name: key1
        secret: c2VjcmV0LWtleS1leGFtcGxlCg==
  - identity: {}
```
- **aescbc**: AES-256 알고리즘을 사용하여 데이터를 암호화.
- **identity**: 암호화를 비활성화(평문 저장).

#### 4. Encryption at Rest 활성화 방법

Encryption at Rest를 활성화하려면 Kubernetes API 서버 설정 파일에 암호화 구성을 추가해야 합니다.

1. **암호화 구성 파일 생성**
- `/etc/kubernetes/encryption-config.yaml` 파일 생성:
```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
  - secrets
  providers:
  - aescbc:
      keys:
      - name: key1
        secret: c2VjcmV0LWtleS1leGFtcGxlCg==
  - identity: {}
```

2. **API 서버에 암호화 구성 적용**
- Kubernetes API 서버 매니페스트 파일(`/etc/kubernetes/manifests/kube-apiserver.yaml`)에 다음 플래그 추가:
```yaml
--encryption-provider-config=/etc/kubernetes/encryption-config.yaml
```

3. **API 서버 재시작**
	- API 서버를 재시작하여 암호화 구성을 적용합니다.

4. **기존 데이터 암호화**
	- 이미 저장된 Secret 데이터는 기본적으로 암호화되지 않습니다.
	- `kubernetes secret` 데이터를 재생성하거나, `etcd` 데이터를 수동으로 재암호화(Migration)해야 합니다.


### RBAC를 통한 접근 제어

Kubernetes에서 Secret과 같은 민감한 데이터를 다룰 때, **RBAC**(Role-Based Access Control)는 중요한 역할을 합니다. RBAC를 사용하면 누구(사용자, 시스템, 애플리케이션 등)가 무엇(리소스)에 어떤 작업(읽기, 쓰기 등)을 수행할 수 있는지를 제어할 수 있습니다. 이를 통해 Secret에 대한 접근을 세밀하게 관리할 수 있습니다.


#### RBAC란 무엇인가?

RBAC는 사용자가 수행할 수 있는 작업을 **역할**(Role)로 정의하고, 이를 사용자나 그룹에 **바인딩**(Binding)하여 권한을 부여합니다.

RBAC의 주요 구성 요소는 다음과 같습니다:

1. **Role**
- 특정 네임스페이스 내 리소스에 대한 권한을 정의합니다.
- 예: 특정 네임스페이스의 Secret을 읽거나 쓰는 권한만 부여.

2. **ClusterRole**
- 클러스터 전체 또는 모든 네임스페이스에 대해 권한을 정의합니다.
- 예: 모든 네임스페이스의 Secret을 읽는 권한.

3. **RoleBinding**
- Role을 특정 사용자, 그룹, 또는 서비스 계정에 바인딩합니다.
- 바인딩이 적용되는 범위는 특정 네임스페이스입니다.

4. **ClusterRoleBinding**
- ClusterRole을 특정 사용자, 그룹, 또는 서비스 계정에 바인딩합니다.
- 바인딩이 클러스터 전체에 적용됩니다.

#### RBAC를 사용한 Secret 접근 제어

1. **Role 생성**
	다음은 특정 네임스페이스에서 Secret을 읽을 수 있는 권한을 정의하는 `Role` 예제입니다:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: my-namespace # 네임스페이스 지정
  name: secret-reader-role # Role 이름
rules:
- apiGroups: [""]
  resources: ["secrets"] # Secret 리소스에 대한 권한
  verbs: ["get", "list"] # 읽기 권한 (get: 읽기, list: 목록 조회)
```

2. **RoleBinding 생성**
	위에서 생성한 `Role`을 특정 사용자나 서비스 계정에 바인딩합니다:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: my-namespace # 네임스페이스 지정
  name: secret-reader-binding # RoleBinding 이름
subjects:
- kind: User # 사용자 유형 (User, Group, 또는 ServiceAccount)
  name: my-user # 사용자 이름
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role # 바인딩할 Role의 종류
  name: secret-reader-role # 위에서 생성한 Role 이름
  apiGroup: rbac.authorization.k8s.io
```

3. **ClusterRole 및 ClusterRoleBinding**
	클러스터 전체에서 Secret에 대한 권한을 부여하려면 `ClusterRole`과 `ClusterRoleBinding`을 사용합니다.

##### ClusterRole 예제:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-secret-reader # ClusterRole 이름
rules:
- apiGroups: [""]
  resources: ["secrets"] # Secret 리소스에 대한 권한
  verbs: ["get", "list"] # 읽기 권한
```

##### ClusterRoleBinding 예제:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-secret-reader-binding # ClusterRoleBinding 이름
subjects:
- kind: User # 사용자 유형 (User, Group, 또는 ServiceAccount)
  name: my-user # 사용자 이름
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole # 바인딩할 Role의 종류
  name: cluster-secret-reader # 위에서 생성한 ClusterRole 이름
  apiGroup: rbac.authorization.k8s.io
```

##### Role과 ClusterRole의 차이

| **구분**    | **Role**             | **ClusterRole**        |
| --------- | -------------------- | ---------------------- |
| **범위**    | 특정 네임스페이스            | 클러스터 전체 또는 모든 네임스페이스   |
| **적용 대상** | 네임스페이스 내 리소스         | 클러스터 리소스 또는 네임스페이스 리소스 |
| **사용 예시** | 특정 네임스페이스의 Secret 관리 | 모든 네임스페이스의 Secret 관리   |


#### RBAC를 활용한 Secret 보안 강화

1. **최소 권한 원칙 적용**
   - 사용자와 애플리케이션이 필요한 최소한의 권한만 부여합니다.
   - 예: Secret을 읽기만 해야 하는 경우 `get`과 `list` 권한만 부여.

2. **네임스페이스 분리**
   - 네임스페이스 별로 Secret과 Role을 분리하여 관리.
   - 개발, 테스트, 프로덕션 환경의 Secret을 분리하여 잘못된 접근 방지.

3. **서비스 계정 사용**
   - 애플리케이션 Pod에 서비스 계정을 연결하고, 해당 서비스 계정에 Role을 바인딩하여 Secret 접근을 제어.

4. **로그 및 감사 활성화**
   - Kubernetes API 서버의 감사 로깅(Audit Logging)을 활성화하여 Secret 접근 기록을 남기고, 이상 접근 탐지.

#### Secret 접근 예시
Pod가 Secret 데이터를 환경 변수로 사용할 때 RBAC를 통해 접근을 제어할 수 있습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: my-namespace
spec:
  serviceAccountName: my-service-account # RoleBinding에 연결된 서비스 계정
  containers:
  - name: my-container
    image: nginx
    env:
    - name: SECRET_USERNAME
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: username
```

- 위 Pod는 `my-service-account`라는 서비스 계정을 사용하며, 이 계정에 Secret 읽기 권한이 부여되어야 합니다.

