---
title: "OpenAI API를 사용한 나만의 챗봇 만들기 및 Docker 이미지로 변환하는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-HowtocreateyourownchatbotusingtheOpenAIAPIandturnitintoaDockerimage_0.png"
date: 2024-07-01 21:05
ogImage: 
  url: /assets/img/2024-07-01-HowtocreateyourownchatbotusingtheOpenAIAPIandturnitintoaDockerimage_0.png
tag: Tech
originalTitle: "How to create your own chatbot using the OpenAI API and turn it into a Docker image"
link: "https://medium.com/@yharystefa/how-to-create-your-own-chatbot-using-the-openai-api-and-turn-it-into-a-docker-image-b35b7ae6b51e"
---


![이미지](/assets/img/2024-07-01-HowtocreateyourownchatbotusingtheOpenAIAPIandturnitintoaDockerimage_0.png)

# 소개

챗봇의 개념, 적용 분야 및 자然 언어 처리 작업에 OpenAI와 같은 API를 사용하는 중요성 소개

# 필수 조건

<div class="content-ad"></div>

여행 전에 반드시 해야 할 것들을 준비해 봅시다!

- 오픈에이아이(OpenAI) 계정
- 오픈에이아이(OpenAI)의 API 키
- 비주얼 스튜디오 코드(Visual Studio Code) 설치
- 파이썬(Python) 프로그래밍에 대한 기본적인 지식

# 단계 1: 오픈에이아이(OpenAI) 계정 생성하기

아직 오픈에이아이(OpenAI) 계정이 없다면 어떻게 가입하는지 알려드리겠습니다. 가능하다면 회원가입 과정을 스크린샷으로 포함해서 설명해 드릴게요.

<div class="content-ad"></div>

# **단계 2: OpenAI에서 API 키 가져오기**

여행자 여러분, 이제는 OpenAI에서 API 키를 얻는 과정을 안내해 드리겠습니다!

1. 먼저 OpenAI 계정에 로그인해 주세요.
2. API 섹션으로 이동해 주세요.
3. 새 API 키를 생성해 주세요.
4. API 키를 안전하게 보관하는 중요성에 대해 설명해 주세요.

계정을 로그인하고 API 섹션으로 이동하세요! 🌟

# **단계 3: Visual Studio Code에서 프로젝트 설정하기**

<div class="content-ad"></div>

비주얼 스튜디오 코드에서 프로젝트 설정 방법 안내:

- 프로젝트를 위한 새 디렉토리를 생성합니다.
- 비주얼 스튜디오 코드를 열고 프로젝트 디렉토리로 이동합니다.
- Python 개발을 위한 가상 환경을 설정합니다.

```js
$ cd docker_project
$ virtualenv .env
$ source .env/bin/activate
```

![이미지](/assets/img/2024-07-01-HowtocreateyourownchatbotusingtheOpenAIAPIandturnintoaDockerimage_1.png)

<div class="content-ad"></div>

파이썬 스크립트를 개발하고 OpenAI API를 사용하여 챗봇을 만들어보세요!

먼저, 가상 환경에 필요한 라이브러리(openai, dotenv 등)를 설치해야 합니다. 아래 명령어를 사용해 필요한 라이브러리를 설치하세요.

```js
$ pip install openai python-dotenv
```

다음으로, OpenAI API와 상호작용하기 위한 파이썬 코드를 작성하세요. 챗봇 응답을 위한 함수를 정의하세요. 이러한 과정을 통해 멋진 챗봇을 만들어보세요!

<div class="content-ad"></div>

