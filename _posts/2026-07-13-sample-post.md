---
layout: article
title: "샘플 글: 이것은 블로그 포스트입니다"
date: 2026-07-13 14:00:00 +0900
categories: [sample]
tags: [sample, post]
---

이 파일은 **블로그 글(post)** 샘플입니다.

## 글(post)이 되는 조건 두 가지

1. **위치** — `_posts/` 폴더 안에 있어야 합니다.
2. **파일명** — `YYYY-MM-DD-제목.md` 형식이어야 합니다.
   이 파일은 `2026-07-13-sample-post.md` 라서 조건을 만족합니다.

여기에 맨 위 front matter(`---` 블록)의 `layout: post` 까지 있으면,
Jekyll이 이 파일을 HTML로 변환하고 **홈페이지의 Posts 목록에 자동으로 올려줍니다.**

## 코드 블록도 됩니다

```ruby
puts "Hello, Jekyll!"
```

> 참고: 파일명의 날짜(`2026-07-13`)와 front matter의 `date` 는 둘 다 있으면
> front matter 쪽이 시각까지 포함해 우선합니다.
