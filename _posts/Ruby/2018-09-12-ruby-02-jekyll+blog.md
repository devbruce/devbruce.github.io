---
title: "[Ruby] Start Jekyll Blog"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Ruby

tags:
  - ruby
  - gem
  - jekyll
  - bundler
  - blog

---

# Install

> Ruby 가 설치되어 있다는 전제하에 진행

```bash
$ gem install bundler jekyll
```

<br><br>

# Start Jekyll

```bash
$ jekyll new <NAME>
```

위 명령어를 실행하면, `<NAME>`에 해당하는 디렉토리가 생성된다.  
그리고 내부에는 블로그를 구성하는 기본파일들과 Gemfile 이 있다.

<br>

## Bundle

위의 `jekyll new` 명령어로 만들어진 디렉토리로 이동하여 아래의 명령어를 실행한다.

```bash
$ bundle install
```

위 명령어를 통해서 Gemfile 에 작성되어있는 라이브러리들을 설치해준다. (의존성관리)  

`GitHub Pages` 를 이용할 경우 (Github 을 통해서 블로그를 개설할 경우) 에는  
Gemfile 을 아래 Use GitHub Pages 의 설명대로 Gemfile 을 편집한 뒤 위의 코드를 실행한다.

<br><br>

### Use GitHub Pages

#### Edit Gemfile

- 11th Line `gem "jekyll", "~> 3.8.3"` 을 주석처리 또는 제거

- 18th Line `gem "github-pages", group: :jekyll_plugins` 주석해제

> Gemfile 을 살펴보면 해당내용이 주석으로 작성되어있다.

<br>

#### Make Repository and push

- Github 로그인 이후 Repository 생성  
New Repository → Repository 이름 `<UserName>.github.io`로 설정

<br>

- `$ jekyll new <NAME>` 으로 생성했던 디렉토리를 `<UserName>.github.io` Repository 로 push

<br>

- `https://<UserName>.github.io/` 로 접속하여 생성된 블로그 확인가능

<br><br>

## Local 작업

```
$ bundle exec jekyll serve
```

위의 명령어를 통해 로컬서버를 띄울 수 있다.  
이를 통해 Github 에 직접 push 하지않고 로컬에서 여러가지 테스트를 진행해볼 수 있다.

<br><br>

# Reference

- [Jekyll](https://jekyllrb.com/)
