---
title: "[Ruby] Start Jekyll Blog"
excerpt: 
last_modified_at: 2021-10-27

categories:
  - Ruby

tags:
  - ruby
  - gem
  - jekyll
  - bundler
  - blog

---

# [Ruby] Start Jekyll Blog

## Install

> Ruby 가 설치되어 있다는 전제하에 진행

```bash
gem install bundler jekyll
```

<br><br>

## Start Jekyll

```bash
jekyll new ${PROJECT_NAME}
```

위 명령어를 실행하면, `PROJECT_NAME`으로 디렉토리가 생성된다.  
해당 디렉토리 하위에는 블로그 기본파일들과 Gemfile 로 구성된다.  

<br>

## Bundle

위의 `jekyll new` 명령어로 만들어진 디렉토리로 이동하여 아래의 명령어를 실행한다.  
(Gemfile 에 작성되어있는 라이브러리들을 의존성을 위해 설치한다.)  

```bash
bundle install
```

`GitHub Pages` 를 이용할 경우 (Github 을 통해서 블로그를 개설할 경우) 에는  
Gemfile 을 아래 Use GitHub Pages 설명에 따라 Gemfile 을 편집한 뒤 위의 코드를 실행한다.  

<br><br>

## GitHub Pages

### Edit Gemfile

`./Gemfile`의 내용 중 일부는 아래와 같다.  
주석부분을 살펴보면 If you want to use GitHub Pages,  
remove the "gem "jekyll"" above and uncomment the line below. 이라는 부분이 있다.  
해당 내용에 따라 `gem "jekyll", "~> 4.2.1"` 을 주석처리하고,  
`# gem "github-pages", group: :jekyll_plugins`의 `#`을 제거한다.  

```
...
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
gem "jekyll", "~> 4.2.1"
# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.5"
# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
# gem "github-pages", group: :jekyll_plugins
# If you have any plugins, put them here!
...
```

### bundle install

- 수정한 Gemfile 을 통해 라이브러리 설치

- 해당 명령어를 사용했을 때 버전 충돌이 발생할 경우, `./Gemfile.lock`을 제거 후 명령어 재실행한다.  
(이럴 경우, `./Gemfile.lock`은 재생성된다.)

```bash
bundle install
```

<br>

### Make Repository and push

- GitHub 로그인 이후 `${UserName}.github.io` 라는 이름으로 Repository 생성

- `jekyll new ${PROJECT_NAME}`으로 생성했던 디렉토리의 내용을 `${UserName}.github.io` Repository 로 push

- `https://${UserName}.github.io/` 로 접속하여 생성된 블로그 확인가능

<br><br>

## Local Server 띄우기

```bash
bundle exec jekyll serve
```

위의 명령어를 통해 로컬서버를 띄울 수 있다.  
이를 통해 GitHub에 직접 push 하지않고 로컬에서 여러가지 테스트를 진행해볼 수 있다.  

만약 `require': cannot load such file -- webrick (LoadError)`과 같은 에러가 발생할 경우  
다음 명령어를 사용 후 재시도

```bash
bundle add webrick
```

<br><br>

## Reference

- [Jekyll](https://jekyllrb.com/)