```python
import os
from typing import List, Dict, Any
from openai import OpenAI, APIError
from dotenv import load_dotenv

def load_api_key() -> str:
    """
    Load the OpenAI API key from the environment variables.

    Returns:
        str: The OpenAI API key.

    Raises:
        ValueError: If the API key is not found in the environment variables.
    """
    load_dotenv()
    api_key = os.getenv("OPENAI_API_KEY")
    
    if not api_key:
        raise ValueError("OpenAI API key is not set. Make sure the .env file contains OPENAI_API_KEY.")
    
    return api_key

def create_openai_client(api_key: str) -> OpenAI:
    """
    Create an OpenAI client using the provided API key.

    Args:
        api_key (str): The OpenAI API key.

    Returns:
        OpenAI: The OpenAI client.
    """
    return OpenAI(api_key=api_key)

def get_assistant_response(client: OpenAI, messages: List[Dict[str, str]]) -> str:
    """
    Generate a response from the assistant based on the provided messages.

    Args:
        client (OpenAI): The OpenAI client.
        messages (List[Dict[str, str]]): The conversation history.

    Returns:
        str: The assistant's response.
    """
    try:
        response = client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=messages,
            temperature=0.6, # Try different temperatures, remember that the closer to one the more creative the response, the closer to zero the more accurate the response.
            max_tokens=100
        )
        return response.choices[0].message.content
    
    except APIError as e:
        print(f"OpenAI API Error: {e}")
        return "I'm sorry, but I encountered an error while processing your request."

def main() -> None:
    """
    Main function to run the chat with the OpenAI assistant.
    """
    try:
        api_key = load_api_key()
        client = create_openai_client(api_key)
        
        messages = [
            {"role": "system", "content": "Absolutely, let's dive into Docker! Docker is a powerful tool used for containerization, allowing you to package your applications and all their dependencies into a standardized unit for software development."},
            {"role": "user", "content": "I'm a software engineer and I need to learn about Docker. Act as a Docker expert and teach me Docker from scratch, including exercises and evaluations."}
        ]
    
        while True:
            # Generate a response from the assistant
            assistant_response = get_assistant_response(client, messages)
            print("Assistant:", assistant_response)
            
            # Request new input from the user
            user_input = input("You: ")
            
            # Add the user's input to the conversation history
            messages.append({"role": "user", "content": user_input})
            
            # Add the assistant's response to the conversation history
            messages.append({"role": "assistant", "content": assistant_response})
    
    except ValueError as ve:
        print(f"ValueError: {ve}")
    
    except Exception as ex:
        print(f"Unexpected Error: {ex}")

if __name__ == "__main__":
    main()
```

# Step 5: 환경 변수 설정

Visual Studio Code에서 환경 변수를 설정하여 OpenAI API 키를 안전하게 저장하는 방법을 설명합니다:

- .env 파일에 OPENAI_API_KEY=your_api_key_here를 추가합니다.
- dotenv 라이브러리를 사용하여 API 키를 안전하게 Python 스크립트로로드합니다.


<div class="content-ad"></div>


Step 6: Docker 이미지 생성하기

챗봇 애플리케이션을 Docker화 하는 방법을 알려드릴게요:

프로젝트 디렉토리에 Dockerfile을 생성해주세요.


<div class="content-ad"></div>


# Python 3.11의 공식 기본 이미지를 사용하세요.
FROM python:3.11-slim

# 컨테이너 내의 작업 디렉토리를 설정하세요.
WORKDIR /app

# 소스 코드와 .env 파일을 컨테이너로 복사하세요.
COPY . .

# 필요한 종속성을 설치하세요.
RUN pip install --no-cache-dir -r requirements.txt

# OpenAI API 키를 위한 환경 변수를 설정하세요.
ENV OPENAI_API_KEY="당신의_API_키"

# Python 스크립트를 실행하는 명령어를 설정하세요.
CMD ["python", "your_api_key_here"]


2. docker build -t chatbot-image . 명령을 사용하여 Docker 이미지를 빌드하세요.

# 단계 7: 테스트 및 문제 해결

로컬에서 Docker화된 챗봇을 테스트하는 방법에 대해 알아보세요:


<div class="content-ad"></div>

- 컨테이너를 시작하려면 docker run -it chatbot-imagen:latest을 사용하세요.
- OpenAI API와 상호 작용이 올바르게 이루어지는지 확인하기 위해 챗봇 기능을 테스트하세요.

# 8단계: 이미지 취약점 확인

도커 이미지의 취약점을 검사하는 방법을 보여드릴게요:

- 도커의 취약점 스캔 도구를 사용하거나 docker-scout quickview chatbot-image:latest와 같은 타사 도구를 사용하여 도커 이미지를 스캔하세요.
- 발견된 취약점을 해결하기 위한 모범 사례에 대해 논의해보세요.

<div class="content-ad"></div>

# 고려 사항 및 모범 사례

추가적인 고려 사항과 모범 사례를 제공해 드립니다:

- 버전 및 태깅: Docker 이미지를 버전과 함께 태깅하여 관리를 효율적으로 할 수 있습니다 (v1.0, latest).
- 지속적 통합 (CI/CD): Docker 빌드를 CI/CD 파이프라인에 통합하여 자동화된 테스트와 배포가 가능합니다.
- 배포 전략: Docker로 구성된 애플리케이션의 다양한 배포 전략에 대해 논의해 보세요 (클라우드, 온프레미스 등).
- 모니터링 및 로깅: 성능 및 사용자 상호작용을 효과적으로 추적하기 위해 모니터링과 로깅을 구현하세요.

# 결론

<div class="content-ad"></div>

OpenAI API를 사용하여 챗봇을 만들고 Dockerizing하는 과정을 간략하게 요약해보았어요. 이 프로세스를 자세히 살펴보고 여러분의 프로젝트에 적용해보세요! 여기 [레포지토리 링크]에서 완전한 코드를 찾을 수 있어요. 가상 환경을 만들고 저가 남긴 명령어를 실행하기만 하면 프로젝트를 실행할 수 있어요. 자세한 내용은 레포지토리를 확인해보세요!