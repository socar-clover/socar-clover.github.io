---
layout: post
title: "쏘카 데이터 그룹 - 데이터 엔지니어링 팀이 하는 일"
subtitle: "데이터 엔지니어는 무슨 일을 할까?"
date: 2021-03-24 18:00:00 +0900
category: data
background: '/img/what-socar-data-engineering-team-does/juri-noga-Nt-lInxgFaw-unsplash.jpg'
author: hardy
comments: true
tags:
    - data
    - data engineering
---



안녕하세요. 데이터 엔지니어링 팀에서 이제 막 신입 1년차를 마친 하디입니다.
요즘 취업 준비하시는 분들에게 '데이터 엔지니어가 되려면 어떤 걸 준비해야 하나요?'라는 질문을 종종 받습니다. 그리고 데이터 엔지니어는 무슨 일을 하는지, 어떤 기술들을 다루는지 궁금해하시는 분들도 많은 것 같습니다.  

이번 글에서는 **쏘카의 데이터 엔지니어링 팀은 무슨 일을 하고, 어떤 이슈를 다루는지를 소개합니다.** (데이터 엔지니어 역할, 데이터 엔지니어 업무)

기술적으로 매우 세세한 부분보다는 전체적으로 어떤 일들을 하는지, 어떤 이슈를 다루는지 위주로 적어보았습니다.
평소 데이터 엔지니어링이 어떤 일을 하는지 궁금해하시던 분들에게 도움이 되기를 바래봅니다.

## 목차

- 데이터 그룹과 데이터 엔지니어링 팀
- 데이터 엔지니어링 팀 업무
  - 1) 자유로운 통합 데이터 분석 환경 만들기
  - 2) 데이터 지표와 보고서 만들기
  - 3) 데이터 기반의 백엔드 서비스 개발 및 운영
  - 4) 데이터 그룹의 인프라/리소스 관리
  - 정리
- 앞으로 목표와 할 일들



---

## 데이터 그룹과 데이터 엔지니어링 팀

데이터 엔지니어링 팀을 말하기 앞서, 팀의 소속 그룹인 데이터 그룹에 대해 먼저 간략히 소개합니다.  
현재 데이터 그룹은 크게 다음과 같이 구성되어 있습니다.

- 데이터 분석 팀
    - 현업 부서의 운영을 효율화 및 자동화하기 위한 데이터 분석 및 머신러닝/딥러닝/최적화 모델링을 수행합니다.
    - 사고 리스크, 차량 파손 탐지, 보험 사기 탐지, 타다 재배치, Fleet 효율화 모델 개발 및 최적화 등을 운영하고 있습니다.
- 비즈니스 데이터 팀
    - 비즈니스 사업 분야에서의 최적의 손익을 이끌어 낼 수 있는 솔루션 제시합니다.
    - 비즈니스 현황 분석을 진행한 후, 담당 부서와 액션 아이템을 도출합니다.
- 데이터 엔지니어링 팀
    - 데이터 분석가 및 사내 다양한 팀들이 손쉽게 서비스 데이터를 다루고 분석할 수 있는 기반을 마련합니다.
    - 데이터 분석팀들이 만든 모델 혹은 비즈니스 로직을 실제로 적용할 수 있도록 백엔드 서비스를 개발하고 환경을 구축합니다.

**데이터 엔지니어링 팀의 기본적인 임무는 다른 팀을 위한 "지원" 입니다.** 동시에 이 지원을 어떻게 하면 효율적으로 처리시킬 수 있을지에 대해서 고민하고 해결합니다. 여기에는 데이터 적재의 자동화, 속도와 확장 가능성을 고려한 운영, 그리고 개발과 인프라에 대한 것들이 포함되어 있습니다.

이제 데이터 엔지니어링 팀이 어떤 고민을 하고, 어떤 문제들을 풀어가는지 아래에서 살펴보겠습니다.



## 데이터 엔지니어링 팀 업무



### 자유로운 통합 데이터 분석 환경 만들기

![](/img/what-socar-data-engineering-team-does/datalake-pipeline.png)

데이터 엔지니어링 팀의 첫 번째 고민은 '자유로운 통합 데이터 분석 환경을 어떻게 만들 수 있을 것인가?'입니다.  
보통 IT 서비스 회사들은 서비스를 먼저 배포, 운영한 후에 사업적 고도화를 위해 데이터 분석을 시도해보려고 합니다.  
이 때 별도의 분석 환경 없이 서비스의 데이터 저장소에 접근하여 데이터를 분석하려고 하면 크게 2가지의 이슈를 만나게 됩니다.

