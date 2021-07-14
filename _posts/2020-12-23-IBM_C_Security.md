---
title: "[IBM C:louders] Secrets Manager 생성하기"
description: "IBM Clouders mission"
layout: post
toc: false
comments: true
search_exclude: true
categories: [IBM Clouders]
image: "/images/ibm_clouders.png"
---

# IBM Cloud Security

IBM Cloud의 보안은 처음 뱃지 획득을 위해 Course를 수강했을 때도 다국적기업으로 B2B를 많이 하는 IBM특성상 보안수준이 높다고 알려져있다.

점차 개인정보 및 데이터 보안에 대한 문제가 대두되고있으며  
이번 12월 뱃지 미션에서 나왔던 ReplicaSet의 Secrets를 관리해보도록 하겠습니다.

ConfigMaps and Secrets

> 컨피그맵은 키-값 쌍으로 기밀이 아닌 데이터를 저장하는 데 사용하는 API 오브젝트이다. 파드(Pod)는 볼륨에서 환경 변수, 커맨드-라인 인수 또는 구성 파일로 컨피그맵을 사용할 수 있다. \쿠버네티스 시크릿을 사용하면 비밀번호, OAuth 토큰, ssh 키와 같은 민감한 정보를 저장하고 관리할 수 ​​있다. 기밀 정보를 시크릿에 저장하는 것이 파드(Pod) 정의나 컨테이너 이미지 내에 그대로 두는 것보다 안전하고 유연하다.

## 요약

Secrets Manager를 사용하여 IBM Cloud 서비스 또는 사용자 빌드 애플리케이션에서 사용되는 시크릿을 작성하고 대여하며, 중앙에서 관리할 수 있습니다. 시크릿은 IBM Cloud에 있는 오픈 소스 HashiCorp Vault의 전용 인스턴스에 저장됩니다.

## 기능

**스케일링 시 중앙에서 시크릿 관리**
오픈 소스 HashiCorp Vault에 빌드된 전용 시크릿 저장소에서 애플리케이션 시크릿을 관리합니다. Secrets Manager는 몇 개의 시크릿만 있으면 되는 개발자, 또는 수백만 개의 시크릿을 필요로 하는 대형 엔터프라이즈의 요구에 맞게 스케일링할 수 있습니다.

**동적으로 시크릿 작성**
지원되는 IBM Cloud 오퍼링을 사용할 때 API 키, 비밀번호 및 데이터베이스 구성과 같은 시간 기반 시크릿을 작성하고 대여하는 데 도움이 되는 플랫폼 통합을 사용하여 시간을 절약합니다.

**사용자 시크릿 가져오기**
시크릿 온프레미스를 생성하기 위해 엄격한 가이드라인을 준수해야 하는 경우 Secrets Manager로 시크릿을 안전하게 가져와서 기존 시크릿 관리 인프라를 확장할 수 있습니다.

**시크릿 그룹으로 액세스 정의**
Secrets Manager는 Cloud IAM(Identity and Access Management)과 통합되어 보안 관리자가 시크릿 그룹을 사용하여 시크릿을 구성하고 시크릿에 대한 액세스 권한을 부여할 수 있습니다.

**스토리지에서 시크릿 보호**
IBM Key Protect를 사용하여 저장 시 시크릿 보안을 향상시킵니다. Key Protect 암호화 키를 Secrets Manager 서비스 인스턴스의 신뢰 루트로 선택하면 시크릿에 대한 고급 고객 관리 암호화를 사용할 수 있습니다

## 계정 업그레이드 실패..

계정을 업그레이드 해야 이 서비스를 이용할 수 있다는 것을 알게되었습니다.  
하지만 아직 신용카드도 없을뿐더러 Mastercard가 찍힌 체크카드로는 계정을 업그레이드 할 수 없어 자세한 실습을해보지 못했습니다.
