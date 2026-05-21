# Kimtalmo's Blog

이 블로그는 저의 프로젝트, 학습 기록, 그리고 유용한 팁들을 공유하기 위해 만든 Hugo 기반의 블로그입니다.

- **URL:** [https://4bmtolobpl.github.io/blog/](https://4bmtolobpl.github.io/blog/)
- **Theme:** [Terminal](https://github.com/panr/hugo-theme-terminal)

## 🚀 주요 구성

이 블로그는 크게 두 가지 섹션으로 구성되어 있습니다:

1. **Blog (`content/posts`)**: 일상적인 학습 내용, 문제 해결 과정, 기술 팁 등을 작성합니다.
2. **Showcase (`content/showcase`)**: 제가 진행한 프로젝트들을 정리하여 보여줍니다.

## 🛠 로컬 개발 방법

로컬 환경에서 블로그를 실행하고 수정 사항을 미리 확인하려면 아래 단계를 따르세요.

### 사전 준비

- [Hugo](https://gohugo.io/installation/) (Extended version 권장, v0.161.1 이상)
- [Go](https://golang.org/doc/install)
- [Node.js](https://nodejs.org/en/download/)

### 실행 방법

1. 저장소를 클론합니다:
   ```bash
   git clone --recursive https://github.com/4bmtolobpl/blog.git
   cd blog
   ```

2. Hugo 서버를 실행합니다:
   ```bash
   hugo server -D
   ```

3. 브라우저에서 `http://localhost:1313/blog/`로 접속합니다.

## 📝 새로운 콘텐츠 추가하기

### 포스트 작성
```bash
hugo new posts/my-new-post.md
```

### 쇼케이스 작성
```bash
hugo new showcase/my-project.md
```

생성된 파일은 `content/` 디렉토리 내에 위치하며, Markdown 형식으로 작성할 수 있습니다.

## 📦 배포

이 프로젝트는 **GitHub Actions**를 통해 자동 배포됩니다. `master` 브랜치에 코드가 푸시되면 자동으로 빌드되어 GitHub Pages에 반영됩니다.

배포 워크플로우 설정은 `.github/workflows/hugo.yaml` 파일에서 확인할 수 있습니다.