- 실시간으로 유저에게 서비스 되고 있는 데이터 저장소에 직접 접근하는 것은 위험하다.
    - 분석 중 자칫 잘못하다가 데이터를 오염(수정 혹은 삭제)시킬 수 있습니다.
    - 데이터를 조회하기 위해 사용하는 쿼리가 DB에 부하를 주는 경우, 운영중인 서비스의 성능에 영향을 줍니다. 
- 분석하고자 하는 데이터가 산발적으로 퍼져있어, 통합적으로 분석하기가 어렵다.
    - 예를 들어 서비스 A는 MySQL을 사용하고 있고, 서비스 B는 MongoDB를 사용하고 있는 경우, A의 데이터와 B의 데이터를 하나의 쿼리로 조회하기가 어렵습니다. 
    - 애초에 모든 서비스가 MySQL만 사용하면 되겠다고 생각할 수 있지만, 서비스를 개발할 당시에는 이런 통합 데이터 저장소를 고려하지 않는 경우가 대부분이고 하나의 데이터 저장소만 사용해서는 안되는 경우도 존재합니다.

이 이슈들은 두 가지 필요성을 느끼게 해줍니다.

- **서비스 운영 DB가 아닌 별도의 저장소에서 분석 환경이 필요합니다.**
- **여기저기 퍼져있는 데이터들을 한 군데(별도의 저장소)로 모아야 합니다.**

쏘카 데이터 엔지니어링 팀은 별도의 통합 저장소로 [Google Cloud Platform의 BigQuery](https://cloud.google.com/bigquery?hl=ko)를 사용합니다.  
그리고 데이터를 모으는데 사용하는 워크플로우 툴로 [Apache Airflow](https://airflow.apache.org/)를 사용하고 있습니다.

> *** BigQuery와 Airflow를 선택한 이유는?**
>
> BigQuery는 대용량 데이터 분석 목적으로 개발된 GCP의 클라우드 서비스입니다.  
> BigQuery를 선택한 이유를 한마디로 표현하면, 당장 사용하기 쉽고 인프라나 운영에 크게 신경쓸 것이 없기 때문입니다. 또한 비용도 저렴하고 쿼리 속도도 뛰어납니다.  
> 
>Airflow는 Airbnb에서 개발한 파이썬 기반의 배치성 파이프라인 프레임워크입니다.  
> 비슷한 툴로는 Luigi, Argo Workflow 등이 있습니다.  
> Airflow를 선택한 이유는 팀원들 대부분 파이썬에 익숙했고, Airflow로 파이프라인을 만드는데 그리 큰 시간이 들지 않았기 때문입니다. 더불어 오픈소스라 무료로 사용 가능하고, 원하는대로 커스터마이징이 가능합니다. 또한 Github에서 많은 수의 Star를 받은 만큼, 다른 프레임워크에 비해 커뮤니티가 잘 활성화되어 있습니다. 따라서 트러블 슈팅하는데 필요한 자료를 찾기가 용이합니다.

통합 데이터 저장소에 저장할 데이터는 필요에 따라 쏘카의 여러 서비스에서 가져옵니다.   
특히 쏘카 서비스는 내부적으로 MSA(Micro Service Architecture) 형태로 설계되어 있기 때문에 서비스나 서비스의 버전에 따라 데이터 소스(DB 등)가 다른 경우가 많습니다. 이렇게 데이터 소스에 따라 어떻게 효율적으로 데이터를 가져올지 고민하며 파이프라인을 구성해야 합니다.  
예를 들면 다음과 같은 것들을 고려해야 합니다.

- **1) 데이터 소스가 어떤 형태(RDB? NoSQL? 혹은 파일?)고 어떻게 가져오는게 가장 최적화된 방법일까?**
- **2) 한 번에 가져오는 데이터양이 너무 많아서 처리 속도 혹은 메모리에 문제는 없나? 있다면 어떻게 해결해야 할까?**
- **3) 데이터 소스가 서비스 DB인 경우, 부하를 주지 않으면서 빠르게 데이터를 가져오려면 어떻게 해야 할까?**
- **4) 서비스 DB에서 UPDATE, DELETE가 수행되어도 일관성 있게 데이터를 가져오려면 어떻게 해야 할까?**
- **5) 파이프라인을 재 실행해도 데이터를 멱등하게 수집하려면 어떻게 해야 할까?**

