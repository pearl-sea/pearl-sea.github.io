---
layout: post
title: lint-staged와 husky를 이용한 prettier 자동화
categories: ["Study"]
---

코드 포맷팅을 적용하지 않은 커밋을 원격 저장소에 푸시하는 실수를 방지하기 위해 프로젝트에 lint-staged와 husky를 적용해 보았다.

#### Git hooks

깃과 관련된 이벤트가 발생 했을 때 스크립트를 실행하는 기능이다.  
예를 들어 `pre-commit`이라는 스크립트가 있다면 커밋 전에 어떤 행동을 실행하게 만들 수 있다.

---

자동화하려면 아래와 같은 패키지가 필요하다.

- **husky**  
  git hooks을 지원하는 패키지이다.

- **lint-staged**  
  스테이징 영역에 올라온 파일에만 지정된 스크립트를 실행하는 패키지이다.

#### 설치

```bash
npm install lint-staged husky --save-dev
```

#### 적용

package.json `scripts` 부분에 추가

```json
{
  "scripts": {
    "prepare": "husky install",
    "lint-staged": "lint-staged"
  }
}
```

package.json에 추가

```json
"lint-staged": {
  "**/*.{js,jsx}": [
      "eslint --fix",
      "prettier --write"
  ]
},
"husky": {
  "hooks": {
    "pre-commit": "lint-staged"
  }
}
```

package.json만 수정하면 자동화가 되어야 하는데 잘 안돼서 이렇게도 입력했다.

```bash
npx husky install
npx husky add .husky/pre-commit "npm run lint-staged"
```

이렇게 해도 안 된다면 dependencies에 라이브러리가 빠져 있을 지 모르니 확인해보자.
