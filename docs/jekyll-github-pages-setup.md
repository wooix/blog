# Jekyll + GitHub Pages 설치 문서

로컬 작업 경로: `~/workspace/blog`

## 0. 사전 준비

```bash
# Ruby 설치 확인 (없으면 설치)
ruby -v

# 맥 기준 설치
brew install ruby

# Bundler 설치
gem install bundler
```

## 1. 작업 폴더 생성

```bash
mkdir -p ~/workspace
cd ~/workspace
```

## 2. GitHub 저장소 준비

GitHub에서 먼저 저장소를 생성합니다.

- **개인 메인 사이트**로 쓸 경우: 저장소 이름을 반드시 `사용자명.github.io`로 생성
  → `https://사용자명.github.io`로 서비스됨
- **프로젝트/블로그용**으로 쓸 경우: 저장소 이름 자유(예: `blog`)
  → `https://사용자명.github.io/blog/`로 서비스됨 (이 경우 `_config.yml`에 `baseurl: "/blog"` 추가 필요)

로컬로 clone:

```bash
git clone git@github.com:사용자명/저장소명.git ~/workspace/blog
cd ~/workspace/blog
```

## 3. Jekyll 사이트 생성

```bash
gem install jekyll
jekyll new --skip-bundle --force .
```

`~/workspace/blog` 폴더 안에 Jekyll 기본 뼈대(`_config.yml`, `_posts/`, `Gemfile` 등)가 생성됩니다.

## 4. GitHub Pages용 Gemfile 조정

`~/workspace/blog/Gemfile`을 열어 아래처럼 수정:

```ruby
# gem "jekyll", "~> 4.3"   # 주석 처리
gem "github-pages", "~> 232", group: :jekyll_plugins   # 주석 해제 + 버전 지정
```

버전 번호는 [GitHub Pages 의존성 페이지](https://pages.github.com/versions/)에서 최신 값 확인.

이렇게 맞춰두면 로컬 Jekyll 버전과 GitHub이 실제 빌드할 때 쓰는 버전이 일치해서 "로컬은 되는데 배포하면 깨지는" 문제를 방지할 수 있습니다.

## 5. 의존성 설치 및 로컬 미리보기

```bash
cd ~/workspace/blog
bundle install
bundle exec jekyll serve
```

브라우저에서 `http://localhost:4000` 접속해 확인.

## 6. GitHub에 push

```bash
cd ~/workspace/blog
git add .
git commit -m "init jekyll site"
git push origin main
```

## 7. GitHub Pages 활성화

저장소 → Settings → Pages → Build and deployment → Source:

- **"Deploy from a branch"** (권장, 가장 간단) → Branch: `main`, 폴더: `/root`
  → push할 때마다 GitHub이 자동으로 Jekyll 감지 후 빌드·배포
- **"GitHub Actions"** → 최신 Jekyll 버전/플러그인이 필요할 때, GitHub 제공 "Jekyll" 시작 워크플로 사용

### "Deploy from a branch" vs "GitHub Actions" 차이

**Deploy from a branch (레거시 파이프라인)**
push하면 GitHub 서버 내부에 있는 자체 빌드 시스템이 알아서 Jekyll을 실행해줍니다. 별도 설정 파일이 필요 없어서 간단하지만 두 가지 제약이 있습니다.
- Jekyll 버전이 `Gemfile`에 지정한 `github-pages` gem 버전으로 고정됨 (최신 Jekyll 기능 사용 불가)
- 보안상 GitHub이 미리 허용해둔 "안전 목록(safe list)"에 있는 플러그인만 동작함 → 목록 밖 플러그인은 에러 없이 조용히 무시됨

**GitHub Actions (직접 빌드 파이프라인)**
`.github/workflows/` 안에 워크플로 파일을 직접 작성해서, GitHub이 아니라 "내가 지정한 방식"으로 빌드하는 방식입니다. Hugo 때 만들었던 것과 동일한 개념입니다. 이렇게 하면:
- 원하는 Jekyll 버전을 자유롭게 지정 가능
- safe list 제약 없이 아무 gem/플러그인이나 설치해서 사용 가능
- 빌드 전에 추가 스크립트(이미지 최적화, 검사 등)를 끼워 넣을 수 있음

동작 흐름은: 코드 checkout → Ruby 설치 → `bundle install` → `jekyll build`로 정적 파일 생성 → 결과물을 "Pages artifact"로 업로드 → 배포. 대략 아래와 같은 워크플로입니다.

```yaml
# .github/workflows/jekyll.yml
name: Deploy Jekyll site

on:
  push:
    branches: ["main"]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: '3.3'
          bundler-cache: true
      - uses: actions/configure-pages@v5
      - run: bundle exec jekyll build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: "_site"

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

**정리:** 처음엔 "Deploy from a branch"로 시작해도 충분합니다. 나중에 특정 플러그인이 안 먹거나 최신 Jekyll 기능이 필요해지면, 그때 이 워크플로 파일 하나 추가하고 Source를 "GitHub Actions"로 바꾸면 됩니다(기존 콘텐츠나 설정은 그대로 유지).

## 8. 접속 확인

몇 분 후 Settings → Pages 상단에 뜨는 배포 URL로 접속:

```
https://사용자명.github.io
# 또는
https://사용자명.github.io/blog/
```

## 9. (선택) 커스텀 도메인

```bash
echo "yourdomain.com" > ~/workspace/blog/CNAME
```

도메인 등록업체에서 A/CNAME 레코드를 GitHub Pages로 설정 후, Settings → Pages → Custom domain 필드에도 입력.

## 이후 글 작성 워크플로

```bash
cd ~/workspace/blog
# _posts/YYYY-MM-DD-title.md 형식으로 파일 생성 후 작성
bundle exec jekyll serve   # 로컬 확인
git add .
git commit -m "new post: title"
git push origin main       # push 시 자동 배포
```