필요한 데이터를 가져오는게 너무 복잡하거나 공공 데이터(날씨 등) 수집이 필요한 경우 관련 코드를 직접 작성하기도 합니다. 이 경우 처리 속도, 메모리 사용량 등의 컴퓨팅 리소스와 코드의 가독성, 재사용성 등의 지속 가능한 개발을 고려하며 모듈을 개발합니다. 이러한 모듈은 팀 내 리뷰를 거쳐 메인 코드 저장소(Github)에서 관리되고 있습니다.

![](/img/what-socar-data-engineering-team-does/datalake-diagram.001.jpeg)

데이터 파이프라인은 Apache Airflow의 DAG 코드(파이썬 코드)를 작성해서 만들 수 있습니다. Airflow는 기본적으로 데이터 파이프라인 작성을 위한 [여러 클래스와 함수들(Operator)](https://airflow.apache.org/docs/apache-airflow/1.10.14/_api/airflow/operators/index.html)을 제공해줍니다. 이 클래스와 함수들을 적절히 이용하거나 직접 필요한 모듈을 작성하여 파이프라인을 구성합니다.

![](/img/what-socar-data-engineering-team-does/airflow.jpg)

단순히 데이터를 한 군데로 모으는 것을 넘어서, 사내에서 자주 사용되는 **데이터를 별도로 정제 및 집계하여 일종의 데이터 마트로 만들기도 합니다.** 쏘카의 "SODA Store"가 바로 그 대표적인 예입니다. SODA Store는 SOCAR Data Store의 약자로 쏘카의 구성원들이 복잡한 데이터를 쉽고 편하게 조회할 수 있도록 만든 데이터 마트입니다.

SODA Store가 등장하기 전에는 원하는 차량 예약 관련 데이터를 보기 위한 쿼리는 500줄이 넘곤 했습니다. 이 500줄 쿼리를 직접 짜거나 다른 팀원에게 전달받아야만 했죠. 이에 전사적으로 구성원들이 자주 조회하는 결과 테이블을 미리 만들어 두었고, 이 결과로 SODA Store가 등장하게 되었습니다. 이제 500줄이 아닌 몇 줄의 쿼리로 필요한 데이터를 빠르게 조회할 수 있습니다. 또한 쏘카 서비스 내의 필요한 데이터를 찾기 위해 힘들게 모든 테이블을 살펴보지 않고, 이 SODA Store에서 우선적으로 필요한 데이터를 찾아볼 수 있습니다. 데이터 탐색 시간은 줄어들고, 사용법도 편해졌습니다. 쏘카 직원이라면 누구나 쉽게 이 데이터 마트를 통해, 필요한 데이터를 볼 수 있습니다. 

![](/img/what-socar-data-engineering-team-does/datamart.png)

정리하면, 데이터 엔지니어링 팀은 **서비스의 다양한 데이터를 한 곳으로 모아두는 작업**을 담당합니다. 또한 더 쉽게 데이터를 조회할 수 있도록 데이터 마트를 만듭니다. 쉽게 데이터를 조회하고 분석할 수 있는 환경을 지속해서 만드는 것이 데이터 엔지니어링 팀의 미션입니다.





### 데이터 지표와 보고서 만들기

![](/img/what-socar-data-engineering-team-does/data-report-pipeline.png)

데이터 엔지니어링 팀의 두 번째 업무는 **분석 환경 구축을 넘어 실제 비즈니스 목적을 가진 분석 지표와 보고서를 만드는 일**입니다. 이 보고서들은 실제 도메인 담당자들이 의사 결정할 때 사용됩니다. "이전 달 손익 지표 보고서", "최근 일주일간 가동률 보고서" 등이 대표적인 예입니다.  

하나의 보고서는 보기 좋은 테이블이나 그래프로 보이지만, 사실 이 보고서를 만들기 위해 여러 작업이 필요합니다. 먼저 보고서를 요청한 팀, 예를 들면 운영 본부나 사업 본부와 같은 팀과 미팅을 통해 보고서의 목적을 확인하고 이와 관련된 지표를 정합니다. 이후 관련 데이터들을 전처리하는 로직을 설계합니다. 이 로직을 기반으로 기반 데이터를 만들고, 이 기반 데이터를 가공해 최종적으로  사용 가능한 데이터를 만듭니다. 마지막으로 최종 결과물인 보고서를 요청한 팀에게 전달하고 피드백을 받습니다. 이런 과정은 한번으로 끝나는 게 아니라, 지속적으로 이루어지기 때문에 관련 지표와 로직은 일관성 있고 확장 가능하도록 설계해야 합니다. 이런 경우엔 다음과 같은 내용을 고민하게 됩니다.

- **어느 정도로 태스크를 쪼개야 유지보수와 재사용이 쉬울까?**
- **쿼리는 어떻게 구조화하고 최적화해야 읽기도 좋고 성능도 좋을까?**
- **어느 시간대에 어떤 순서로 실행해야 올바르게 파이프라인이 동작할까?**

이런 기술적 고민과 함께 관련 팀과 미팅, 올바른 지표 설계, 워크플로우 설계, 최종 보고서 구현 모두 데이터 엔지니어링 팀이 진행하고 있습니다.

> *** 지표 및 보고서를 만들 때 어떤 도구를 사용하나요?**
>
> 쏘카에서는 다음과 같은 툴을 사용합니다.  
> 모두 빅쿼리와 연결이 쉽고, 보고서 업데이트 스케쥴링 기능을 지원합니다.
>
> [구글 스프레드 시트](https://www.google.com/sheets/about/) : 테이블 형태로 여러 지표 값을 한눈에 보고자 할 때 주로 사용합니다. 대부분의 쏘카 구성원들이 구글 스프레드 시트를 이용하고 있어서 접근성과 이용성 모두 좋습니다.
>
> [구글 데이터 스튜디오](https://marketingplatform.google.com/intl/ko/about/data-studio/) : 간단하게 지표를 시각화할 때 주로 사용합니다. 구글 스프레드시트보다 나은 시각화 템플릿을 제공하며 필터링 및 파라미터 관련 기능들을 구현하기 쉽습니다.
>
> [태블로](https://www.tableau.com/ko-kr) : 구글 데이터 스튜디오에서 구현하기 어려운 보고서를 만들거나 요청 팀에서 구글 데이터 스튜디오보다 태블로를 선호하는 경우 사용합니다. 태블로는 더 다양한 시각화 템플릿을 사용할 수 있고, 더 정밀한 서식 설정이 가능합니다. 다만 태블로는 구글 플랫폼에 소속된 서비스가 아니라서 앞에 두 툴에 비해 접근성 및 이용성이 상대적으로 떨어지는 편입니다.
>
> 보고서를 보는 대상이나 표현하려는 지표의 형태에 따라 툴을 선택합니다.





### 데이터 기반의 백엔드 서비스 개발 및 운영

![](/img/what-socar-data-engineering-team-does/backend.png)

데이터 그룹에서 만든 **분석 및 모델링 결과를 서비스에 반영하기 위한 백엔드 서비스 역시 데이터 엔지니어링 팀이 담당하고 있습니다.** 쏘카의 "대여 가격 시스템", "면책 요금 시스템", "차량 파손 탐지 시스템" 이 그 대표적인 예입니다. 이 시스템들은 MSA 형태로 쏘카 서비스의 필요한 모든 것을 구현하는 것이 아니라, 데이터 기반 의사결정이 필요한 도메인 로직만 분리해 처리합니다.

차량 파손을 탐지하는 머신러닝 모델을 서빙하는 백엔드 서비스인 "차량 파손 탐지 시스템"의 경우를 예를 들어보겠습니다.  
먼저 서비스를 사용하는 관련 부서와 어떤 기능이 필요한지 논의하고, 요구사항을 정리합니다. 이후 서버 개발팀과 어떤 통신 형태를 사용할지(HTTP API 형태로 주고 받을지? gRPC를 사용할지?), 인터페이스는 어떻게 할지 논의합니다. 차량 파손 탐지는 딥러닝 모델을 기반으로 이뤄지기 때문에, 모델 학습과 예측 결과에 대해 데이터 분석 팀과도 논의합니다. 이러한 논의 끝에, 백엔드 서비스를 개발하고  Kubernetes 환경에 배포합니다. 이후 지속적으로 모니터링하며 에러는 없는지, 리소스 사용량에 문제는 없는지 등을 확인합니다.
([쏘카 테크 블로그의 "딥러닝 모델 Serving 간단 구축기"](https://tech.socarcorp.kr/data/2020/03/10/ml-model-serving.html)를 보시면 이에 대해 자세히 보실 수 있습니다.)

서비스가 성장하며 계속해서 추가 기능을 개발하고 운영해야 하므로 쉬운 **유지 보수를 위한 클린 코드, 아키텍처나 CI/CD를 고민하며 백엔드 서비스를 개발합니다.** 데이터 분석 팀 혹은 비즈니스 데이터 팀의 비즈니스 로직과 요구사항이 계속해서 바뀌는 경우가 많기 때문에 늘 변화를 염두에 두고 유연한 코드를 설계하는데 관심을 두고 있습니다. 일례로 최근에 도메인 주도 설계(DDD) 패턴을 적용하여 "면책 요금 시스템"을 리팩토링 한 바 있습니다. 또한 유저에게 바로 노출되는 서비스인 만큼 처리 속도 역시 신경 써야 하며 로직 최적화 및 캐싱 기법 등에도 관심을 두며 스터디하고 가능한 대안을 적용하고 있습니다. 최종적으로는 쿠버네티스에 올라가기 때문에 리소스 사용량은 얼마나 되는지, 급격한 사용량에 대해 잘 대처할 수는 있는지를 고민하며 해당 내용을 인프라팀과 논의 후 결정하곤 합니다.

이 외에도 데이터 엔지니어링 팀은 **데이터 그룹에 도움이 되는 파이썬 모듈 개발이나 기술 문화를 도입에 힘쓰고 있습니다.** 이처럼 그룹의 전반적인 엔지니어링 스킬을 올리고 선도하는 것도 데이터 엔지니어링 팀의 역할입니다.

> *** 주로 어떤 언어, 프레임 워크, 툴로 개발 및 배포하나요?**
>
> 현재는 주로 파이썬으로 개발합니다. 
> 제한된 시간 안에 적은 인원으로 빠르게 성과물을 낼 수 있고, 팀원들이 파이썬에 더 익숙하기 때문입니다.
>
> 프레임워크는 때에 따라 다릅니다. 
> HTTP 통신 프레임워크로는 flask를 씁니다. (최근에는 FastAPI 를 사용해보려고 하고 있습니다.) RPC 통신은 gRPC를 주로 사용합니다.  
> 쏘카 시스템 전체가 MSA로 설계된 만큼, 이러한 툴 사용은 필요와 요구사항에 따라 달라집니다.   
> 프로그래밍 언어 역시 필요에 따라 바꿀 수 있다는 생각을 하고 있습니다.
>
> CI/CD 파이프라인 툴은 전사적으로 [BuddyWorks](https://app.buddy.works/) 를 사용합니다.  
> BuddyWorks 는 자유도에 제한이 있고 유료이기는 하지만 쉽고 빠르게 CI/CD 파이프라인을 구축할 수 있습니다.  
> 데이터 그룹에 한해 배포 툴은 별도로 ArgoCD 를 사용하며 Git-ops 형태로 운영하고 있습니다.



### 데이터 그룹의 인프라/리소스 관리

![](/img/what-socar-data-engineering-team-does/infra.png)

쏘카 데이터 그룹은 빅쿼리를 사용한 분석 작업이 많아서 자연스럽게 Google Cloud Platform의 managed service를 다양하게 사용하고 있습니다(PubSub, Cloud Function, Dataproc, Dataflow, Data Studio 등). GCP와 관련된 인프라 구축은 인프라팀의 도움을 받지만, 실 서비스 운영 및 관리는 데이터 엔지니어링 팀이 주로 담당하고 있습니다.

주로 다음과 같은 항목들을 다룹니다.

- 데이터 그룹원들이 사용하는 **Computing Resource, BigQuery 사용량, 데이터 정합성 검사, 그리고 네트워크 방화벽을 회사 방침에 맞게 잘 사용하는지 등을 모니터링합니다.**
    - Computing Resource, BigQuery를 과도하게 사용하는 경우, 알람 메시지가 오도록 설정해둡니다.
    - 이 알람을 통해 Computing Resource 관련 장애를 미연에 방지할 수 있습니다.
    - BigQuery 관련 사용 요금을 미리 알 수 있고 과금되는 것을 막을 수 있어, 데이터 그룹 구성원들이 비교적 마음 편하게 쿼리를 날릴 수 있습니다.
    - BigQuery에 데이터가 잘 적재되었는지 정합성 검사를 통해 주기적으로 확인할 수 있습니다.
    - 네트워크 방화벽을 모니터링하여 보안에 문제가 없는지 주기적으로 확인할 수 있습니다.
- GKE(Google Kubernetes Engine) 환경에서 **필요한 리소스 할당 및 일관된 템플릿을 제공**해주고, GKE에 배포하는 파이프라인을 설계 및 가이드합니다.
    - 배포 파이프라인을 만들거나 혹은 데이터 그룹 구성원이 직접 만들 수 있도록 가이드합니다.
    - 현재 배포는 Git-ops 형태로 관리하고 있으며, helm chart나 commit 메시지 등을 일관되게 구성하도록 관리합니다.
    - GKE에 올라가는 차트를 작성, 관리합니다. 예를 들면 Airflow와 관련된 Helm chart를 직접 커스터마이징하여 운영합니다.
    - 더 나은 개발 환경과 배포 파이프라인은 없는지 계속해서 고민하고 리서치합니다.
- 그 외 VPC 설정 및 쏘카의 개발 인프라인 AWS와의 연동 작업 관리 등을 담당합니다.



## 정리

![](/img/what-socar-data-engineering-team-does/all-in-one-diagram.png)

데이터 엔지니어링 팀의 업무를 정리하면 다음과 같습니다.

- **전사 직원을 위한 통합 데이터 저장소 마련**
    - 다양한 데이터 소스의 통합 데이터 저장소로 이동하는 파이프라인 설계
    - 좀 더 편리한 분석을 위한 데이터 마트 구축
- **타 부서를 위한 지표, 시각화 및 보고서 개발**
    - 보고서의 데이터가 지속적으로 업데이트되는 파이프라인 설계
    - 업무 규칙 협의, 지표 및 데이터 검증
- **데이터 기반의 백엔드 서비스 개발 및 운영**
    - 데이터 그룹 내 오퍼레이션, 머신러닝을 서빙하는 백엔드 서비스 개발
    - 지속적이고 유지보수가 쉬운 개발 방법론 연구 및 적용
    - 운영에 차질이 없도록 로그, 리소스 모니터링
-  **데이터 그룹 인프라 및 리소스 관리**
    - GCP 서비스 운영 및 관리
    - 리소스, 네트워크, 빅쿼리 사용량 모니터링
    - GKE에 올라가는 helm chart 및 리소스 관리
    - 개발 환경과 배포 파이프라인(CI/CD) 관리





## 앞으로 목표와 할 일들

### GKE 환경과 Airflow on GKE

쏘카 서비스가 계속해서 성장하면서, 작년 말부터 GCP에서 쿠버네티스를 사용하기 위해 GKE를 세팅 및 운영하는 작업을 지속적으로 해오고 있습니다. 데이터 파이프라인 역시 점점 파이프라인 개수가 증가하고 고성능이 요구됨에 따라, 기존의 단일 컴퓨팅 환경으로 운영되었던 Airflow를 쿠버네티스 환경으로 옮기고 있습니다. 최종적으로는 데이터 파이프라인을 포함한 모든 GCE 리소스를 GKE로 옮기고, 엔지니어링 팀에서 클러스터를 잘 운영하는 것이 목표입니다. 



### 타다 데이터 엔지니어링

올해 초부터 타다 서비스의 데이터 엔지니어링 업무를 쏘카 데이터 엔지니어링 팀이 맡게 되었습니다. 이에 따라 업무의 범위는 수평적으로 더 늘어나, 타다 서비스의 데이터 파이프라인을 개선할 계획입니다. 타다 서비스의 특성에 맞게 배치성 데이터 외에도 스트리밍 데이터를 더 다뤄보고 개발할 기회를 가지게 되었습니다.



### 그 외

그 밖에도 기존에 개발한 백엔드 서비스의 데이터가 점점 더 많아지고 있으며, 어떻게 하면 효율적으로 결과물을 서비스할지 공부하고 적용해보고 있습니다. 이렇게 운영한 경험을 바탕으로 새로운 데이터 기반의 백엔드 서비스를 만들어야 할 업무들도 이미 백로그에 존재합니다. 

더 커지는 사업 규모, 더 늘어나는 트래픽, 발전하는 머신러닝 모델링에 맞춰 데이터 엔지니어링 팀은 운영이 용이한 데이터 파이프라인, 잘 개발된 백엔드 서비스, 더 나아가 MLOps까지 커버하는 것을 목표로 하고 있습니다. 더불어, 빠른 시간 안에 필요에 따라 적절하게 엔지니어링하고 서비스에 문제없이 운영해나가는 것이 데이터 엔지니어링 팀의 주된 목표입니다.


> 쏘카 데이터 엔지니어링 팀은 채용 중입니다.  
> [쏘카(SOCAR) - 데이터 엔지니어 채용 공고 링크](http://bit.ly/socar-data-engineer-recruitement)