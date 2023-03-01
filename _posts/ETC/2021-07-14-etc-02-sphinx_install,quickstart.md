---
title: "[ETC] Sphinx - Install / Quickstart"
excerpt: 
last_modified_at: 2021-07-14

categories:
  - ETC

tags:
  - python
  - sphinx
  - documentation
  - install
  - quickstart

---

![sphinx-version-4.1.0](https://img.shields.io/badge/Sphinx-v4.1.0-lightgreen.svg)

# Sphinx Install & Quickstart

## Install

```bash
pip install -U sphinx
```

<br>

## Quickstart

```bash
sphinx-quickstart ${RootPath}
```

- `${RootPath}`: 현재 경로를 기준으로 sphinx 관련파일을 생성한다. (Default : `.`)  
`sphinx-quickstart doc` 이라고 입력할 경우,  
현재 위치에서 `doc` 이라는 디렉토리를 생성 후, 관련 파일을 `doc` 디렉토리에 생성한다.

<br>

위의 명령어를 터미널에 입력하면 여러가지 질의가 나오게 된다.  
> 질의에 의한 설정값들은 `conf.py` 에서 수정가능

<br>

### Questions

```bash
> Separate source and build directories (y/n) [n]
```

소스파일과 빌드파일(HTML 같은 결과물) 디렉토리를 분리할 것인지를 묻는다. (기본값: n)  

#### n 선택할 경우 파일구성

```
doc
├── _build
├── _static
├── _templates
├── conf.py
├── index.rst
├── Makefile
└── make.bat
```

#### y 선택할 경우 파일구성

Build 와 Source 파일이 구분된다. (개인적으로 이 옵션을 선호한다.)

```
doc
├── Makefile
├── make.bat
├── build
└── source
    ├── _static
    ├── _templates
    ├── conf.py
    └── index.rst
```

<br>

이후 다음의 질문들이 나오며 원하는 값으로 입력한다. (`conf.py`로 언제든지 수정가능)

```bash
> Project name:
```

```bash
> Author name(s):
```

```bash
> Project release []:
```

```bash
> Project language [en]:
```

<br><br>


### Sphinx Extensions

`conf.py`에서 다음과 같이 extension을 추가할 수 있다.

```python
extensions = [
    'sphinx.ext.autodoc',
    'sphinx.ext.doctest',
    'sphinx.ext.intersphinx',
    'sphinx.ext.todo',
    'sphinx.ext.coverage',
    'sphinx.ext.imgmath',  # mathjax 와 중복사용 X
    'sphinx.ext.mathjax',  # imgmath 와 중복사용 X
    'sphinx.ext.ifconfig',
    'sphinx.ext.viewcode',
    'sphinx.ext.githubpages',
]
```

<br><br>

#### Build Files

```bash
$ make html
```

`sphinx-quickstart` 명령어를 통해 생성된 `Makefile`로 doc파일을 빌드하는 명령어이다.  

<br>

`Makefile` 을 사용하지 않고 아래 명령어로도 가능하다.  

```bash
$ sphinx-build -b html ${SrcDir} ${BuildDir}
```

빌드를 완료한 뒤 `build/html` 디렉토리에서 Master Document 에 해당하는 `index.html`을 열어보면  
아래의 이미지와 같은 페이지를 확인할 수 있다.

<br>

![etc-sphinx_quickstart](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/ETC/images/etc-02-sphinx_quickstart.png?raw=true){: width="650px"}

<br><br>

## Reference

- [Sphinx](http://www.sphinx-doc.org/en/master/)
- [Sphinx Documentation - quickstart](http://www.sphinx-doc.org/en/master/usage/quickstart.html)
