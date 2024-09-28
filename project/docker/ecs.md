### ecs 란?

`ECS`는 `**Elastic Container Service**`의 약자로, `AWS(Amazon Web Services)`에서 제공하는` **컨테이너 오케스트레이션** 서비스`입니다. `ECS`는 `Docker 컨테이너`를 **실행**, **관리**, **확장**하는 데 사용되며, 복잡한 인프라를 관리할 필요 없이 컨테이너화된 애플리케이션을 쉽게 배포하고 운영할 수 있도록 돕습니다.

• **완전 관리형 서비스**: 인프라 설정이나 관리 부담 없이 컨테이너 오케스트레이션을 수행할 수 있습니다.

• **높은 확장성**: 트래픽 증가에 따라 애플리케이션을 자동으로 확장할 수 있습니다.

• **보안 및 통합**: `VPC`, `IAM`, `CloudWatch` 등 `AWS`의 다른 서비스와 긴밀하게 통합되어 안전하고 관리가 용이합니다.

• **Fargate 통합**: 서버리스 컴퓨팅을 통해 인프라 관리를 최소화할 수 있습니다.

---
### 핵심 개념

1. **컨테이너(Container)**:
   - 컨테이너는 애플리케이션과 그 종속성을 하나의 패키지로 묶어, 어디서든 일관되게 실행할 수 있게 해주는 기술입니다. Docker가 가장 널리 사용되는 컨테이너 플랫폼입니다.

2. **태스크(Task)**:
   - ECS에서 태스크는 하나 이상의 컨테이너로 구성된 작업 단위입니다. 특정 애플리케이션 기능을 수행하는 데 필요한 컨테이너 그룹을 정의합니다.

3. **태스크 정의(Task Definition)**:
   - 태스크 정의는 컨테이너의 설정(예: 이미지, CPU, 메모리, 네트워크 설정 등)을 지정하는 JSON 파일입니다. 이는 ECS에서 태스크를 생성하고 실행할 때 필요한 청사진 역할을 합니다.

4. **서비스(Service)**:
   - 서비스는 지정된 수의 태스크가 항상 실행되도록 보장하는 ECS 기능입니다. 예를 들어, 3개의 태스크를 정의하면 ECS는 항상 3개의 태스크가 실행되도록 관리합니다. 서비스는 오토 스케일링과 로드 밸런싱을 지원합니다.

5. **클러스터(Cluster)**:
   - 클러스터는 ECS에서 태스크와 서비스를 실행하는 논리적 그룹입니다. 클러스터는 EC2 인스턴스 또는 AWS Fargate와 같은 인프라 자원으로 구성되며, ECS에서 이 자원들을 관리합니다.

---
### 장점

1. **완전 관리형 서비스**: `AWS` `ECS`는 완전 관리형 서비스로, 클러스터 관리, 서버 패치, 업그레이드 등을 자동으로 처리해줍니다. 따라서 인프라 관리에 대한 부담이 줄어듭니다.

2. **AWS 서비스와의 통합**: `ECS`는 `AWS`의 다른 서비스, 예를 들어 `AWS Fargate`, `IAM`, `CloudWatch`, `VPC`, `ELB` 등과 쉽게 통합할 수 있습니다. 이는 보안, 로깅, 모니터링, 네트워킹을 간소화하는 데 도움을 줍니다.

3. **Fargate 지원**: ECS는 서버리스 컨테이너 실행 옵션인 `AWS Fargate`와 함께 사용할 수 있습니다. 이를 통해 사용자는 인프라를 관리할 필요 없이 컨테이너만 신경 쓸 수 있습니다.

4. **비용 효율성**: `ECS`는 필요에 따라 **클러스터의 크기를 자동으로 확장하거나 축소**할 수 있어, 리소스를 효율적으로 사용하고 비용을 절감할 수 있습니다. 또한, `Fargate`와 함께 사용할 경우 미사용 자원에 대한 비용을 줄일 수 있습니다.

