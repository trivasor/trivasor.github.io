---
title: "도커 초보자부터 전문가까지 튜토리얼"
description: ""
coverImage: "/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_0.png"
date: 2024-07-01 20:56
ogImage: 
  url: /assets/img/2024-07-01-DockerBeginnertoExpertTutorial_0.png
tag: Tech
originalTitle: "Docker Beginner to Expert Tutorial"
link: "https://medium.com/gitconnected/docker-beginner-to-expert-tutorial-68555aa3e544"
---


## 친절한 설명과 예제로 도커를 기초부터 전문가까지 배워보세요!

![도커 강좌](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_0.png)

도커는 개발, 배포, 실행 방식을 혁신적으로 바꿔놓았습니다. 이를 통해 개발자들은 응용 프로그램을 컨테이너로 패키징할 수 있게 되었습니다. 이 컨테이너는 응용 소스 코드와 운영 체제(OS) 라이브러리 및 해당 코드를 어느 환경에서든 실행할 수 있는 데 필요한 종속성을 결합한 표준화된 실행 가능한 구성 요소입니다. 이 글은 도커의 주요 개념, 설치, 명령어, 이미지 작성, 컨테이너 관리, 네트워킹, 데이터 지속성, 그리고 기타 몇 가지 고급 주제를 가르쳐 줄 것입니다.

그럼 바로 시작해봅시다. 하지만 도커에 대해 배우기 전에 우선 컨테이너/컨테이너화에 대해 배워야 합니다.

<div class="content-ad"></div>

# 컨테이너 및 컨테이너화

## 컨테이너란?

컨테이너는 코드, 런타임, 시스템 도구, 라이브러리 및 설정을 포함한 소프트웨어 패키지로, 소프트웨어를 실행하는 데 필요한 모든 것을 포함하는 가벼운, 독립적이며 실행 가능한 소프트웨어 패키지입니다. 컨테이너는 소프트웨어를 환경으로부터 격리시키고 개발과 스테이징 사이의 차이와 같은 것에도 균일하게 작동하도록 보장합니다. 이는 응용 프로그램, 모든 종속성 및 라이브러리와 함께 단일 단위로 패키지하여 Docker와 같은 컨테이너 런타임이 있는 장치에서 실행할 수 있는 것을 의미합니다.

## 컨테이너화란?

<div class="content-ad"></div>

컨테이너화란 소프트웨어 코드와 의존성을 함께 패키징하여 어떤 인프라에서든 일관되고 균일하게 실행될 수 있도록 하는 과정을 말합니다. 이 방법은 애플리케이션이 한 컴퓨팅 환경에서 다른 곳으로 이동할 때 손쉽게 배포되고 신뢰성 있게 실행되도록합니다.

## 컨테이너화의 장점

- 일관성: 어디에서 실행하더라도 애플리케이션들이 동일하게 작동합니다.
- 효율성: 컨테이너는 가볍고 호스트 OS 커널을 공유하여 가상 머신보다 적은 시스템 리소스를 사용합니다.
- 확장성: 애플리케이션 크기가 서서히 증가함에 따라 애플리케이션을 쉽게 확장하거나 축소할 수 있습니다.
- 격리: 각 컨테이너는 독립된 환경에서 실행되어 보안과 안정성을 향상시킵니다.

![Docker Tutorial](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_1.png)

<div class="content-ad"></div>

# 도커란 무엇인가요?

도커는 개발자들이 쉽고 효율적으로 컨테이너화된 애플리케이션을 구축, 배포, 실행, 업데이트 및 관리할 수 있는 오픈 소스 플랫폼입니다. 도커가 인기를 끄는 주요 이유는 다음과 같습니다:

도커는 개발 워크플로우를 간소화하고, "내 컴퓨터에서는 잘 되는데"라는 문제를 줄여주며, 애플리케이션을 효율적으로 배포하고 확장할 수 있도록 해줍니다.

도커의 주요 이점:

<div class="content-ad"></div>

- 이동성: 응용 프로그램은 배포 위치에 관계없이 동일하게 실행됩니다.
- 확장성: 수요에 따라 손쉽게 응용 프로그램을 확장하거나 축소할 수 있습니다.
- 격리: 컨테이너는 응용 프로그램과 해당 종속성을 캡슐화하여 서로 독립적으로 실행되도록 보장합니다.

# 도커의 역사

도커는 처음 2013년 3월 DotCloud(현 Docker, Inc.)에서 처음 출시되었습니다. 도커의 아이디어는 서로 다른 환경에서 응용 프로그램을 일관되게 패키징하고 실행하는 가벼우며 효율적인 방법을 만드는 것이었습니다. 이는 선적용 컨테이너 개념에서 영감을 받았으며, 응용 프로그램과 그 종속성이 표준화된 컨테이너로 패키지화되어 쉽게 이동하고 배포할 수 있습니다.

현재 도커는 다양한 산업군에서 널리 사용되고 있으며, 많은 개발자 및 기여자들로 이루어진 큰 활발한 커뮤니티에 의해 지원되고 있습니다. 컨테이너화의 핵심 기술로 자리를 잡았으며, 컨테이너 오케스트레이션을 위한 Kubernetes와 같은 관련 기술의 성장을 이끌어내고 있습니다.

<div class="content-ad"></div>

# 도커 시작하기

도커를 시작하기 전에 시스템에 설치해야 합니다. 도커는 Windows, macOS 및 Linux를 포함한 여러 플랫폼에서 사용할 수 있습니다.

# 도커 설치하기

공식 웹사이트에서 운영 체제에 맞는 지침을 따르세요.

<div class="content-ad"></div>

설치를 완료한 후 Docker가 실행 중인지 확인해보세요:

```js
docker --version
```

