---
title: "End-to-End CI CD 파이프라인 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_0.png"
date: 2024-07-01 00:17
ogImage: 
  url: /assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_0.png
tag: Tech
originalTitle: "End-to-End CI CD Pipeline Implementation"
link: "https://medium.com/devops-dev/building-an-end-to-end-ci-cd-pipeline-with-jenkins-7ef2205d7988"
---



![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_0.png)

# 소개

현대 소프트웨어 개발에서는 지속적 통합과 지속적 배포(CI/CD)가 중요합니다. 이는 자동화된 코드 통합과 신뢰할 수 있는 애플리케이션 전달을 용이하게 합니다.

Jenkins는 유연성과 다양한 플러그인 옵션으로 유명하여, CI/CD 파이프라인을 만드는 데 선도하는 도구입니다.


<div class="content-ad"></div>

이 블로그 포스트에서는 Jenkins를 사용한 CI/CD 파이프라인의 전체 설정을 안내합니다. 젠킨스 구성부터 버전 관리 시스템과 통합, 빌드, 테스트, 배포 오케스트레이션까지 모두 다룰 예정입니다. 소프트웨어 전달 프로세스를 개선하는 데 도움이 되도록 제작되었습니다.

# 사용된 도구 및 기술

이 안내서에서는 다음과 같은 다양한 기술과 도구를 활용할 예정입니다:

- 버전 관리를 위한 GitHub
- 프로젝트 관리 및 빌드를 위한 Maven
- 코드 품질 분석을 위한 SonarQube
- 컨테이너화를 위한 Docker
- Continuous Integration을 위한 Jenkins
- 쿠버네티스 배포 관리를 위한 ArgoCD 및 Helm
- 컨테이너 오케스트레이션을 위한 쿠버네티스

<div class="content-ad"></div>

LinkedIn에서 연락할 것이 있으면 언제든지 연락해주세요.

# Git 설정하기

자바 애플리케이션의 Jenkins 파이프라인을 위한 기반을 설정하기 위해 Git을 구성하는 것부터 시작해보겠습니다:

- 개인 Git 저장소 만들기: 

<div class="content-ad"></div>

- 원하는 Git 호스팅 플랫폼(예: GitHub, GitLab)을 방문해주세요.
- 계정에 로그인하거나 계정이 없다면 가입해주세요.
- 새 저장소를 만들어주세요. 코드를 보호하기 위해 가시성을 비공개로 설정하는 것을 잊지 마세요.

2. 개인 액세스 토큰 생성하기:

- 계정 설정으로 이동해주세요. 프로필 아래나 드롭다운 메뉴에서 찾을 수 있습니다.
- "개발자 설정" 또는 "개인 액세스 토큰" 라벨이 있는 섹션을 찾아주세요.
- 새 토큰을 생성하고 'repo'와 같은 필요한 권한을 할당해주세요.
- 이 토큰을 안전하게 복사하고 보관해주세요. Jenkins 파이프라인에서 액세스를 구성하는 데 나중에 필요할 것입니다.

3. 저장소를 로컬로 복제하기:

<div class="content-ad"></div>

- 여기서 소스 코드를 찾으세요.
- Git Bash나 터미널을 열어주세요.
- 저장소를 복제하고 싶은 디렉토리로 이동해주세요.
- 다음 명령을 실행해주세요. `<URL>` 부분은 귀하의 저장소 URL로 교체해주세요:

```js
git clone <URL>
```

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_1.png)

# EC2 인스턴스 생성하기

<div class="content-ad"></div>

지금 우리는 테라폼을 사용하여 자동화할 수도 있지만, 이해를 돕기 위해 이 과정을 수동으로 진행하겠습니다.

1. AWS 콘솔에 로그인합니다:
    - AWS 관리 콘솔에 접속하세요.
    - AWS 계정 자격 증명을 사용하여 로그인하세요. 계정이 없는 경우 새로 생성해야 합니다.

2. EC2 대시보드로 이동합니다:

<div class="content-ad"></div>

- 콘솔 상단에 있는 "Services" 메뉴를 찾아주세요.
- "Compute" 섹션 아래에 있는 "EC2"를 클릭하여 EC2 대시보드로 이동해주세요.

3. 인스턴스 시작:

- "인스턴스 시작" 버튼을 클릭해주세요. 이로써 새로운 EC2 인스턴스를 생성하는 프로세스가 시작됩니다.