5. **높은 보안 수준**: `AWS`의 보안 기능을 활용하여 `ECS`에서 실행되는 컨테이너에 대해 강력한 보안을 제공할 수 있습니다. IAM 역할, VPC 격리, 네트워크 ACL, 보안 그룹 등을 이용해 보안을 강화할 수 있습니다.

6. **쉽고 직관적인 사용**: `AWS Management Console`, `AWS CLI`, `SDK`를 통해 쉽게 `ECS`를 설정하고 관리할 수 있습니다. 복잡한 설정 없이 컨테이너 오케스트레이션을 시작할 수 있습니다.

---
### 단점

1. **복잡성**: ECS는 간단하게 사용할 수 있지만, 대규모로 확장하거나 복잡한 아키텍처를 구현하려면 여러 `AWS` 서비스와의 통합이 필요하여 설정이 복잡해질 수 있습니다.

2. **AWS 종속성**: `ECS`는 `AWS` 전용 서비스이기 때문에 멀티 클라우드 전략을 사용하고자 할 때 제약이 있을 수 있습니다. 다른 클라우드 서비스로의 이전이 어렵습니다.

3. **Fargate 비용**: `Fargate`를 사용하는 경우, 작은 워크로드에서는 비용 효율적일 수 있지만, 대규모 애플리케이션에서는 비용이 빠르게 증가할 수 있습니다. 이는 사용자에게 비용 부담으로 작용할 수 있습니다.

4. **제한된 기능성**: `ECS`는 `Kubernetes(K8s)` 같은 다른 오케스트레이션 도구에 비해 기능적으로 제한적일 수 있습니다. 예를 들어, `Kubernetes(K8s)`는 더 다양한 오픈소스 도구 및 커뮤니티 지원을 제공하는 반면, `ECS`는 `AWS`에 특화된 기능을 제공합니다.

5. **멀티 클러스터 관리**: `ECS`에서 여러 클러스터를 관리하는 것이 다소 번거로울 수 있습니다. 특히 여러 리전에 걸쳐 있는 클러스터를 효율적으로 관리하는 데 어려움이 있을 수 있습니다.

---

### 배포

1. 클러스터 생성

`AWS Management Console`에서 `ECS` **클러스터를 생성**합니다. `Fargate`나 `EC2` 모드 중 하나를 선택할 수 있습니다.

2. 작업 정의 생성

작업 정의를 작성하여 `컨테이너 설정`을 구성합니다. `Docker 이미지`, `CPU` 및` 메모리` 요구사항, `네트워크` 설정 등을 지정합니다.

3. 서비스 생성 및 배포

서비스를 생성하여 작업을 실행하고, 원하는 수의 작업이 항상 실행되도록 설정합니다. `ALB`와 연동하여 트래픽을 분산할 수 있습니다.

4. 모니터링 및 확장

`CloudWatch`를 통해 애플리케이션의 상태를 모니터링하고, 필요 시 서비스 또는 클러스터를 확장할 수 있습니다.

---
### 사용사례

1. 웹 애플리케이션 호스팅

`ECS`는 다양한 언어와 프레임워크로 작성된 웹 애플리케이션을 배포하는 데 적합합니다. ECS 서비스를 통해 다양한 서버에 걸쳐 웹 애플리케이션을 실행하고, `ALB(Application Load Balancer)`와 연동하여 트래픽을 분산할 수 있습니다.

2. 백엔드 API 서비스

`ECS`는 `마이크로서비스 아키텍처`에서 `백엔드 API`를 관리하는 데 유용합니다. 각 마이크로서비스를 별도의 `ECS` 작업으로 실행하고, `서비스 디스커버리`와 `로드 밸런싱`을 통해 효율적으로 관리할 수 있습니다.

3. 배치 작업 처리

`ECS`를 사용하여 대량의 `배치 작업`을 효과적으로 처리할 수 있습니다. `Fargate`와 결합하면 서버 관리 없이도 대규모 배치 작업을 실행할 수 있으며, 작업 완료 후 자동으로 리소스를 해제할 수 있습니다.