# 도커 아키텍처

도커의 아키텍처를 이해해보세요.

<div class="content-ad"></div>

## 도커 구성 요소

도커는 여러 핵심 구성 요소를 활용하는 클라이언트-서버 구조를 사용합니다:

- 도커 클라이언트: 사용자가 도커와 상호 작용할 수 있도록 해주는 명령줄 인터페이스(CLI) 도구입니다. 도커 데몬과 통신하여 명령을 실행합니다.
- 도커 데몬 (또는 도커 엔진): 도커 엔진은 오픈 소스 컨테이너화 기술로, 개발자가 애플리케이션을 컨테이너로 패키징할 수 있게 합니다. 컨테이너는 어떤 환경에서든 해당 코드를 실행하는 데 필요한 응용프로그램 원본 코드, 운영 체제(OS) 라이브러리 및 종속성이 통합된 표준화된 실행 가능한 구성 요소입니다. 도커 API 요청을 수신하고 해당 요청을 처리합니다.
- containerd: 컨테이너의 라이프사이클을 관리하는 핵심 구성 요소로, 컨테이너 프로세스의 시작, 중지 및 관리를 포함합니다.
- runc: Open Container Initiative (OCI) 명세에 따라 컨테이너를 생성하고 실행하는 가벼운 CLI 도구입니다.
- 도커 레지스트리: 도커 이미지를 저장하고 배포하는 서비스입니다. 도커 허브는 기본 공개 레지스트리지만 개인 레지스트리도 사용할 수 있습니다. 이는 github와 유사하지만 이미지를 푸시(push)하는 대신 소스 코드를 업로드할 수 있습니다. 예시입니다.
- 도커 네트워킹: 컨테이너들이 서로와 외부 세계와 통신할 수 있게 하는 네트워킹 기능을 제공합니다.
- 도커 볼륨과 바인드 마운트: 데이터 지속성 및 컨테이너 간 또는 호스트 시스템 간의 공유를 가능하게 합니다.
- Docker Compose: YAML 파일을 이용하여 다중 컨테이너 애플리케이션을 정의하고 실행하는 도구입니다.

<div class="content-ad"></div>

# 도커가 애플리케이션을 실행하는 방법

- Build: 도커 클라이언트가 도커 데몬에 빌드 요청을 보내면, 도커파일에 있는 명령에 따라 이미지를 생성합니다.
- Ship: 이미지는 도커 레지스트리(공개 또는 개인)에 저장되어 다운로드하고 공유할 수 있습니다.
- Run: 도커 클라이언트가 도커 데몬에게 이미지를 기반으로 컨테이너를 생성하고 실행해 달라고 요청합니다.

# 도커 이미지

도커 이미지는 코드, 런타임, 라이브러리, 환경 변수, 설정 파일을 포함하여 소프트웨어를 실행하는 데 필요한 모든 것을 담고 있는 가벼운 독립형 실행 가능한 패키지입니다.

<div class="content-ad"></div>

## 도커 이미지 다운로드

도커 허브에서 이미지를 다운로드할 수 있어요:

```bash
docker pull hello-world
```

이미지를 다운로드한 후에는 `docker run` 명령어를 사용하여 손쉽게 실행할 수 있어요.

<div class="content-ad"></div>

도커 이미지와 컨테이너의 차이는 이렇게 설명할 수 있어요. 컨테이너는 깃헙에서 가져온 소스 코드로 컨테이너에서 노드 앱을 실행했을 때 생기는 것이에요. 반면 이미지는 깃헙에 있는 코드베이스에 비유할 수 있어요.

![이미지](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_3.png)

# 기본 도커 명령어 —

1.) 도커 버전 확인하기

<div class="content-ad"></div>

```js
도커 버전
```

2.) 시스템 전반적인 도커 정보 보기

```js
도커 정보
```

3.) 모든 도커 이미지 나열하기

<div class="content-ad"></div>

4. 실행 중인 컨테이너 목록


docker ps

docker ps -a      // 모든 컨테이너(실행 중인 것과 중지된 것) 목록


5. 레지스트리로부터 이미지 가져오기

<div class="content-ad"></div>

클릭하셔서 node의 특정 버전을 내려받습니다.

6. 이미지로부터 새 컨테이너를 생성하고 시작하기

아래 예제에서는 호스트 포트 8080을 컨테이너 포트 80에 매핑하여 NGINX 서버를 백그라운드 모드(-d)로 배포합니다.

```js
docker run -d -p 8080:80 nginx
```

<div class="content-ad"></div>

7.) 컨테이너 실행 중지하고 제거하기

```bash
docker stop <container_id>

docker rm <container_id>                // 중지된 컨테이너 제거
```

8.) 이미지 제거하기

```bash
docker rmi <image_id>
```

<div class="content-ad"></div>

9.) 도커 이미지 만들기


docker build -t <당신의 이미지 이름> .


10.) 레지스트리에 이미지 푸시하기


docker push <이미지 이름> <저장소 이름>


<div class="content-ad"></div>

# 도커 포트 매핑 설명

도커에서의 포트 매핑은 호스트 머신의 포트를 컨테이너 내의 포트로 매핑하는 과정입니다. 도커 호스트 외부에서 컨테이너 내에서 실행 중인 애플리케이션에 액세스하기 위해서는 이 과정이 필수적입니다.

## 포트 매핑 작동 방식

도커 컨테이너 내에서 3000번 포트에서 실행 중인 웹 서버가 있다고 가정해 보세요. 기본적으로 이 포트는 도커 네트워크 내에서만 액세스할 수 있고 호스트 머신이나 외부 네트워크에서는 접근할 수 없습니다.

<div class="content-ad"></div>