4. 태그 추가:

<div class="content-ad"></div>

5. 이미지 태그와 이름을 추가하여 인스턴스를 더 잘 정리하고 관리합니다.

    - AMI 선택:

    - 사전 구성된 서버 템플릿인 AMI 목록이 표시됩니다. 이것들은 다른 운영 체제와 설정을 포함할 수 있습니다.
    
    - 요구 사항에 맞는 AMI를 선택하십시오. 초보자에게는 Amazon Linux AMI나 기본 Ubuntu Server가 가장 쉬운 옵션이 될 수 있습니다.

6. 인스턴스 유형 선택:

<div class="content-ad"></div>

- 필요에 맞는 인스턴스 유형을 선택하세요.
- 기본 옵션(일반적으로 t2.micro 인스턴스)은 테스트와 소규모 워크로드에 적합하며 무료 티어 대상입니다.
- 저는 t2.large 인스턴스를 선택했는데, 2개의 vCPU와 8 GiB의 메모리를 제공하여 중간 규모의 워크로드를 처리하기에 적합합니다.
- 이 인스턴스 유형을 활용하면 요금이 발생하니, 예산을 효과적으로 관리하기 위해 AWS의 최신 요금 정보를 확인하는 것이 중요합니다.

7. 키페어 생성:

- 키페어를 생성하거나 기존 것을 사용하세요. 이 키페어는 인스턴스에 대한 SSH 액세스에 중요합니다.
- 개인 키 파일(.pem 파일)을 다운로드하고 안전한 곳에 보관하세요. 생성된 후에는 다시 다운로드할 수 없습니다.

8. 인스턴스 세부정보 구성:

<div class="content-ad"></div>

9. 보안 그룹 구성:

- 보안 그룹은 인스턴스로부터 허용되는 및 송수신되는 트래픽을 제어하는 가상 방화벽 역할을 합니다.
- 새로운 보안 그룹을 생성하거나 이미 있는 것을 선택할 수 있습니다. SSH (포트 22), 사용자 정의 TCP (8080) 및 사용자 정의 TCP (9000)와 같은 인바운드 포트를 열어두었는지 확인해주세요.

10. 스토리지 추가:

<div class="content-ad"></div>

- 루트 볼륨의 크기를 지정합니다 (일반적으로 기본 설정이 테스트 목적으로 적합합니다).

11. 검토 및 시작:

- 인스턴스 설정을 확인하세요. AMI 세부 정보, 인스턴스 유형, 보안 그룹 및 키페어를 확인합니다.
- 계속하려면 "시작"을 클릭하세요.

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_2.png)

<div class="content-ad"></div>

# 인스턴스에 액세스하기

인스턴스가 시작되면 초기화하는 데 몇 분이 걸릴 것입니다.

그런 다음 다운로드한 .pem 파일을 사용하여 SSH를 통해 인스턴스에 연결할 수 있습니다.

Windows 기기에서 EC2 인스턴스에 SSH하기 위해 Mobaxterm을 사용하고 있습니다.

<div class="content-ad"></div>

- MobaXterm을 다운로드하고 설치하세요.
- 데스크톱이나 시작 메뉴에서 MobaXterm을 엽니다.
- "세션" 버튼을 클릭한 후 "SSH"를 선택하세요.
- 서버의 IP 주소 또는 호스트 이름을 입력하고 사용자 이름을 지정하세요.
- "고급 SSH 설정"을 클릭한 후 "개인 키 사용"란을 체크하고 인스턴스에서 사용한 개인 키 쌍을 선택하세요.
- "확인"을 클릭하여 연결하세요.

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_3.png)

# Jenkins 설정하기

Java 설치하기:

<div class="content-ad"></div>

제단을 실행하기 전에 서버에 Java를 설치해야 합니다. Jenkins는 OpenJDK와 Oracle Java 둘 다 호환되지만, 일반적으로 OpenJDK에서 최적의 성능을 발휘합니다.

SSH로 접속한 인스턴스에 Java를 설치하는 방법은 다음과 같습니다:

```js
sudo apt update
sudo apt install openjdk-11-jdk
java -version
```

제단을 설치하세요:

<div class="content-ad"></div>

이제 서버에 Java가 설치되어 있고 준비가 되었으므로 다음 단계는 Jenkins를 설치하는 것입니다. 설치를 진행하는 두 가지 옵션이 있습니다: 스크립트 사용 또는 수동 명령 실행.

