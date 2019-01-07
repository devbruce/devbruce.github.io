---
title: "[Git] Install / Config"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Git

tags:
  - git
  - install
  - config

---

![OS-macOS](https://img.shields.io/badge/OS-macOS-blue.svg)
![git-version-2.18.0](https://img.shields.io/badge/git-v2.18.0-yellow.svg)

# Install

- brew를 통해 설치

```bash
$ brew install git
```

<br>

- 설치 확인 (버전 확인)

```bash
$ git --version
```

<br>

- Help

```bash
$ git --help
```


<br><br>

# Config

## Form of git config file

```
[<Section1_Name>]
    <Variable> = <Value>
    <Variable2> = <Value2>
[<Section2_Name>]
    <Variable> = <Value>
    <Variable2> = <Value2>
```

<br>

## Option

### edit

config 파일 수정

```bash
$ git config --edit
```

<br>

### list

config 파일에 설정된 변수와 값들을 보여준다.

```bash
$ git config --list
```

<br>

### get

지정된 변수의 값을 반환한다. 

```bash
$ git config --get <Section>.<Variable>
```

아래와 같이 `--get` 을 따로 지정하지 않아도 값을 반환받을 수 있다.

```bash
$ git config <Section>.<Variable>
```

하지만 옵션을 지정해주지 않을 경우 정규식을 이용한 필터링을 사용할 수 없다.

<br>

### add

config 파일에 값을 추가한다. (기존 값이 존재해도 새로 한 줄 추가한다.)

```bash
$ git config --add <Section>.<Variable> <Value>
```

아래와 같이 옵션을 주지않을 경우 기존 값이 새로 지정한 값으로 변경된다.

```bash
$ git config <Section>.<Variable> <Value>
```

<br>

### unset

해당 변수를 설정파일에서 제거한다.

```bash
$ git config --unset <Section>.<Variable>
```

<br>

### remove-section

해당 Section 을 제거한다.

```bash
$ git config --remove-section <SectionName>
```

<br>

### rename-section

Section 의 이름을 변경한다.

```bash
$ git config --rename-section <SectionName> <NewSectionName>
```

<br><br>

## Scope Option

git의 옵션들은 config 파일이 기본적으로 `--local` (Default)로 적용되어 있다.  
`--local` 보다 큰 범위로 지정하고 싶은 경우 옵션값을 지정해서 변경한다.

<br>

### local (Default)

- `--local` 

- 우선순위 1순위

- 해당 리포지터리에 적용

- Config File Path : `.git/config`

<br>

### global

- `--global`

- 우선순위 2순위

- 현재 사용자(User)에게 적용

- Config File Path : `~/.gitconfig` or `~/.config/git/config`

<br>

### system

- `--system`

- 우선순위 3순위

- 모든 사용자와 모든 리포지터리에 적용

- Config File Path : `/etc/gitconfig`

<br><br>

## Example

- User Name 설정

```bash
$ git config [ScopeOption] user.name <UserName>
```

<br>

- User Email 설정

```bash
$ git config [ScopeOption] user.email <UserEmail>
```

<br>

- 기본 Editor 설정 (ex. vi, vim, emacs, nvim...)

```bash
$ git config [ScopeOption] core.editor <EditorName>
```

<br><br>

# Notice

사용정보 (UserName, UserEmail) 설정 후 git 을 사용(commit)하다가  
설정값과 다른 계정을 사용해야 하는 경우가 있다. **(계정변경)**  
git의 config 만 재설정할 경우, 에러가 발생할 수 있다.  
해결방법 중 단순한 방법 한가지를 아래에 작성하였다.

<br>

## Case: macOS

1. `Keychain Access.app` 실행
2. login → `github.com`
3. `github.com` 파일 제거 (git을 사용하면 다시 재생성)
4. git config 재설정

<br><br>

# Reference

- [Git Documentation - Reference - Setup and Config: config](https://git-scm.com/docs/git-config)