이 서버를 컨테이너 외부에서 접근 가능하게 하려면, 호스트와 컨테이너 사이의 포트를 포워딩해야 합니다.

예시:

![Docker port forwarding](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_4.png)

```js
docker run -p [호스트포트]:[컨테이너포트] [이미지이름]
```

<div class="content-ad"></div>

-p는 포트 매핑을 지정하는 데 사용되는 플래그입니다.

## Dockerfile

Dockerfile은 Docker 이미지를 빌드하는 방법에 대한 일련의 명령을 담고 있는 텍스트 파일입니다. 각 명령은 이미지에서 레이어를 생성하며, 이러한 레이어는 미래 빌드 작업을 가속화하기 위해 캐싱됩니다.

## Dockerfile의 주요 명령어

<div class="content-ad"></div>

- FROM: 이 후속 작업에서 기본 이미지를 설정합니다.
- WORKDIR: 컨테이너 내의 작업 디렉토리를 설정합니다.
- COPY: 호스트 시스템에서 컨테이너로 파일을 복사합니다.
- RUN: 컨테이너에서 명령을 실행합니다.
- CMD: 컨테이너 시작 시 실행할 명령을 지정합니다.
- EXPOSE: 컨테이너가 수신 대기하는 포트를 문서화합니다.

**.dockerignore**

.dockerignore 파일은 .gitignore 파일과 유사하게 작동합니다. Docker 이미지를 빌드할 때 무시해야 하는 파일 및 디렉토리를 지정합니다. 이를 통해 이미지를 가볍게 유지하고 불필요한 파일을 피할 수 있습니다. 이는 빌드 컨텍스트 크기를 줄이고 빌드 시간을 단축하는 데 도움이 됩니다. node_modules, dist 폴더 등을 추가하세요.

**도커 이미지를 빌드하는 방법은?**

<div class="content-ad"></div>

우리는 기본 mongo-express typescript 앱을 도커 이미지로 만드는 예제를 보여드리겠습니다.

1. 프로젝트 초기화

```bash
mkdir ts-express-app
cd ts-express-app
npm init -y

npm install express mongoose dotenv
npm install --save-dev typescript @types/node @types/express @types/mongoose ts-node
tsc --init
```

2. tsconfig.json 파일 생성하기

<div class="content-ad"></div>

```json
{
    "compilerOptions": {
        "target": "ES6",
        "module": "commonjs",
        "outDir": "./dist",
        "rootDir": "./src",
        "strict": true
    }
}
```

3.) src/index.ts 파일을 생성합니다.

```javascript
import express from 'express';
import mongoose from 'mongoose';
import dotenv from 'dotenv';

dotenv.config();

const app = express();
const PORT = 3000;
const DB_URL = process.env.DATABASE_URL || '';

mongoose.connect(DB_URL, { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log('Connected to MongoDB'))
    .catch(err => console.error('Could not connect to MongoDB', err));

app.get('/', (req, res) => {
    res.send('Ram Ram bhai Sareya Ne');
});

app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
```

4.) package.json 파일과 .env 파일을 생성해주세요.

<div class="content-ad"></div>

```json
"scripts": {
    "start": "node dist/index.js",
    "build": "tsc"
}    
```

```js
DATABASE_URL=mongodb://localhost:27017/ts-express-app
```

## 이제 도커로 만들어봅시다.

5. 루트 폴더에 Dockerfile을 생성하세요.

<div class="content-ad"></div>


# Node.js 20 이미지를 기본 이미지로 사용합니다.

WORKDIR /usr/src/app

# package.json 및 package-lock.json을 복사합니다.
COPY package*.json ./

# 의존성 설치
RUN npm install

# 나머지 애플리케이션 코드를 복사합니다.
COPY . .

# TypeScript 코드를 빌드합니다.
RUN npm run build

# 앱이 실행되는 포트를 노출합니다.
EXPOSE 3000

# 앱을 실행하는 명령어
CMD ["npm", "start"]


6.) 프로젝트 루트에 .dockerignore 파일을 만듭니다.


node_modules
dist
npm-debug.log


7.) 도커 이미지 빌드하기


<div class="content-ad"></div>

```bash
도커 빌드 -t ts-express-app .
```

위 명령어가 성공적으로 실행되면 아래와 유사한 출력이 나올 것입니다:

![도커 시작부터 전문가 튜토리얼 5](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_5.png)

8.) 도커 컨테이너 실행하기

<div class="content-ad"></div>

이미지가 생성된 후에 다음 명령을 사용하여 컨테이너를 실행할 수 있습니다.

```js
docker run -p 3000:3000 ts-express-app
```

이전에 말했던대로, 위 명령은 컨테이너를 실행하고 호스트 머신의 포트 3000을 컨테이너의 포트 3000에 매핑합니다. localhost:3000을 방문하여 컨테이너가 성공적으로 실행되는 것을 확인할 수 있습니다.

![Docker Tutorial](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_6.png)

<div class="content-ad"></div>

위 이미지에서 도커 이미지 목록에 내 이미지가 표시되어 있고 포트 3000에서 실행 중인 컨테이너를 볼 수 있습니다. 이 포트는 기계 포트 3000과 매핑되어 있습니다. 여기서 시간을 내어 주변을 둘러보고 이곳에서 무슨 일이 일어나고 있는지 분석해보세요. 또한 위 명령어에서 이전에 설명하지 않은 추가 사항을 사용했습니다. 이를 발견하고 그것에 대해 학습하세요. 발견했다면 코멘트 섹션에 적절한 설명과 함께 써 주세요 :)

이제 계속 진행하면, 이미지를 Docker Hub 레지스트리에 올리는 간단한 3개의 명령어가 있습니다 —

9.) 도커 레지스트리에 이미지 푸시하기

