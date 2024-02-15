---
title: 깃허브 블로그 생성과 오류 해결
date: 2024-02-15 11:25:00 +0900
categories: [Github Blog, Setting]
tags: [git, github, jekyll, chirpy, theme]
pin: true
math: false
mermaid: false
---

> 깃허브 블로그를 만들어야지.. 만들어야지.. 하고 계~속 미루다가, 올해는 계획표도 짜고 열심히 살아보자 싶어서 기록용으로 제일 중요한 깃허브 블로그를 만들어 보았다. 기념의 첫 포스팅!
> 
> 사실 블로그 만들고 테마 입히는 걸 되게 얕보고 있었는데... 이번에 Chirpy 테마를 적용하면서 이틀동안 삽질을 너무 해서 혹시나 이 글을 보게 되신 분은 나와 같은 오류로 헤매지 않았으면 하여 글을 작성한다.   
> (구글링을 계속 하다보니 다른 jekyll 테마는 잘 적용되는데 유독 이 테마가 적용 시 오류가 많다고 한다.)
> 
> 그래도 성공적으로 적용했으니 된 거 아닐까? 추후엔 코멘트 기능도 달고, jekyll 어드민도 사용 가능하게 넣어보려고 한다!
> 
> *MacOS 기준으로 작성


## Chirpy 테마 적용하기
<hr/>

무엇보다 중요한 게 있다. 바로 [**공식 문서**](https://chirpy.cotes.page/posts/getting-started/)를 참고하여 테마를 적용하는 것이다..!   
chirpy 테마 소스는 공식 문서대로 하지 않고 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 에서 Download ZIP 하여 내 repository 에 붙여 넣었다.   
jekyll 설치, ruby 설치 등은 다른 많은 블로그에서 상세히 설명하고 있으니 따로 적지 않겠다.   
이 글은 공식 문서를 참고하다가, 오류가 발생했을 때 만약 아래의 내용과 동일한 오류라면 참고하면 좋을 것 같다.   
**그리고 gh_pages 브랜치는 최신 chirpy 테마에는 없다!!! 왜 안 생기지 할 필요 없다.**

## ruby 버전 문제
<hr/>

항상 무언가를 설치할 때 혹시 모를 버그가 있을 수 있기 때문에 최신버전은 기피해야 한다. 하지만 설마 괜찮겠지 하고 설치했던 게 독이 되었던.. ruby 버전으로 인해 발생한 오류가 있었다.   
현재 날짜 기준(24.02.14) ruby의 제일 최신 버전이 3.3.0 인데, 이는 2개월 전에 등록된 버전이다.   
ruby의 버전 문제로 계속 발생했던 오류는 다음과 같다.    
<br/>
작업이 완료된 줄 알고 git push를 했는데 내 블로그 화면은 안뜨고 웬 오류메세지만 등장했다.
```
--- layout: home # Index page ---
```
이게 무엇인고...   
git Action 으로 가서 자세한 오류를 확인해보니...
```
An error occurred while installing google-protobuf (3.25.2), and Bundler cannot
continue.

In Gemfile:
  jekyll-theme-chirpy was resolved to 6.4.2, which depends on
    jekyll-archives was resolved to 2.2.1, which depends on
      jekyll was resolved to 4.3.3, which depends on
        jekyll-sass-converter was resolved to 3.0.0, which depends on
          sass-embedded was resolved to 1.70.0, which depends on
            google-protobuf
Error: The process '/opt/hostedtoolcache/Ruby/3.2.2/x64/bin/bundle' failed with exit code 5
```
저 메세지로 구글링을 하면 pages-deploy.yml 파일의 ruby-version을 3.2로 바꾸라는 등의 내용이 나오는데, 나의 경우는 해결이 되지 않았다.   
사실 검색을 하면서도 설마 버전때문은 아니겠지 했었는데 ㅎㅎ   
3.1.3 버전으로 다운그레이드 하고 다시 bundle 을 적용했다. (그래도 3.0 이상 버전을 사용해야 한다.)
```shell
$ rbenv install -l 
$ rbenv install 3.1.3
$ rbenv global 3.1.3
$ bundle update
```
계속 말썽을 부리던 Setup Ruby 단계를 무사히 성공했다.
> ruby 버전 다운그레이드 후 bundle update
{: .prompt-tip }

## bash tools/init 문제
<hr/>

실행을 시켜도 계속 git 오류가 났다.
```
commit unstaged files first, and then run this tool again.
```
처음 등장한 이 오류메세지는 커밋을 하면 사라졌다.   
그런데 다른 오류가 튀어나왔다. 위 오류는 init 파일에서 뱉는 오류라면 아래의 오류는 git에서 뱉는 오류였는데,   
```
fatal: ambiguous argument '': unknown revision or path not in the working tree.
```
음...   
tools 폴더로 이동하면 실행은 되었지만, 기능이 제대로 작동하지 않아서 루트에서 실행을 시켜야 했다.   
구글링을 해봐도 잘 나오지 않았다. 저 '' 가 뭔지.. 어디서 걸린 건지.   
고민하다가 그냥 파일 내용을 뜯기로 했다 ㅋㅋㅋ 터미널에서 vi로 열던, 직접 문서로 열던, 내용은 바로 확인할 수 있다.   
그런데 내용을 보니, 굳이 init 파일을 실행하지 않고 필요한 명령어만 뽑아서 사용하면 될 것 같아서 터미널 창에 직접 실행했다.   
```shell
$ mv .github/workflows/pages-deploy.yml.hook .
$ rm -rf .github
$ mkdir -p .github/workflows
$ mv ./pages-deploy.yml.hook .github/workflows/pages-deploy.yml

$ sed -i.to-delete "s/^img_cdn:.*/img_cdn:/;s/^avatar:.*/avatar:/" "_config.yml"
$ rm -f "_config.yml".to-delete

$ rm -rf _posts/*

$ npm i && npm run build

$ sed -i.to-delete "/^assets.*\/dist/d;/^sw.*\.js/d" ".gitignore"
$ rm -f ".gitignore".to-delete

$ git add -A
$ git commit -m "chore: initialize the environment" -q
```
굿. 잘 된다. 
> 버전에 따라 init 내용이 변경될 수 있으니 복붙은 안된다.
{: .prompt-warning }

## 끝!