스크립트 사용은 효율적이고 재현 가능하며 Jenkins를 여러 번 배포하거나 다른 환경에서 일관된 설정을 유지하려는 경우에 특히 좋습니다.

- 'vim' 또는 원하는 기타 편집기를 사용하여 스크립트 파일을 생성합니다.

```bash
vim install_jenkins.sh
```

<div class="content-ad"></div>

- i키를 눌러 삽입 모드로 전환한 후 스크립트를 작성하세요.

```bash
#!/bin/bash
# Jenkins GPG 키 다운로드
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# 패키지 관리자 소스에 Jenkins 저장소 추가
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# 패키지 관리자 저장소 업데이트
sudo apt-get update

# Jenkins 설치
sudo apt-get install jenkins -y
```

- :wq를 입력한 후 Enter를 눌러 변경 사항을 파일에 저장하고 에디터를 종료합니다.
- 다음 명령을 사용하여 파일을 실행 가능하게 만드세요:

```bash
chmod +x install_jenkins.sh 
```

<div class="content-ad"></div>

- 스크립트를 실행할 수 있게 되었으니, Jenkins를 설치하기 위해 스크립트를 실행할 차례입니다:

```bash
./install_jenkins.sh
```

방화벽 설정 조정:

웹 브라우저를 통해 Jenkins에 접속할 수 있도록 하려면, 방화벽 설정을 제대로 구성하는 것이 중요합니다.

<div class="content-ad"></div>

젠킨스는 기본적으로 8080 포트에서 실행됩니다. 따라서 보안 그룹 설정에서 이 포트로의 인바운드 트래픽을 허용해야 합니다.

젠킨스 UI에 액세스하기:

젠킨스 UI에 접속하려면 웹 브라우저를 열고 인스턴스의 IP 주소에 :8080 을 추가하여 입력하세요 (예: http://192.168.1.2:8080). 192.168.1.2를 실제 서버의 IP 주소로 대체하세요.

초기 관리자 암호를 사용하여 젠킨스를 잠금 해제하세요. 암호는 다음 위치에서 찾을 수 있습니다:

<div class="content-ad"></div>

```js
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_4.png)

추천 플러그인 설치하기:

- Jenkins를 잠금 해제하면 추천 플러그인을 설치하거나 특정 플러그인을 수동으로 선택할 수 있는 옵션이 제공됩니다.
- 대부분의 사용자들에게 권장되는 표준 플러그인 세트를 자동으로 설치하려면 '추천 플러그인 설치'를 클릭해주세요.

<div class="content-ad"></div>

마스터 환경 변수

- 마스터가 시작될 때 특정 환경 변수 설정이 필요한 경우 환경 변수를 추가할 수 있습니다.
- 'Manage Jenkins'에서 'Configure System'에 들어가서 'Global properties' 섹션에서 'Environment variables'를 설정합니다.

<div class="content-ad"></div>

제이킨스 설정 완료:

- 제이킨스는 제이킨스 인스턴스의 URL을 구성하도록 요청할 것입니다.
- 기본 URL(서버 IP 및 포트에 기반)이 자동으로 입력됩니다.
- 설정을 완료하고 제이킨스 대시보드로 이동하려면 "제이킨스 사용 시작"을 클릭하세요.

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_7.png)

필요한 플러그인 설치하기:

<div class="content-ad"></div>

- Jenkins 대시보드 열기: Jenkins 인터페이스에 로그인하세요.
- “관리 Jenkins > 플러그인”으로 이동하세요.
- 플러그인 설치: “docker pipeline” 및 “sonarqube scanner” 플러그인을 찾아 설치하고 필요한 경우 Jenkins를 재시작하세요.

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_8.png)

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_9.png)

# 컴파일 작업

<div class="content-ad"></div>

- Jenkins 메인 대시보드에서 "새 항목"을 클릭합니다.
- 파이프라인의 이름을 지정하고 프로젝트 유형으로 '파이프라인'을 선택한 후 '확인'을 클릭합니다.

![pipeline](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_10.png)

파이프라인 구성:

- 생성된 작업을 클릭하고 구성 화면에서 "파이프라인" 섹션으로 스크롤합니다.
- "파이프라인 스크립트" 또는 "SCM에서 파이프라인 스크립트"를 선택합니다.

<div class="content-ad"></div>

첫 번째 옵션은 Jenkins 인터페이스에서 Groovy 스크립트를 직접 작성하는 것이고, 두 번째 옵션은 스크립트를 Git과 같은 소스 코드 관리 시스템에서 가져오는 것입니다.

- "SCM에서 파이프라인 스크립트": SCM 유형을 선택하세요 (예: Git).
- Jenkinsfile이 포함된 저장소의 URL을 입력하세요.
- 저장소가 비공개인 경우 자격 증명을 추가하세요.
- 빌드할 브랜치를 지정하세요. 일반적으로 */main 또는 */master입니다.
- Jenkinsfile이 서브디렉토리에 있거나 다르게 명명된 경우 경로를 지정하세요 (기본값은 Jenkinsfile).

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_11.png)

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_12.png)

<div class="content-ad"></div>

Jenkins 재시작하기:

- 구성 변경이나 업데이트를 효과적으로 적용하기 위해 Jenkins를 재시작하세요.
- 이를 위해 Jenkins '대시보드'로 이동하고 사이드바에서 'Jenkins 관리'를 클릭하세요.
- 거기서 "디스크에서 구성 다시로드" 또는 "안전하게 재시작"을 선택하세요.

![Jenkins 재시작](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_13.png)

# Sonarqube 서버 설정하기

<div class="content-ad"></div>

Docker 컨테이너로 SonarQube를 설치하는 것은 설정 프로세스를 간단히 하고 관리 및 확장을 쉽게 만드는 인기 있는 옵션입니다.

설치 전 요구 사항: 서버에 Docker가 설치되어 있는지 확인해주세요. 만약 설치되어 있지 않다면 공식 Docker 웹사이트에서 Docker를 다운로드하고 설치할 수 있습니다.

Docker 설치:

- 'vim' 또는 사용하고자 하는 다른 편집기를 사용하여 스크립트 파일을 만들어주세요.

<div class="content-ad"></div>

```js
install_docker.sh
```

- 삽입 모드에 들어가기 위해 i를 누르세요. 그리고 스크립트를 작성하세요.

```bash
#!/bin/bash

# 패키지 관리자 저장소 업데이트
sudo apt-get update

# 필요한 종속성 설치
sudo apt-get install -y ca-certificates curl

# Docker GPG 키를 위한 디렉토리 생성
sudo install -m 0755 -d /etc/apt/keyrings

# Docker의 GPG 키 다운로드
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

# 키에 적절한 권한 부여
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Docker 저장소를 Apt 소스에 추가
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 패키지 관리자 저장소 업데이트
sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin 
```

- 정상 모드로 변경하시려면 Esc를 눌러서 normal 모드로 전환한 후 :wq를 입력한 후 Enter 키를 눌러주세요.
- 파일을 실행 가능하게 만드는 명령어를 사용하세요.

<div class="content-ad"></div>

```js
chmod +x install_docker.sh
```

이제 스크립트가 실행 가능하니 Docker를 설치하려면 다음과 같이 실행하면 됩니다:

```js
./install_docker.sh
```

Sonarqube 설치하라:


<div class="content-ad"></div>

- 도커 허브에서 공식 SonarQube 도커 이미지를 가져옵시다:

```js
docker pull sonarqube
```

- 다음 명령어를 사용하여 도커 컨테이너에서 SonarQube를 실행해보세요.

```js
docker run -d --name sonarqube -p 9000:9000 sonarqube
```

<div class="content-ad"></div>

SonarQube는 기본적으로 9000번 포트에서 작동합니다. SonarQube 대시보드에 원활하게 접속하기 위해 방화벽을 구성하여이 포트의 들어오는 트래픽을 허용하는 것이 중요합니다.

브라우저에서 http://`your_instance_ip`:9000을 입력하여 SonarQube에 액세스하십시오. `your_instance_ip`를 서버의 IP 주소로 대체하십시오.

SonarQube의 기본 로그인 자격 증명은 다음과 같습니다:

- 사용자 이름: admin
- 비밀번호: admin

<div class="content-ad"></div>

하지만 보안상의 이유로 초기 설정 후에는 이러한 기본 자격 증명을 변경하는 것이 좋습니다.

![image](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_14.png)

Jenkins와 통합하기:
- Jenkins 플러그인을 위한 SonarQube Scanner를 설치합니다.
- SonarQube 대시보드에 로그인합니다.
- "내 계정"으로 이동하여 "보안"을 클릭하고 "토큰 생성"을 클릭합니다.
- 토큰에 이름을 지정하고 "생성"을 클릭합니다.
- 생성된 토큰을 복사합니다.

<div class="content-ad"></div>

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_15.png)

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_16.png)

Jenkins에 SonarQube 토큰을 자격 인증으로 추가하는 방법:

- Jenkins에서 “Jenkins 관리” > “자격 증명” > “시스템” > “전역 자격 증명”(또는 프로젝트 자격 증명으로 이동).
- “자격 증명 추가”를 클릭합니다.
- 자격 증명의 종류로 “비밀 텍스트”를 선택합니다.
- SonarQube 인증 토큰을 “비밀” 필드에 붙여 넣습니다.
- 자격 증명의 ID와 설명을 선택적으로 제공합니다.
- 자격 증명을 저장하려면 “생성”을 클릭합니다.

<div class="content-ad"></div>

이미지태그를 Markdown 형식으로 바꿔줍시다.

![이미지 이름](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_17.png)

![이미지 이름](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_18.png)

Jenkins SonarQube Scanner 설정:

- Jenkins 작업 구성에서 SonarQube 분석 섹션 또는 해당 섹션 이름을 찾아보세요.
- SonarQube 서버 URL을 제공합니다(e.g.,http://`your_instance_ip`:9000, `your_instance_ip`을 서버의 IP 주소로 대체하세요).
- 이전에 추가한 SonarQube 토큰을 인증 토큰으로 사용하세요.

<div class="content-ad"></div>

# 자격증

CI/CD 파이프라인에 필요한 모든 자격증이 올바르게 구성되어 있는지 확인하세요.

SonarQube 인증, Docker Hub 접근 및 Git 저장소 인증을 위한 자격증이 포함됩니다.

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_19.png)

<div class="content-ad"></div>

# Jenkinsfile

Jenkinsfile은 Jenkins 파이프라인의 구성을 정의하는 텍스트 파일입니다. 이는 Java 플랫폼을 위한 스크립팅 언어 인 Groovy로 작성됩니다.

Jenkinsfile은 파이프라인 작업을 실행할 때 Jenkins가 실행해야하는 단계, 스테이지 및 작업을 지정합니다.

Jenkins 파일의 일부는 자격 증명 및 세부 정보로 교체해야 할 부분이 있으니 주의해야 합니다.

<div class="content-ad"></div>

Pipeline Stages:

Stage 1: Git에서 소스 코드를 확인합니다.

Stage 2: Maven을 사용하여 Java 애플리케이션을 빌드합니다.

Stage 3: JUnit과 Mockito를 사용하여 단위 테스트를 실행합니다.

<div class="content-ad"></div>

## Stage 4: 코드 품질을 확인하기 위해 SonarQube 분석 실행

## Stage 5: 어플리케이션을 JAR 파일로 패키징

## Stage 6: Helm을 사용하여 어플리케이션을 테스트 환경에 배포

## Stage 7: 배포된 어플리케이션에서 사용자 수락 테스트 실행

<div class="content-ad"></div>

**Stage 8: Helm을 사용하여 애플리케이션을 프로덕션 환경으로 프로모션하기**

```js
파이프라인 {
  에이전트 {
    도커 {
      이미지 'abhishekf5/maven-abhishek-docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // 호스트의 도커 데몬에 액세스하기 위해 도커 소켓을 마운트
    }
  }
  stages {
    stage('Checkout') {
      steps {
        sh 'echo passed'
        //git branch: 'main', url: 'https://github.com/wangoimwangi/jenkins-CICD.git'
      }
    }
    stage('Build and Test') {
      steps {
        sh 'ls -ltr'
        // 프로젝트 빌드하고 JAR 파일 생성
        sh 'cd spring-boot-app && mvn clean package'
      }
    }
    stage('정적 코드 분석') {
      environment {
        SONAR_URL = "http://54.252.140.131:9000"
      }
      steps {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'cd spring-boot-app && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }
    stage('Docker 이미지 빌드 및 푸시') {
      environment {
        DOCKER_IMAGE = "ultimate-cicd:${BUILD_NUMBER}"
        // DOCKERFILE_LOCATION = "spring-boot-app/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
            sh 'cd spring-boot-app && docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                dockerImage.push()
            }
        }
      }
    }
    stage('배포 파일 업데이트') {
        environment {
            GIT_REPO_NAME = "jenkins-CICD"
            GIT_USER_NAME = "wangoimwangi"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git config user.email "mariakoi800@gmail.com"
                    git config user.name "Maria"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" spring-boot-app-manifests/deployment.yml
                    git add spring-boot-app-manifests/deployment.yml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push @github.com/${GIT_USER_NAME}/${GIT_REPO_NAME">https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
            }
        }
    }
  }
}
```

- "지금 빌드"를 클릭하여 파이프라인 작업을 트리거합니다.
- Jenkins는 귀하의 저장소에서 Jenkinsfile을 가져와 정의된대로 실행합니다.
- Jenkins 대시보드에서 파이프라인 작업 진행 상황을 확인할 수 있습니다.
- 각 파이프라인 단계가 실행될 때마다 자세한 로그와 상태 업데이트를 보려면 작업을 클릭하십시오.
- 파이프라인 실행 중 문제가 발생하면 Jenkinsfile 및 작업 구성을 검토하여 오류를 확인하십시오.
- 문제가 발생했을 경우 콘솔 출력과 로그를 확인하여 실패에 대한 자세한 내용을 확인하십시오.

![End-to-End CI/CD 파이프라인 구현](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_20.png)

<div class="content-ad"></div>

SonarQube는 파이프라인 실행 보고서를 포함하게 됩니다.

![Pipeline Report](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_21.png)

# ArgoCD 설정

ArgoCD는 CI/CD 파이프라인의 지속적 배포 세그먼트를 관리하며, Kubernetes로의 배포를 자동화합니다.

<div class="content-ad"></div>

로컬 배포는 Minikube를 사용하거나 Amazon EKS를 활용한 클라우드 배포를 선택할 수 있어요. 

준비물:
- Minikube에서 필요로 하는 가상화를 위해 Windows 컴퓨터에 VirtualBox 또는 Hyper-V가 설치되어 있는지 확인해주세요.

Minikube 설치하는 방법은 다음과 같아요:

<div class="content-ad"></div>

- Minikube을 다운로드하고 설치하세요. 각 OS에 맞는 설명은 Minikube 공식 문서를 참고해주세요.
- 로컬 Kubernetes 클러스터를 시작하세요.

```bash
minikube start
```

Kubectl 설치 방법:

- 공식 Kubernetes 릴리스 페이지에서 최신 버전의 kubectl을 다운로드하세요.
- kubectl을 PATH에 추가하여 명령 프롬프트에서 어디서든 실행할 수 있도록 해주세요.

<div class="content-ad"></div>

# ArgoCD 오퍼레이터 설치하기

쿠버네티스에 Argo CD를 설치하려면 Argo CD 오퍼레이터를 사용할 수 있습니다. 이 오퍼레이터는 Argo CD 인스턴스의 배포와 관리를 자동화합니다.

- [공식 Operator Hub 페이지](https://operatorhub.io)로 이동합니다.
- Operator Hub 웹사이트의 검색 바를 사용하여 "Argo CD"를 검색하고 "설치"를 클릭합니다.
- 다음 명령어를 실행하세요:

```shell
# Operator Lifecycle Manager (OLM)을 설치합니다. OLM은 클러스터에서 실행 중인 Operator를 관리하는 데 도움이 되는 도구입니다.

$ curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.27.0/install.sh | bash -s v0.27.0
```

<div class="content-ad"></div>

```shell
# AgroCD Operator 설치하기
kubectl create -f https://operatorhub.io/install/argocd-operator.yaml
```

- 이 Operator는 “operators” 네임스페이스에 설치되며, 클러스터의 모든 네임스페이스에서 사용할 수 있습니다.

```shell
# Operator 구동 상태 확인하기
$ kubectl get csv -n operators
```

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_22.png)

<div class="content-ad"></div>


![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_23.png)

# ArgoCD 컨트롤러 설정하기

- OperatorHub.io로 이동합니다.
- "Argo CD" Operator에서 "Operator Documentation"으로 스크롤합니다.
- "Usage"를 클릭한 후 "Basics"를 클릭합니다.
- 제공된 YAML 구성을 복사합니다. 이 YAML은 Kubernetes 클러스터에 Argo CD를 배포하는 데 사용됩니다.

![이미지](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_24.png)


<div class="content-ad"></div>

![image](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_25.png)

- Argo CD 인스턴스를 정의하는 다음 내용을 포함하여 vim argocd-basic.yml이라는 새 파일을 생성하세요:

```js
apiVersion: argoproj.io/v1alpha1
kind: ArgoCD
metadata:
  name: example-argocd
  labels:
    example: basic
spec: {}
```

- 구성을 적용하세요.

<div class="content-ad"></div>

```bash
kubectl apply -f argocd-basic.yml
```

![ArgoCD UI](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_26.png)

# ArgoCD UI 설정

- 브라우저를 통해 Argo CD 서버 UI에 접속하려면 서비스 유형을 'ClusterIP'에서 'NodePort'로 변경해야 합니다.

<div class="content-ad"></div>

```js
kubectl get svc
```

- Minikube를 통해 브라우저를 통해 Argo CD 서버에 직접 액세스할 수 있는 URL을 생성할 수 있습니다.

```js
minikube service argocd-server --url
```

![2024-07-01-End-to-EndCICDPipelineImplementation_27.png](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_27.png)

<div class="content-ad"></div>

- 이전 명령어에서 표시된 URL을 복사해서 브라우저에 붙여넣어 Argo CD UI에 액세스하세요.

![Argo CD UI](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_28.png)

- 기본 사용자 이름은 'admin'입니다. 관리자 암호를 얻으려면 Kubernetes 시크릿에서 추출해야 합니다:

```js
kubectl get secret
```

<div class="content-ad"></div>

- "example-argocd-cluster" 시크릿을 편집하고 어드민 비밀번호를 복사해주세요.

```bash
kubectl edit secret example-argocd-cluster
```

- K8s 시크릿은 base64로 암호화되어 있으니 디코딩하려면 이 커맨드를 사용하세요.

```bash
echo <인코딩된 비밀번호 입력>= | base64 -d
```

<div class="content-ad"></div>

- "admin" 사용자 이름과 이전 단계에서 가져온 비밀번호를 사용하여 Argo CD UI에 로그인하세요.

![image](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_29.png)

# Argo CD로 배포하기

- Argo CD UI에서 "Create Application"을 클릭하세요.
- 애플리케이션에 필요한 정보를 입력하세요.

<div class="content-ad"></div>

애플리케이션 이름: 애플리케이션에 대한 설명적인 이름을 입력해주세요.

프로젝트 이름: 애플리케이션이 속한 프로젝트를 지정해주세요.

동기화: 자동 동기화를 위해 "자동"을 선택해주세요.

저장소 URL: 애플리케이션 코드를 포함한 Git 저장소의 URL을 입력해주세요.

<div class="content-ad"></div>

Path: 레포지토리 내 배포 파일 경로를 지정합니다.

Destination: 쿠버네티스 클러스터의 URL을 입력합니다 (예: https://kubernetes.default.svc).

Namespace: 애플리케이션이 배포될 쿠버네티스 네임스페이스를 지정합니다.

- 필요한 모든 정보를 제공한 후, "생성"을 클릭합니다.
- 제공된 구성에 따라 Argo CD가 자동으로 쿠버네티스 클러스터에 애플리케이션을 생성합니다.

<div class="content-ad"></div>

![이미지1](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_30.png)

![이미지2](/assets/img/2024-07-01-End-to-EndCICDPipelineImplementation_31.png)

# 결론

이 프로젝트는 GitHub, Maven, SonarQube, Docker, Jenkins, Argo CD, Helm, 그리고 Kubernetes를 CI/CD 파이프라인에 통합하여 소프트웨어 개발의 효율성과 신뢰성을 향상시킬 수 있다는 것을 효과적으로 시연하였습니다.

<div class="content-ad"></div>

저희는 프로세스를 개선하여 배송 속도를 높이고 소프트웨어 품질을 향상시켰습니다.

앞으로도 파이프라인을 계속 개선하고 자동화를 강화하기 위해 새로운 도구를 탐색할 것입니다.

감사의 말씀

이 프로젝트는 우수한 데브옵스 컨텐츠로 유명한 Abhishek Veeramalla의 영감을 많이 받았습니다. 그의 컨텐츠가 나의 발전을 위한 견고한 기반이 되었습니다. 그의 컨텐츠는 여기에서 확인해보세요.

<div class="content-ad"></div>

# 함께 소통해요

더 많은 정보와 향후 프로젝트에 대해 더 알고 싶다면, LinkedIn, GitHub, 그리고 Medium에서 저와 함께 소통해주세요. 🌏✈️🗺️