```js
docker login
docker tag ts-express-app your-dockerhub-username/ts-express-app:latest
docker push your-dockerhub-username/ts-express-app:latest
```

<div class="content-ad"></div>

위에 있는 "your-dockerhub-username" 자리에 도커허브 사용자명을 입력해주세요. 제가 나중에 설명할 표현태그를 사용했어요. 일단 참고해주세요!

도커허브에 이미지를 푸시한 것을 축하드려요. 이제 이미지 작성과 컨테이너 실행 기본 개념에 익숙해졌을 것 같아요, 그럼 조금 더 깊게 살펴봅시다.

운영 환경에서는 보안을 위해 .env 파일을 숨겨야 해요. 그래서 .env의 비밀 정보를 .env 파일을 사용하지 않고 도커에 전달하는 방법은 무엇인가요?

간단하게 "-e" 플래그를 사용해 앱에 환경 변수를 전송할 수 있어요.

<div class="content-ad"></div>

```js
도커 실행 -p 3000:3000 -e DATABASE_URL=mongodb://localhost:27017/ts-express-app ts-express-app
```

# 도커 태그

도커 태그는 특정 이미지 버전/변형에 대한 유용한 정보를 전달합니다. 태그를 사용하면 도커 레지스트리에서 이미지의 다른 버전을 식별하고 가져올 수 있습니다. 태그는 이미지의 ID에 대한 별칭이며 종종 이렇게 보입니다: f1477ec11d12. 단순히 이미지를 가리키는 방법일 뿐입니다. Git 태그가 특정 커밋을 가리키는 것과 비슷한 예시입니다.

도커 이미지에 태그를 붙이는 일반적인 구문은 다음과 같습니다: 


<div class="content-ad"></div>

