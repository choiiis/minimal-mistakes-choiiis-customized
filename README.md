# hyeonil's blog

[Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 테마(gem 방식, v7.6) 기반 기술 블로그 — https://hyeonil98.github.io

## 로컬 실행

```bash
bundle install
bundle exec jekyll serve      # http://127.0.0.1:4000
```

## 구조

| 경로 | 내용 |
| --- | --- |
| `_config.yml` | 사이트 설정 (제목, 언어, 댓글 utterances 등) |
| `_posts/` | 글 (`YYYY-MM-DD-slug.md`) |
| `_drafts/TEMPLATE.md` | 새 글 템플릿 + 작성 가이드 |
| `_tabs/` | 사이드바 탭 (About, Portfolio, Categories, Tags, Archives) |
| `_data/contact.yml` | 사이드바 하단 연락처 아이콘 |
| `assets/css/jekyll-theme-chirpy.scss` | 커스텀 스타일 (Pretendard 폰트) |
| `_includes/metadata-hook.html` | `<head>` 에 추가할 태그 |
| `assets/img/` | 아바타, 파비콘, 글 이미지 |

테마 파일은 gem 안에 있으므로 업데이트는 `Gemfile` 의 버전만 올리면 된다.
레이아웃을 바꾸고 싶으면 gem 의 해당 파일을 같은 경로로 복사해서 덮어쓴다 (`bundle info --path jekyll-theme-chirpy`).
