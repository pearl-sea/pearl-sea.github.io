---
layout: post
title: CI/CD와 Github Actions
categories: ["study"]
---

CI/CD는 애플리케이션 개발 단계를 **자동화**하여 애플리케이션을 더욱 짧은 주기로 고객에게 제공하는 방법이다.

---

#### CI (Continuous Integration)

개발자들이 코드를 원격 저장소에 `push`하고 `merge`했을때 빌드 / 테스트를 자동화하는 프로세스이다.

#### CD (Continuous Deployment/Delivery)

빌드의 결과물인 애플리케이션 배포를 자동화하는 프로세스이다.

- **Continuous Deployment**  
  개발한 코드가 테스트를 통과했을때 **실제 운영 환경**에 자동 배포하는것이다.

- **Continuous Delivery**  
   개발한 코드가 테스트를 통과했을때 **스테이징 환경**에 배포하는것이다. 실제 운영환경과 유사한 테스트 환경이기 때문에 스테이징 환경에서 배포되는 애플리케이션은 베타 버전, 테스트 버전과 같은 용어로 표현된다.
  <br><br>

#### CI/CD 파이프라인

일반적으로 Code - Test - Build - Staging - Deploy의 과정을 거친다.

- **Code**  
  개발자들이 소스코드를 작성하는 단계이다.

- **Test**  
  작성된 코드를 자동/수동 테스트를 통해 검증하는 단계이다.

- **Build**  
  소스코드를 실행 가능한 형태로 변환하는 단계이다. 이 과정에 컴파일도 포함된다.

- **Staging**  
  개발한 애플리케이션을 테스트 환경에 배포하는 단계이다.

- **Deploy**  
   사용자들이 실제로 애플리케이션을 사용할 수 있도록 배포하는 단계이다.
  <br><br>

#### Github actions

빌드, 테스트, 배포 파이프라인을 자동화할 수 있는 CI/CD플랫폼이다.  
Pull Request, push와 같은 이벤트를 트리거로 하는 Workflow를 구성할 수 있다.

#### 빌드 / 배포 자동화 Workflow 구성하기

- **YAML**  
  설정파일을 작성할때 일반적으로 YAML으로 작성하며 .github/workflow 경로에 yaml/yml 파일을 생성한다.

* **Action secrets**  
  원격 저장소 페이지에서 settings > Secrets and variables > Action -> New repository secret 클릭 후 시크릿을 등록하면 된다.

```yaml
name: Deploy to AWS S3

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Build
        run: npm run build

      - name: Sync Bucket
        env:
          AWS_ACCESS_KEY_ID: ${% raw %}{{ secrets.AWS_ACCESS_KEY_ID }}{% endraw %}
          AWS_SECRET_ACCESS_KEY: ${% raw %}{{ secrets.AWS_SECRET_ACCESS_KEY }}{% endraw %}
          AWS_EC2_METADATA_DISABLED: true
        run: |
          aws s3 sync \
            --region ap-northeast-2 \
            build s3://배포할 버킷에 따라 적절하게 변경 \
            --delete
```