```js
도커 태그 SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

태그를 지정하지 않으면 도커는 기본적으로 최신 태그를 사용합니다.

## 태그가 사용되는 두 가지 주요 사례는 다음과 같습니다:

- 이미지를 빌드하는 경우, 다음 명령을 사용합니다:

<div class="content-ad"></div>

```js
도커 빌드 -t 사용자명/이미지이름:태그명 .
```

2. 명시적으로 이미지를 태깅하는 방법.

```js
도커 태그 소스이미지[:태그] 대상이미지[:태그]
```

3. 버전 관리: 태그는 이미지의 다양한 버전을 표시하는 데 자주 사용됩니다. 예를 들어 1.0, 1.1, 2.0 등과 같은 태그를 가질 수 있으며, 이는 주요 또는 부 버전 업데이트를 나타낼 수 있습니다.

<div class="content-ad"></div>

4. 환경 또는 스테이지 식별: 태그는 개발, 스테이징 및 프로덕션 환경을 구분하는 데 도움이 될 수 있습니다. 예를 들어, dev, staging, prod와 같은 태그가 있을 수 있습니다.

5. 아키텍처 또는 플랫폼 식별: 태그는 amd64, arm64 또는 windows와 같은 다양한 아키텍처나 플랫폼용 이미지를 식별할 수 있습니다.

태그를 사용하여 더 자세히 이해해 봅시다.

- 태그를 사용하여 Docker 이미지 빌드하기:

<div class="content-ad"></div>

```bash
docker build -t express-mongo-app:1.0 .
```

2. 원하는 태그를 가져오기:

```bash
docker pull node:14.18.0
```

3. 기존 이미지에 새로운 태그를 추가하기:


<div class="content-ad"></div>


이미지 태그를 변경하는 방법은 아래와 같아요:

시멘틱 버전을 사용해서 (주 버전.부 버전.패치) —

```shell
docker build -t express-mongo-app:1.0.0 .
docker tag express-mongo-app:1.0.0 express-mongo-app:1.0
docker tag express-mongo-app:1.0.0 express-mongo-app:latest
```


<div class="content-ad"></div>

# 도커 exec 명령어

도커 exec 명령어는 실행 중인 도커 컨테이너 내에서 명령어를 실행할 수 있게 해줍니다. 디버깅을 위해, 관리 작업을 실행하거나 폴더/볼륨을 생성하거나 컨테이너의 상태를 검사하는 등 유용하게 활용할 수 있습니다.

## 기본적인 도커 exec 사용법

```js
도커 exec [옵션] CONTAINER_ID|CONTAINER_NAME COMMAND [ARG…]
```

<div class="content-ad"></div>

- 옵션: -it와 같은 인터랙티브 모드와 같은 다양한 옵션.
- 컨테이너: 컨테이너의 이름 또는 ID.
- 명령: 컨테이너 내부에서 실행하고 싶은 명령어.
- [ARG…]: 명령에 대한 인자들.

![이미지](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_7.png)

이 명령을 실행하면 실행 중인 컨테이너 내부에서 대화형 Bash 셸이 열립니다. 그러면 명령을 실행하거나 파일을 검사하거나 볼륨을 생성/제거하거나 컨테이너 환경 내에서 스크립트를 실행할 수 있습니다.

docker exec의 -it 옵션은 "-interactive -tty"를 의미하며, 이를 통해 컨테이너 셸과 상호작용할 수 있습니다.

<div class="content-ad"></div>

이제 궁금해하시겠죠. 제가 위에서 언급한 볼륨이란 무엇인지 알아보도록 합시다 —

# Docker 볼륨

Docker 볼륨은 도커 컨테이너에 장착된 파일 시스템으로, 컨테이너에서 생성된 데이터를 보존하는 데 사용됩니다.

## 도커 파일 시스템이란?

<div class="content-ad"></div>

도커 컨테이너는 도커 이미지로 지정된 소프트웨어 스택을 실행합니다. 이미지는 읽기 전용 레이어로 구성되어 있으며 Union 파일 시스템에서 작동합니다. 새로운 컨테이너를 시작할 때 도커는 이미지 레이어 위에 읽기-쓰기 레이어를 추가하여 컨테이너가 일반적인 Linux 파일 시스템처럼 작동하도록 합니다. 따라서 컨테이너 내에서 각 파일 수정은 읽기-쓰기 레이어에 작동하는 복사본을 생성합니다. 그러나 컨테이너가 중지되거나 제거되면 읽기-쓰기 레이어가 사라집니다.

도커는 호스트 파일 시스템의 일부인 볼륨을 관리합니다 (/var/lib/docker/volumes on Linux). 이 파일 시스템의 일부는 도커 프로세스 외부에서 변경되어서는 안됩니다. 도커에서 데이터를 저장하는 가장 효과적인 방법은 볼륨을 사용하는 것입니다. 도커 볼륨 작성 명령인 'docker volume create'를 사용하여 직접 볼륨을 생성할 수도 있고, 도커가 컨테이너나 서비스를 생성할 때 자동으로 생성하게 할 수도 있습니다.

볼륨에는 세 가지 주요 유형이 있습니다:

- 네임드 볼륨: 도커가 관리하며 호스트 파일 시스템의 지정된 위치에 저장됩니다.
- 바인드 마운트: 호스트 머신의 디렉토리나 파일을 컨테이너로 마운트합니다. 호스트 시스템에서는 바인드 마운트를 어디에나 유지할 수 있습니다. 이는 중요한 시스템 폴더나 파일일 수 있습니다. 또한 도커 호스트나 컨테이너에서 실행되는 도커 프로세스에 의해 항상 수정 가능합니다. 비교적으로 바인드 마운트는 볼륨보다 적게 유용합니다.
- tmpfs 마운트: 컨테이너로 일시적인 파일 시스템을 마운트하고 호스트 메모리에 저장합니다. 이러한 마운트는 호스트 시스템의 파일 시스템에 쓰이지 않고, 대신 호스트 시스템 메모리에만 저장됩니다. 디스크에 저장되지 않습니다. 컨테이너의 지속 시간 동안 tmpfs 마운트에 민감한 또는 비영구 상태 데이터를 저장할 수 있습니다.

<div class="content-ad"></div>

## 🗺️ Named Volumes:

아래 명령어를 사용하여 명명된 볼륨을 생성해보세요:

```js
docker volume create my-ts-app-data
```

이제 볼륨을 컨테이너 내부의 디렉토리에 마운트해 보겠습니다. 우리는 my-ts-app-data 볼륨을 컨테이너 내의 /app/data 디렉토리에 마운트하겠습니다. 컨테이너 내부의 /app/data에 기록된 모든 데이터는 호스트에 있는 명명된 볼륨에 저장됩니다.

<div class="content-ad"></div>

```bash
docker run -d -p 3000:3000 -e DATABASE_URL=mongodb://mongo:27017/ts-express-app -v my-ts-app-data:/data/db ts-express-app
```

![Docker volume inspect](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_8.png)

도커 볼륨 inspect "my-ts-app-data" 명령어를 사용하여 호스트 파일시스템에서 해당 볼륨의 위치를 포함한 세부 정보를 확인할 수 있습니다.

더 이상 필요하지 않은 볼륨을 제거할 때는 "rm" 명령어를 사용하세요.

<div class="content-ad"></div>

```docker
docker volume rm my-ts-app-data
```

![Docker Beginner to Expert Tutorial](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_9.png)

이제 새로운 것을 시도해 봅시다. MongoDB Compass 앱에서 데이터를 시각화하고 싶다고 가정해 봅시다. 그러나 컨테이너 내의 볼륨에 연결하는 방법은 무엇일까요?

아래 명령을 실행하여 로컬에서 mongo 컨테이너를 시작하세요 —

<div class="content-ad"></div>


도커를 실행하여 몽고DB를 시작합니다.
```bash
docker run -p 27017:27017 -d mongo
```
몽고DB 컴퍼스를 열고 27017 포트에 연결하세요. 데이터베이스와 컬렉션을 만들고 몇 가지 데이터를 삽입하여 저장하세요.

![2024-07-01-DockerBeginnertoExpertTutorial_10.png](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_10.png)

이제 컨테이너를 중지한 다음 다시 시작하세요. 몽고DB 컴퍼스를 열어서 이전에 생성한 DB와 데이터를 확인하세요. 무엇을 보실 건가요? 다 사라졌죠. 😅


<div class="content-ad"></div>

![이미지](https://miro.medium.com/v2/resize:fit:440/0*GTzdCMFX8ovYrk8Y.gif)

데이터를 어떻게 유지할까요? 맞추셨네요. 볼륨을 사용하죠!
이미 이전에 'my-ts-app-data'라는 이름의 볼륨을 만들었습니다. 그걸 사용합시다. 몽고 컨테이너의 /data/db 디렉토리에 볼륨을 마운트하고 아래 명령을 사용하여 실행하세요.

```js
docker run -d -v my-ts-app-data:/data/db -p 27017:27017 mongo
```

이제 위의 단계를 반복해서 데이터베이스를 생성하고 데이터를 추가하세요. 컨테이너를 중지시키고 다시 시작한 후 이전에 입력한 데이터를 다시 확인해보세요. 데이터가 유지되었다는 것을 확인할 수 있을 거에요!

<div class="content-ad"></div>

이제 우리는 네임드 볼륨에 대해 배웠으니, 이제 코어 도커 주제에 대해 자세히 알아봅시다. 앞서 언급했듯이 도커 볼륨을 설명할 때 "레이어"라는 용어를 사용했습니다. 그런데 레이어가 무엇일까요? 함께 살펴보겠습니다...

# 도커의 레이어

지금까지 알아왔듯이, 도커 빌드는 순서대로 배열된 빌드 명령어들의 집합으로 구성됩니다. Dockerfile의 각 명령은 대략적으로 이미지 레이어로도 불리는 레이어로 번역됩니다. 이미지로부터 새 컨테이너를 생성하면, 이미지 레이어 위에 쓰기 가능한 새 레이어가 추가되어 컨테이너가 기존 이미지 레이어를 수정하지 않고 변경을 가할 수 있게 합니다.

## 도커 레이어란 무엇인가요?

<div class="content-ad"></div>

- 베이스 레이어: 이는 당신의 도커 이미지의 시작점입니다. 도커 파일이 지정한 우분투, 알파인 등과 같은 운영 체제를 포함합니다. 이 레이어는 변경할 수 없는 불변한 상태이며, 이후 레이어들의 기초 역할을 합니다.
- 중간 레이어: 이 레이어들은 Dockerfile에 포함된 RUN, COPY, ADD와 같은 명령을 나타냅니다. 각각의 명령은 이전 레이어 위에 새로운 레이어를 생성합니다. 중간 레이어는 읽기 전용이며 캐싱됩니다.
- 최상단 읽기/쓰기 레이어: 이미지에서 컨테이너를 실행할 때, Docker는 읽기 전용 이미지 레이어 위에 쓰기 가능한 레이어를 추가합니다. 이를 통해 컨테이너는 기존 이미지를 수정하지 않고 변경할 수 있습니다.
- 재사용 가능하고 공유 가능: 레이어는 캐싱되어 다른 이미지들 간에 재사용될 수 있어 이미지 빌드와 공유를 효율적으로 만듭니다. 동일한 베이스 이미지나 공통 명령을 공유하는 경우, 동일한 레이어를 재사용하여 저장 공간을 줄이고 이미지 다운로드와 빌드 속도를 높일 수 있습니다.

## 레이어가 생성되는 방법

레이어는 Dockerfile에 지정된 명령에 따라 생성됩니다. Dockerfile의 각 명령은 이전 레이어 위에 새로운 레이어를 생성합니다. 다음은 예시 Dockerfile입니다 —

```js
FROM ubuntu:18.04      // 이 명령은 Docker 레지스트리에서 ubuntu:18.04 이미지를 가져와 베이스 레이어를 생성합니다.

