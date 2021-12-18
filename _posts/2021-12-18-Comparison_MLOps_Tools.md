---
title: "다양한 MLOps Tools: 나는 무엇을 이용해야 할까?"
description: "Comparison MLOps Tools"
layout: post
toc: false
comments: true
search_exclude: true
categories: [MLOps]
image: "/images/MLOps/head.png"
---

목적: MLOps를 공부해보려 했는데 어디에서 부터 손을 대야 할지 모르겠으며 다양한 툴에서 헤매고 있는 제 자신을 구하고자 찾아왔습니다. 아래 원문을 번역한 글입니다.

원문: [https://www.datarevenue.com/en-blog/airflow-vs-luigi-vs-argo-vs-mlflow-vs-kubeflow](https://www.datarevenue.com/en-blog/airflow-vs-luigi-vs-argo-vs-mlflow-vs-kubeflow)

# **Task orchestration tools and workflows**

작업 및 데이터 워크 플로우를 조정하기 위한 여러가지 도구들이 있습니다. 그렇기 때문에 저처럼 헤매고 있는 분들도 계시겠죠? 그래서 이 글은 가장 인기 있는 도구 중 일부를 비교하는 글입니다.

- Apache Airflow 가장 인기 있는 도구이자 가장 광범위한 기능
- Luigi 시작하기 더 간단한 Airflow와 유사한 도구
- Argo 팀이 이미 Kubernetes를 사용하고 있을 때 자주 찾는 팀이며
- Kubeflow, MLFlow 기계 학습 모델 배포 및 실험 추적과 관련된 더 많은 세부 요구 사항을 제공

자세한 비교를 시작하기 전에 task 오케스트레이션과 관련된 몇 가지 더 넓은 개념을 이해하는 것이 좋다고 합니다.

# **What is task orchestration and why is it useful?**

소규모 팀은 일반적으로 작업을 수동으로 관리하는 것으로 시작합니다. (데이터 정제, 기계 학습 모델 학습, 결과 추적, 프로덕션 서버에 모델 배포) 팀과 솔루션의 규모가 커짐에 따라 반복 단계의 수도 커집니다. 그래서 작업을 안정적으로 실행하는 것이 더욱 중요해집니다.

이러한 작업이 서로 의존적으로 더 복잡해집니다. 시작할 때 일주일에 한 번 또는 한 달에 한 번 실행해야 하는 작업 `파이프라인`이 있을 수 있습니다. 이러한 작업은 특정 순서대로 실행해야 합니다. 작업이 커지면서 이 파이프라인은 동적 분기가 있는 `네트워크`가 됩니다. 어떤 경우에는 일부 작업이 다른 작업을 시작하며 이러한 작업은 먼저 실행되는 다른 여러 작업에 따라 달라질 수 있습니다.(아래 사진 오른쪽 참조)

이 네트워크는 각 작업과 이들 간의 종속성을 모델링하는 DAG(Directed Acyclic Graph)로 모델링할 수 있습니다.

![https://global-uploads.webflow.com/5d3ec351b1eba4332d213004/5f3d5fc803a7a16ca9eda2c3_v545yQmSJm0bgn5gGK1gmAK4Jw1W232zutQ3zs32lDq0HxhsteO97EG2a7NnoObsNoE4Zl0084kpBwZCt7T-BiDil1Z-MvaE_s8C0Ytc7gN5bFnGibnOxWAbOnraEXLxZvAi5HcL.png](https://global-uploads.webflow.com/5d3ec351b1eba4332d213004/5f3d5fc803a7a16ca9eda2c3_v545yQmSJm0bgn5gGK1gmAK4Jw1W232zutQ3zs32lDq0HxhsteO97EG2a7NnoObsNoE4Zl0084kpBwZCt7T-BiDil1Z-MvaE_s8C0Ytc7gN5bFnGibnOxWAbOnraEXLxZvAi5HcL.png)

A pipeline is a limited DAG where each task has one upstream and one downstream dependency at most.

워크플로우 오케스트레이션 도구를 사용하면 모든 작업과 작업이 서로 의존하는 방식을 지정하여 DAG를 정의할 수 있습니다. 그런 다음 오케스트레이션 도구는 다음 작업을 실행하기 전에 실패한 작업을 다시 시도하여 정확한 순서로 일정에 따라 이러한 작업을 실행합니다. 또한 진행 상황을 모니터링하고 실패가 발생하면 팀에 알립니다. 와..

일반적으로 CI/CD 도구하면 떠오르는 Jenkins는 코드를 자동으로 테스트하고 배포하는데 사용되며 확실히 작업 오케스트레이션 도구 사이에는 강력한 유사점이 있지만 중요한 차이점도 있습니다.
이론상으로는 이러한 CI/CD 도구를 사용하여 동적이고 상호 연결된 작업을 오케스트레이션할 수 있지만 특정 수준의 복잡성에서는 Apache Airflow와 같은 보다 일반적인 도구를 대신 사용하는 것이 더 쉽다는 것을 알게 될 것입니다.

전반적으로 모든 오케스트레이션 도구의 초점은 모든 자동화 작업을 위한 가상 명령 센터인 `중앙 집중식`, `반복 가능`, `재현 가능` 및 `효율적인` 워크 플로우를 보장하는 것입니다. 이러한 맥락을 염두에 두고 가장 인기 있는 워크플로우 도구 중 일부가 어떻게 구성되어 있는지 살펴보겠습니다.

# Luigi

Luigi는 Python 라이브러리이며 pip 및 conda와 같은 Python 패키지 관리 도구와 함께 설치할 수 있습니다.  
Python 개발자가 쉽게 온보딩하는 것을 목표로 하며 Airflow보다 간단하고 가벼운 것을 목표로 하고 있습니다.  
Python 개발자를 위해 DAG task 정의를 Python으로 할 수 있습니다. 또한, 학습 곡선이 쉬운편이라 시간을 조금만 투자해도 초보자에서 벗어날 수 있습니다.
소규모 팀이 있고 빠르게 시작해야 하고 데이터 정리에서 모델 배포에 이르기까지 다양한 작업을 오케스트레이션해야 하는 경우 사용을 권장드립니다.

# Airflow

Airflow는 Apache 프로젝트이며 완전히 오픈 소스이며 일정 관리와 관련하여 훨씬 더 강력하며 작업 실행 시간을 설정하는 데 도움이 되는 캘린더 UI를 제공합니다.  
Airflow는 Python 광범위 에코시스템 내에 있습니다. 또한, DAG 정의를 Python으로 합니다 (Airflow에 특정한 방식으로 수행)  
Airflow는 일반적인 작업 오케스트레이션 플랫폼이며 다양한 작업을 실행할 수 있는 툴이 높은 성숙도를 가집니다.  
Airflow에는 더 넓은 범위의 사용 사례가 있으며 모든 작업 집합을 실행하는 데 사용할 수 있습니다. Airflow는 작업을 관리하고 예약하기 위한 구성 요소 및 플러그인 세트입니다.

팀 규모가 더 크고 학습 곡선을 넘고 나서 더 많은 리소스를 투입해 초기 생산성에 영향을 끼칠 수 있는 경우 Airflow

# Kubeflow

Kubeflow는 특히 기계 학습 워크플로를 위한 Kubernetes 기반 도구입니다. 이름에서 알 수 있다시피 Kubernetes에 의존적입니다.  
그래서 Kubeflow를 사용하면 각 단계가 Kubernetes 포드인 전체 DAG를 빌드할 수 있습니다.  
Kubeflow에는 실험 추적, 하이퍼 매개변수 최적화 및 Jupyter 노트북 제공을 위한 사전 구축된 패턴이 있습니다. Kubeflow는 `Kubeflow`와 `Kubeflow Pipelines`의 두 가지 고유한 구성 요소로 구성됩니다. 후자는 모델 배포 및 CI/CD에 중점을 두고 있으며 주요 Kubeflow 기능과 독립적으로 사용할 수 있습니다.  
Kubeflow Pipelines 구성 요소를 사용하면 DAG를 지정할 수 있지만 일반 작업보다 배포 및 모델 제공에 더 중점을 둡니다.

이미 Kubernetes를 사용 중이고 기계 학습 솔루션에 대해 즉시 사용 가능한 더 많은 패턴을 원하는 경우 사용을 권장드립니다.

# **MLFlow**

MLFlow는 기계 학습 수명 주기 및 실험을 관리하고 추적하는 데 도움이 되는 보다 전문화된 도구입니다.  
MLFlow를 기계 학습 코드로 function 기능을 사용하여 정보(예: 사용 중인 매개변수)를 기록, 아티팩트(예: 훈련된 모델)를 직접 가져올 수 있습니다. MLFlow를 CLT(Command-line tool)로 사용하여 일반적인 도구(예: scikit-learn)로 빌드된 모델을 제공하거나 일반적인 플랫폼(예: AzureML 또는 Amazon SageMaker)에 배포할 수도 있습니다.

1. 추적을 실험하는 더 간단한 접근 방식을 원하고 Amazon Sagemaker와 같은 관리형 플랫폼에 배포하려는 경우
2. 기계 학습 실험 및 배포를 관리하는 독창적이고 즉시 사용 가능한 방법을 원하는 경우

1와 2의 조건에 맞는 다면 사용을 권장드립니다.

# Argo

Argo는 작업을 Kubernetes 포드로 정의하고 YAML로 정의된 DAG로 실행할 수 있는 작업 오케스트레이션 도구입니다.  
Argo는 Kubernetes 위에 구축되었으며 각 작업은 별도의 Kubernetes pod로 실행됩니다. 이는 대부분의 인프라에 이미 Kubernetes를 사용하고 있는 경우 편리할 수 있지만 그렇지 않은 경우 복잡성이 추가됩니다.

이미 Kubernetes에 투자했고 서로 다른 스택에 작성된 다양한 작업을 실행하려는 경우 사용을 권장드립니다.

# Prefect

Prefect는 Airflow가 너무 복잡하고 경직되어 매우 민첩한 환경에 적합하지 않다는 것을 포함하여 Airflow에 대해 인지된 많은 문제를 해결하기 위해 제작되었습니다.  
Prefect를 사용하면 모든 Python 함수가 작업이 될 수 있으며 모든 것이 예상대로 실행되는 한 Prefect는 방해가 되지 않고 상황이 잘못될 때만 지원을 시작합니다.  
Prefect는 Luigi처럼 Python 개발자가 쉽게 온보딩하는 것을 목표로 합니다. 하지만 Prefect는 Luigi보다 워크플로가 구조화되는 방식에 대해 더 적은 가정을 하고 모든 Python 기능을 작업으로 전환할 수 있습니다. Prefect는 Luigi보다 덜 성숙하고 [오픈 코어](https://en.wikipedia.org/wiki/Open-core_model) 모델입니다

가능한 한 빨리 경량화하고 실행해야 하는 경우 사용을 권장드립니다.
