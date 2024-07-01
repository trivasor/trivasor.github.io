---
title: "2024년 Certified Kubernetes AdministratorCKA 시험 합격 후기 및 꿀팁"
description: ""
coverImage: "/assets/img/2024-07-01-HowIPassedtheCertifiedKubernetesAdministratorCKAExam2024_0.png"
date: 2024-07-01 21:06
ogImage: 
  url: /assets/img/2024-07-01-HowIPassedtheCertifiedKubernetesAdministratorCKAExam2024_0.png
tag: Tech
originalTitle: "How I Passed the Certified Kubernetes Administrator (CKA) Exam — 2024"
link: "https://medium.com/@artiodev/how-i-passed-the-certified-kubernetes-administrator-cka-exam-2024-60da4f7552b3"
---


자격증 Kubernetes Administrator (CKA) 취득을 위한 여정이 시작되면 도전적일 수 있습니다. 
저는 Certified Kubernetes Administrator (CKA) 시험을 준비하고 통과하는 과정에서의 경험과 통찰을 여러분과 공유하고 싶습니다.

CKA 프로그램은 Cloud Native Computing Foundation (CNCF)과 리눅스 재단과의 협력으로 만들어졌으며 Kubernetes 생태계를 발전시키기 위한 목적을 가지고 있습니다. CKA 프로그램의 목적은 CKAs가 Kubernetes 관리자의 업무를 수행하기 위한 기술, 지식 및 능력을 확보하는 것입니다.

이 프로그램은 온라인으로 시행되며 프로터가 감독을 하는 성능 중심 시험으로, 여러 문제를 명령줄에서 해결해야 합니다:

질문 수: 17
시간: 2시간
운영 체제: Ubuntu

<div class="content-ad"></div>

저의 여행 준비 과정에서 가장 중요한 단계는 KodeKloud와 Mumshad Mannambeth가 Udemy에서 제공하는 Certified Kubernetes Administrator (CKA) with Practice Tests 코스에 등록하는 것이었습니다.

해당 포괄적인 코스는 Kubernetes의 모든 측면을 자세히 다루며 각 주제 이후에 실습 실습을 제공합니다. 이 코스는 약 18시간이 소요됩니다. 제안된 모든 실습 테스트를 꼭 수행하도록 하세요.

KodeKloud 코스를 따라가며, 모든 라이트닝 랩과 Mumshad가 제시한 최종 2개의 모의 시험을 완료했습니다. 추가로 KodeCloud에서 제공하는 다른 모의 시험을 권하고 싶습니다: [KodeCloud 링크](https://kodekloud.com/courses/ultimate-certified-kubernetes-administrator-cka-mock-exam/).

그리고 https://killercoda.com/cka에서 제공되는 연습문제를 활용하여 매일 다른 주제를 연습해보세요.

준비과정으로, 시험을 예약하기 며느마자 몇 일 전에 killer.sh 시험 시뮬레이션에 도전했습니다. 이 무료 시뮬레이션은 공식 시험 등록 시 제공되며 실제 CKA 시험보다 더 어려운 경험을 제공합니다. 해당 시나리오는 25문제로 구성되어 있으니 중간 성적을 얻으려고 노력해 보세요.

<div class="content-ad"></div>

CKA 시험의 독특한 측면 중 하나는 공식 Kubernetes 설명서를 사용할 수 있다는 점입니다.
명령으로 생성할 수 없는 모든 리소스를 쉽게 찾아야 합니다. 다음과 같은 객체와 주제가 그 예시입니다:

- Persistent Volume과 Persistent Volume Claims https://kubernetes.io/docs/concepts/storage/persistent-volumes/
- Kubeadm 업그레이드 https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
- Kubeadm 설치 https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
- 환경 변수 설정 https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/

팁: Kubernetes 문서 페이지에 있을 때 ctrl+f를 눌러 kind: *object를 검색하면 리소스를 쉽게 찾을 수 있습니다 (예: kind: pvc).

시험 문제가 무엇을 요구하는지 알아야 하고, 그에 맞는 자료 (예: YAML 파일 템플릿)를 설명서에서 찾아야 합니다. 이것은 실습으로만 가능합니다!

<div class="content-ad"></div>

이 자격증은 두 년 동안 유효하며, 시험을 성공적으로 완료한 후 24/36시간 내로 PDF 자격증과 디지털 뱃지를 받게 됩니다.
리눅스 재단은 12개월의 시험 응시 기간을 제공하여 시험 일정을 편리하게 예약할 수 있게 합니다. 또한 필요한 경우 시험 한 번을 다시 응시할 수 있습니다.

여기 제 자격증이 있어요:

![Certification](/assets/img/2024-07-01-HowIPassedtheCertifiedKubernetesAdministratorCKAExam2024_0.png)

행운을 빕니다!

<div class="content-ad"></div>

링크드인에서 연락해주세요.