COPY . /app            // 이 명령은 베이스 레이어 위에 새로운 레이어를 생성합니다. 빌드 컨텍스트(도커 파일이 있는 디렉토리) 전체를 컨테이너 내부의 /app 디렉토리로 복사합니다.

RUN make /app          // 이 명령은 컨테이너 내부에서 make /app 명령을 실행하여 또 다른 레이어를 생성합니다. 이 명령은 /app 디렉토리에 있는 애플리케이션을 빌드합니다.

CMD python /app/app.py  // 이 명령은 컨테이너 시작시 실행할 기본 명령을 지정하는 새로운 레이어를 생성합니다. 여기서는 python /app/app.py 명령을 실행하도록 지정했습니다.
```

<div class="content-ad"></div>

위에서 볼 수 있듯이 각 지시사항은 이전 것들의 맨 위에 새로운 레이어를 만들어 최종 Docker 이미지를 구성하는 레이어 스택을 형성합니다. 더 시각적으로 이해하기 위해 아래 이미지를 참고해 보세요.

![Docker Image Layers](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_11.png)

## 레이어 캐싱

Dockerfile을 사용하여 Docker 이미지를 빌드할 때 Docker는 각 지시사항을 순차적으로 처리하고 각각에 대해 새로운 레이어를 만듭니다. 이미지의 레이어가 변경되지 않은 경우 도커 빌더는 빌드 캐시에서 이를 가져옵니다. 마지막 빌드 이후에 레이어가 변경된 경우 해당 레이어와 그 뒤에 오는 모든 레이어를 다시 빌드해야 합니다. 예를 들어 설명하겠습니다 —

<div class="content-ad"></div>

위에 첨부된 스크린샷을 보면, 첫 번째 이미지에서 제가 express 앱을 이미지로 빌드했고 각 레이어가 처음부터 구축되었음을 볼 수 있습니다. 이제 app.js 파일에 간단한 변경사항(`hi`를 추가하는 console.log)을 적용했고 이미지를 다시 빌드하고 있습니다. 두 번째 이미지에서 변경된 레이어 5를 제외하고 레이어 2, 3, 4가 캐시되었음을 볼 수 있습니다. 변경 사항이 있는 파일이 있기 때문에 Docker는 해당 변경을 감지하였고, 레이어 5가 캐시되지 않고 처음부터 다시 빌드되었습니다. 그리고 레이어 5가 변경되었으므로 그 위에 있는(이후에 있는) 모든 레이어가 처음부터 다시 빌드될 것입니다.

# Docker 네트워크

기본적으로 Docker 컨테이너는 서로 통신할 수 없습니다. 따라서 Docker 네트워크는 컨테이너들이 서로 통신하고 외부와 통신할 수 있게 합니다. Docker 컨테이너 간의 통신에 대한 격리, 보안 및 제어를 제공합니다.

<div class="content-ad"></div>

![이미지](/assets/img/2024-07-01-DockerBeginnertoExpertTutorial_13.png)

# 도커 네트워크 유형

도커는 여러 종류의 네트워크를 제공합니다:

## 1. 브릿지 네트워크:

<div class="content-ad"></div>

스탠드얼론 컨테이너의 기본 네트워크 유형입니다. 브리지 네트워크는 호스트와 컨테이너 사이에 소프트웨어 기반 브리지를 만듭니다. 네트워크에 연결된 컨테이너는 서로 통신할 수 있지만, 네트워크 외부에서는 격리됩니다. 네트워크의 각 컨테이너에는 고유한 IP 주소가 할당됩니다. 네트워크가 호스트에 브리징되어 있기 때문에 컨테이너는 LAN 및 인터넷에서 통신할 수도 있습니다. 그러나 LAN의 물리적 장치로 표시되지는 않습니다.

## 2. 호스트 네트워크:

호스트 네트워크 모드를 사용하는 컨테이너는 격리 없이 호스트의 네트워크 스택을 공유합니다. 고유한 IP 주소가 할당되지 않으며, 포트 바인딩은 직접 호스트의 네트워크 인터페이스로 공개됩니다. 이는 포트 80에서 수신 대기 중인 컨테이너 프로세스가 `호스트 IP 주소`:80에 바인딩됨을 의미합니다.

## 3. 오버레이 네트워크:

<div class="content-ad"></div>

오버레이 네트워크는 여러 도커 호스트에 걸쳐있는 분산 네트워크입니다. 이 네트워크를 통해 호스트 중 하나에서 실행 중인 모든 컨테이너가 OS 수준의 라우팅 지원 없이 서로 통신할 수 있습니다. 오버레이 네트워크는 도커 스웜 클러스터의 네트워킹을 구현하는데 사용되지만, 컨테이너가 직접적으로 서로 통신해야 하는 두 개의 별도의 도커 엔진 인스턴스를 실행할 때에도 사용할 수 있습니다. 이를 통해 자체적인 스웜과 유사한 환경을 구축할 수 있습니다.

### 4. 맥락(vlan) 네트워크:

맥락(vlan)은 네트워크에서 컨테이너를 물리적 장치로 나타낼 수 있는 또 다른 고급 옵션입니다. 각 컨테이너에 고유한 MAC 주소가 할당되어 작동합니다. 이 네트워크 타입은 호스트의 물리적 인터페이스 중 하나를 가상 네트워크에 할당해야 합니다. 또한, 많은 컨테이너를 실행하는 활성 도커 호스트에 의해 생성될 수 있는 많은 MAC 주소를 지원하기 위해 넓은 네트워크도 적절하게 구성되어야 합니다.

### 5. ipvlan:

<div class="content-ad"></div>

IPvLAN은 컨테이너에 할당된 IPv4 및 IPv6 주소뿐만 아니라 레이어 2 및 3 VLAN 태깅 및 라우팅에 정밀한 제어를 제공하는 드라이버입니다. 이 드라이버는 컨테이너화된 서비스를 기존 물리 네트워크와 통합할 때 유용합니다. IPvLAN 네트워크는 성능상의 이점을 제공하는 고유한 인터페이스를 할당받습니다.

## 6. None Network:

네트워크 인터페이스가 없으며 격리되어 있는 컨테이너입니다.

이제 컨테이너들이 서로 통신하도록 해보겠습니다.

<div class="content-ad"></div>

— 아래 명령을 사용하여 네트워크를 생성하세요 (기본적으로 브리지 네트워크입니다):

컨테이너를 특정 네트워크를 지정하지 않고 실행하면, 브리지 네트워크에 연결됩니다.

```js
docker network create my-first-network
docker network ls
```

— 브리지 네트워크에서 컨테이너를 실행하세요 —

<div class="content-ad"></div>


도커(Docker)를 사용하여 c1 컨테이너와 c2 컨테이너를 연결해 보겠습니다. 먼저 c1 컨테이너 내부로 들어가서 c2 컨테이너를 핑(icmp)합니다.

```js
docker exec -it c1 /bin/bash
ping c2
```

작동하는 것을 확인할 수 있습니다. 이제 새로운 시도를 해보겠습니다. 하나의 컨테이너에는 몽고DB를 실행하고, 다른 컨테이너에는 익스프레스 앱을 실행한 후 몽고DB 컨테이너에 익스프레스 앱을 통해 접근해 보겠습니다. 이전에 만든 익스프레스 앱 코드를 그대로 사용하되, .env 파일에 아래 내용을 추가해주세요 —


<div class="content-ad"></div>


```js
DATABASE_URL=[container_name]://mongodb:27017/ts-express-app-db

