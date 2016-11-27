---
title: Hexo 검색 달기
date: 2016-10-25 23:31:47
categories:
- blog
tags:
- hexo
- 검색
- search
- hexo-generator-search
---

### 검색을 달자
hexo 로 블로그를 새로 만들고, 
이 사이트의 search 메뉴에 적용되어 있는 검색 기능을 설정하는 방법이다.
<!-- more -->

algolia나 Swiftype 같은 써드파티 검색 서비스를 사용하지 않고, 
내부 search.xml 파일을 검색하는 방식이다.
[hexo-generator-search](https://www.npmjs.com/package/hexo-generator-search) 모듈을 사용하고, [tranquilpeak](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak) 테마를 기준으로 작성하였다.

[이 포스트](http://jeffworkshop.com/2016/01/01/hexo-Local-Search/) 내용에 설명을 덧붙인 글이다.

## 설정

#### 사이트 생성 & tranquilpeak 테마 적용
hexo 블로그 폴더(blog) 생성하고, 검색 모듈을 설치한다
```sh
hexo init blog
cd blog
npm install hexo-generator-search --save
```

[테마 파일](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak/releases/download/v1.9.1/hexo-theme-tranquilpeak-built-for-production-1.9.1.zip)을 다운받아서 압축 해제하고, 
폴더 이름을 hexo-theme-tranquilpeak-built-for-production-1.9.1 에서 tranquilpeak로 변경한다. 
링크에 파일 다운이 안되면 [github](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak) 에서 받자. 일반 사용자(users)용으로 받아야 한다.
폴더 구조가 *blog/themes/tranquilpeak* 가 되도록 tranquilpeak 폴더를 blog/themes/로 이동시킨다. 

hexo 테마를 tranquilpeak로 변경하기 위해 blog 폴더 안의 _config.yml 파일의
```yaml blog/_config.yml(before)
theme: landscape
```
위 내용을 찾아서
```yaml blog/_config.yml
theme: tranquilpeak
```
으로 변경한다.


#### 검색 설정
blog/themes/tranquilpeak/_config.yml 파일의 search: 부분을 변경한다.
```yaml blog/themes/tranquilpeak/_config.yml(before)
search:
    title: global.search
    url: /#search
    icon: search
    # `open-algolia-search` classes are used to open algolia search modal
    class: open-algolia-search
```
위 내용을 찾아서
```yaml blog/themes/tranquilpeak/_config.yml
search:
    title: global.search
    url: /search
    icon: search
```
으로 변경한다.

검색 모듈 사용을 위해 blog/_config.yml 파일의 맨 아래에 다음 3줄을 추가한다.
```yaml blog/_config.yml
search:
    path: search.xml
    field: post
```

검색창 레이아웃이 될 파일을 생성하자.
blog/themes/tranquilpeak/layout 폴더에 search.ejs 파일을 새로 만들고 아래 내용을 복사+붙여넣기 한다.
```blog/themes/tranquilpeak/layout/search.ejs
<div id="site_search" class="main-content-wrap">
  <input type="text" id="local-search-input" name="q" results="0" placeholder="search..." 
  class="form-control input--xlarge" autofocus="autofocus"/>
  <div id="local-search-result"></div>
</div>
```

다음은 검색 기능을 수행할 javascript 함수 작성이다.
blog/themes/tranquilpeak/source/assets/js 폴더에 search.js 파일을 새로 만들고 아래 내용을 복사+붙여넣기 한다.
``` javascript blog/themes/tranquilpeak/source/assets/js/search.js
var searchFunc = function(path, search_id, content_id) {
    'use strict';
    $.ajax({
        url: path,
        dataType: "xml",
        success: function( xmlResponse ) {
            // get the contents from search data
            var datas = $( "entry", xmlResponse ).map(function() {
                return {
                    title: $( "title", this ).text(),
                    content: $("content",this).text(),
                    url: $( "url" , this).text()
                };
            }).get();
            var $input = document.getElementById(search_id);
            if (!$input) return;
            var $resultContent = document.getElementById(content_id);
            $input.addEventListener('input', function(){
                var str='<ul class=\"search-result-list\">';                
                var keywords = this.value.trim().toLowerCase().split(/[\s\-]+/);
                $resultContent.innerHTML = "";
                if (this.value.trim().length <= 0) {
                    return;
                }
                // perform local searching
                var numOfPostFound = 0; // keeping track of # of result
                datas.forEach(function(data) {
                    var isMatch = true;
                    var content_index = [];
                    var data_title = data.title.trim().toLowerCase();
                    var data_content = data.content.trim().replace(/<[^>]+>/g,"").toLowerCase();
                    var data_url = data.url;
                    var index_title = -1;
                    var index_content = -1;
                    var first_occur = -1;
                    // only match artiles with not empty titles and contents
                    if(data_title != '' && data_content != '') {
                        keywords.forEach(function(keyword, i) {
                            index_title = data_title.indexOf(keyword);
                            index_content = data_content.indexOf(keyword);
                            if( index_title < 0 && index_content < 0 ){
                                isMatch = false;
                            } else {
                                if (index_content < 0) {
                                    index_content = 0;
                                }
                                if (i == 0) {
                                    first_occur = index_content;
                                }
                            }
                        });
                    }
                    // show search results
                    if (isMatch) {
                        numOfPostFound += 1; // keeping track of # of results
                        str += "<li><a href='"+ data_url +"' class='search-result-title'>"+ data_title +"</a>";
                        var content = data.content.trim().replace(/<[^>]+>/g,"");
                        if (first_occur >= 0) {
                            // cut out 100 characters
                            var start = first_occur - 20;
                            var end = first_occur + 80;
                            if(start < 0){
                                start = 0;
                            }
                            if(start == 0){
                                end = 100;
                            }
                            if(end > content.length){
                                end = content.length;
                            }
                            var match_content = content.substr(start, end); 
                            // highlight all keywords
                            keywords.forEach(function(keyword){
                                var regS = new RegExp(keyword, "gi");
                                match_content = match_content.replace(regS, "<em class=\"search-keyword\">"+keyword+"</em>");
                            });
                            
                            str += "<p class=\"search-result\">" + match_content +"...</p>"
                        }
                        str += "</li>";
                    }
                });
                str += "</ul>";
                // attaching a summary of searching result
                if (numOfPostFound > 0) {
                    if (numOfPostFound > 1) {
                        summary = numOfPostFound + " posts found";
                    } else {
                        summary = numOfPostFound + " post found";
                    }
                } else {
                    summary = "Nothing found";
                }
                var summary = "<p class=\"text-xlarge text-color-base archieve-result search-result-summary\">" + summary + "</ul>";
                $resultContent.innerHTML = summary + str;
            });
        }
    });
}
```

위에서 작성한 함수를 tranquilpeak테마에 추가하자.
blog/themes/tranquilpeak/layout/_partial/script.ejs 파일을 열고 `<!--SCRIPTS END-->` 가 있는 라인을 찾아서 바로 **윗줄**에 다음 코드를 추가한다.
``` javascript blog/themes/tranquilpeak/layout/_partial/script.ejs 
<script type="text/javascript">      
     var search_path = "<%= config.search.path %>";
     if (search_path.length == 0) {
     	search_path = "search.xml";
     }
     var path = "<%= config.root %>" + search_path;
     searchFunc(path, 'local-search-input', 'local-search-result');
</script>
```

다음은 검색창에 입힐 css파일을 만들자.
blog/themes/tranquilpeak/source/assets/css/ 폴더에 search.css 파일을 새로 만들고 아래 내용을 복사+붙여넣기 한다.
``` css blog/themes/tranquilpeak/source/assets/css/search.css
ul.search-result-list {
  padding-left: 10px;
}
a.search-result-title {
  font-weight: bold;
}
p.search-result {
  color=#555;
  word-wrap: break-word;
}
em.search-keyword {
  border-bottom: 1px dashed #4088b8;
  font-weight: bold;
  color: #ff0000;
  font-style: normal;
}
```
방금 생성한 css 파일을 검색 페이지에 적용하기 위해
blog/themes/tranquilpeak/layout/_partial/head.ejs 파일을 열고
파일의 맨 아랫줄`</head>`의 바로 **윗줄**에 다음 내용을 추가한다.
``` javascript blog/themes/tranquilpeak/layout/_partial/head.ejs
<%- css('assets/css/search.css') %>
```
검색 적용은 끝~!

#### 검색페이지 확인
확인을 위해 검색 페이지를 생성하고 서버를 구동해보자.
``` sh
hexo new page "search"
```
위 명령어를 실행하면 검색 페이지(blog/source/search/index.md)가 생성된다. 
파일을 열어서 내용을 전부 삭제하고 아래 내용으로 복사+붙여넣기 한다.
__위에서 만든 search.ejs 파일을 검색 페이지의 레이아웃으로 지정하는 코드인
`layout: "search"`가 필요하다.__
```md
---
title: "search"
layout: "search"
comment: false
---
```

#### 서버 구동
이제 hexo 서버를 실행해서 구동된 주소(기본값은 localhost:4000)로 접속 한 뒤, 
search 메뉴를 클릭하면 검색 페이지를 확인할 수 있다.
``` sh
hexo server
```