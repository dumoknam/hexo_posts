---
title: Hexo 포스트 소스파일 github 연동 (Edit 버튼 달기)
date: 2017-05-07 21:42:42
tags:
- hexo
- github
- jade
---

### edit 버튼을 달자
포스트를 별도의 github repository에 관리하고,
포스트 상세페이지에서 edit 버튼을 달아서
블로그에서 github의 edit 페이지로 자동 연결하는 방법이다.

hexo 의 gandalfr 테마를 기준으로 작성하였다. (jade 템플릿)
<!-- more -->

## 목표
포스트 타이틀 옆에 연필 아이콘을 달고,
아이콘을 클릭하면 github의 포스트 원본 소스로 이동한다.
이후 포스팅을 통해 원본 소스를 수정, commit 하면 hexo가 자동 재배포되는 기능도 추가할 것이다.

## step
1. head.jade 파일에 font awesome css 파일 추가
2. _config.yml 파일에 github repo 주소 추가
3. post 타이틀에 링크 추가

간단하다. 차례대로 해보자.

### head.jade
themes/gandalfr/layout/partial/head.jade 파일을 열고, 세 줄의 link 아래에 다음 코드를 추가하자.
```jade themes/gandalfr/layout/partial/head.jade
link(rel="stylesheet", href=url_for("https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css"))
```

### _config.yml
블로그 루트 디렉토리의 _config.yml 파일 맨 아래에 github repository 주소를 추가하자.
본인 id와 repository로 변경하자.
이 블로그의 설정된 예시는 아래와 같다.

```yaml _config.yml
# github post directory
github_post_edit_path : https://github.com/dumoknam/hexo_posts/edit/master/
# github_post_edit_path : https://github.com/본인id/본인repository/edit/master/
```

### post.jade
포스트 레이아웃 파일에 링크 아이콘을 달자.
themes/gandalfr/layout/mixins/post.jade 파일을 열고, 제일 아래에 45 라인쯤 부터 시작하는 mixin post(item)을 다음 코드로 변경하자

```jade themes/gandalfr/layout/mixins/post.jade
//- Post Page
mixin post(item)
    .post
        article.post-block
            h1.post-title
                != item.title + ' '
                - var links = item.path.split('/')
                - var last_value = links[links.length-2]
                a(href=config.github_post_edit_path+last_value+'.md')
                    i.fa.fa-pencil-square

            .tags
                if item.tags
                    for tag in item.tags.toArray()
                        a.tag-title(href=url_for(tag.path))
                            != '#' + tag.name
            +postInfo(item)
            .post-content
                != item.content
```