DATABASE_URL=mongodb://mongodb:27017/ts-express-app-db
```

```js
docker run -d --name mongodb --network my-first-network -v my-ts-app-data:/data/db -p 27017:27017 mongo

docker run -d -p 3000:3000 --network my-first-network -e DATABASE_URL='mongodb://mongodb:27017/ts-express-app-db' ts-express-app
```

```js
docker exec -it mongodb mongo
use mydatabase
db.users.find().pretty()
```

모든 컨테이너가 실행되었으니 이제는 Postman이나 터미널의 curl 명령어를 사용하여 데이터를 추가해 보세요. 성공적인 응답을 보게 될 것이며, MongoDB에 데이터가 들어갔는지 확인하려면 컨테이너 내부로 들어가서 아래의 MongoDB 명령어를 실행해 보세요.

MongoDB의 데이터 여부를 확인하려면 MongoDB의 컨테이너 로그도 확인해 볼 수 있습니다.



<div class="content-ad"></div>

## 네트워크 제거하기 —

```js
docker network rm my-first-network

# 하나도 연결되어 있지 않은 네트워크 제거하기 (어떤 컨테이너에도 연결되어 있지 않는 네트워크)
docker network prune
```

컨테이너와 네트워크 연결 해제하기 —

```js
docker network disconnect my-first-network mongodb
```

<div class="content-ad"></div>

# 도커 컴포즈

도커 컴포즈는 여러 개의 컨테이너로 구성된 도커 애플리케이션을 정의하고 실행할 수 있게 해주는 도구입니다. 이는 애플리케이션을 구성하는 서비스들과 그들의 의존성, 네트워킹, 볼륨, 그리고 다른 설정들을 조정하고 조율하기 위해 YAML 파일(docker-compose.yml)을 사용합니다.

지금까지 사용해온 익스프레스 앱을 위한 docker-compose.yml 파일 예시 —

```js
version: '3.9'

