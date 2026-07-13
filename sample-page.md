---
layout: page
title: "샘플 페이지"
permalink: /sample-page/
---

이 파일은 **일반 페이지(page)** 샘플입니다.

## 페이지(page)가 되는 조건

- **위치** — `_posts/`가 아니라 **아무 곳(여기서는 프로젝트 루트)** 에 둡니다.
- **날짜 파일명 규칙 없음** — 파일명은 자유입니다. 이 파일은 그냥 `sample-page.md` 입니다.
- **front matter 필요** — `layout: page` 로 페이지 레이아웃을 씁니다.
- **`permalink`** — 접근 주소를 직접 지정합니다. 이 페이지는 `/sample-page/` 로 열립니다.
  (minima 테마는 `permalink`가 있는 페이지를 **상단 네비게이션에 자동으로 추가**합니다.)

## 글(post)과의 차이

| | 글(post) | 페이지(page) |
|---|---|---|
| 위치 | `_posts/` | 아무 곳 |
| 파일명 | `YYYY-MM-DD-제목.md` 필수 | 자유 |
| 홈 Posts 목록 | 자동 등록 | 안 뜸 |
| 성격 | 날짜순 블로그 글 | About, 소개 같은 고정 문서 |