services:
  ts-express-app:
    build: .
    image: "express-mongo-ts-docker"
    container_name: ts-express-app
    ports:
      - "3000:3000"
    environment:
      - MONGO_URL=mongodb://mongodb:27017/ts-express-app-db
    depends_on:
      - mongodb

  mongodb:
    image: mongo
    container_name: mongodb
    volumes:
      - my-ts-app-data:/data/db
    ports:
      - "27017:27017"

volumes:
  my-ts-app-data:
```

<div class="content-ad"></div>

위 파일이 무슨 뜻인지 줄마다 설명하며 배워보도록 하죠—

# 1.) 버전 선언:

- version: `3.9`: Docker Compose 파일 형식의 버전을 지정합니다.

# 2.) 서비스:

<div class="content-ad"></div>

서비스는 어떤 컨테이너 이미지를 사용할지 정의하는 배포 단위입니다. 위의 파일에서 2개의 서비스를 정의했습니다 —

## 가.) ts-express-app

이 서비스는 Express 앱을 정의합니다.

- build: .: 현재 디렉토리의 Dockerfile에서 Docker 이미지를 빌드합니다.
- image: "express-mongo-ts-docker": 이미지를 express-mongo-ts-docker로 지정합니다. (선택 사항)
- container_name: ts-express-app: 컨테이너 이름을 ts-express-app으로 설정합니다.
- ports: - "3000:3000": 포트 매핑은 평소와 같이 이루어집니다.
- environment: - MONGO_URL=mongodb://mongodb:27017/ts-express-app-db: 앱이 MongoDB에 연결하기 위해 사용하는 환경 변수 MONGO_URL을 설정합니다. 이 URL은 아래 구성 파일에서 정의된 mongodb 서비스를 가리킵니다.
- depends_on: - mongodb: ts-express-app 서비스가 mongodb 서비스 이후에 시작되도록 합니다.

<div class="content-ad"></div>

## b.) 몽고디비:

이 서비스는 MongoDB 데이터베이스를 정의합니다.

- 이미지: mongo: 도커 허브에서 공식 MongoDB 이미지 사용.
- 컨테이너 이름: mongodb: 컨테이너 이름을 mongodb로 설정합니다.
- 볼륨: - my-ts-app-data:/data/db: 도커 볼륨 my-ts-app-data를 컨테이너 내의 /data/db에 마운트하여 MongoDB가 데이터를 저장하는 위치로 설정합니다.
- 포트: - "27017:27017": 포트 매핑은 평소와 같이 진행됩니다.

<div class="content-ad"></div>

- my-ts-app-data: 여기에 우리가 사용하는 볼륨을 정의합니다.

그럼 이제 컴포즈 파일을 배웠으니, 위 코드를 복사하여 앱의 루트 디렉토리에 docker-compose.yml 파일을 정의하세요. 그런 다음 터미널에 들어가서 docker-compose up --build 명령을 실행하고 이미지를 빌드하고 컨테이너를 시작할 때까지 기다리세요. 성공 메시지를 볼 수 있고 이제 마침내 몽고와 익스프레스 앱 컨테이너가 같은 네트워크에서 올바르게 연결된 볼륨에 준비된 상태로 실행 중임을 알 수 있습니다.

애플리케이션을 중지하려면 — docker-compose down 명령을 사용하세요.

쉽죠, 나! :)

<div class="content-ad"></div>

게다가 다음과 같이 사용자 정의 네트워크를 정의할 수도 있습니다. 그 밖에도 몇 가지 설명이 필요한 사용자 정의 사항들이 있어요. 앞서 언급한 대로 이와 같은 방법으로 네트워크를 정의하지 않으면, 도커는 서비스를 위해 기본 네트워크를 자동으로 생성합니다. 보통 이 기본 네트워크는 docker-compose.yml 파일이 있는 프로젝트 디렉토리의 이름으로 지정됩니다.

아래에 저희 익스프레스 앱의 github 저장소 링크도 함께 첨부했어요. 클론하고, 라이브러리 설치하고, docker-compose.yml 파일에 정의된대로 볼륨을 생성하고 docker-compose up --build 명령어를 실행하기만 하면 됩니다.

음, 그게 다예요. 이게 기본적인 도커-컴포즈예요! 나머지는 여러분이 스스로 탐험해보시면 되요!

# 마지막으로

<div class="content-ad"></div>

이 글에서는 도커의 기본부터 중급까지 대부분의 기본 개념을 예제와 함께 다루었습니다. 초심자가 이해해야 하는 모든 것을 설명하려 노력했고 자신 있게 말씀드릴 수 있어요. 

내용을 이해하셨기를 바라며 최대한 많은 도움을 얻으시길 바래요. 글이 다소 길 수 있지만, 초심자가 도커를 시작하는 데 필요한 모든 것을 요약하려 노력했습니다. 어려움이나 새로운 도커 기능을 시도하고 싶을 때는 언제나 공식 문서를 참조해주세요.

이런 주제/기술에 대한 글을 더 원하시면 박수를 치거나 댓글을 남겨주세요. 친구들이나 도커를 배우고 싶어하는 분들과 이 글을 공유해주세요! 

![image](https://miro.medium.com/v2/resize:fit:1000/0*IpGyQFK7VuuElDiC.gif)