---
title: "[Python] Sphinx - Install / Quickstart"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - sphinx
  - documentation
  - install
  - quickstart

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)
![sphinx-version-1.8.2](https://img.shields.io/badge/sphinx-v1.8.2-yellow.svg)

# Sphinx

Documentation(문서) 제작을 간편하게 해주는 툴  

<br>

## Install

```bash
$ pip install -U sphinx
```

<br>

## Quickstart

```bash
$ sphinx-quickstart <RootPath>
```

- `<RootPATH>` : 현재 경로를 기준으로 sphinx 관련파일을 생성한다.  
(Default : `.`)

위의 명령어를 터미널에 입력하면 여러가지 질의가 나오게 된다.  

> 질의에 의한 설정값들은 `conf.py` 에서 수정가능

<br>

### Separate Directories

```bash
> Separate source and build directories (y/n) [n]
```

소스파일과 빌드파일(HTML 같은 결과물) 디렉토리를 분리할 것인지를 묻는다. (기본값: n)  

<br>

#### n 선택할 경우 파일구성

```
doc
├── _build (Directory)
├── _static (Directory)
├── _templates (Directory)
├── index.rst
├── conf.py
├── Makefile
└── make.bat
```

<br>

#### y 선택할 경우 파일구성

Build 와 Source 파일이 구분됨을 알 수 있다.

```
doc
├── Makefile
├── make.bat
├── build (Directory)
└── source (Directory)
    ├── _static (Directory)
    ├── _templates (Directory)
    ├── conf.py
    └── index.rst
```

파일 구분을 통해서 

<br><br>

### Prefix For Directories

```bash
> Name prefix for templates and static dir [_]:
```

templates 파일과 static 파일의 prefix 를 지정한다. (기본값 `_`)

<br><br>

### Project Information

```bash
> Project name:
> Author name(s):
> Project release []:
> Project language [en]:
```

<br><br>

### Source File Suffix

```bash
> Source file suffix [.rst]:
```

소스파일의 suffix 지정 (파일 확장자)  
기본값은 `.rst` 이며, `.txt` 와 `.rst` 만 문서로 간주한다.  

<br><br>

### Name Of Master Document

```bash
> Name of your master document (without suffix) [index]:
```

기본값 : `index`

<br><br>

### Sphinx Extension

```bash
> autodoc: automatically insert docstrings from modules (y/n) [n]:
> doctest: automatically test code snippets in doctest blocks (y/n) [n]:
> intersphinx: link between Sphinx documentation of different projects (y/n) [n]:
> todo: write "todo" entries that can be shown or hidden on build (y/n) [n]:
> coverage: checks for documentation coverage (y/n) [n]:
> imgmath: include math, rendered as PNG or SVG images (y/n) [n]:
> mathjax: include math, rendered in the browser by MathJax (y/n) [n]:
> ifconfig: conditional inclusion of content based on config values (y/n) [n]:
> viewcode: include links to the source code of documented Python objects (y/n) [n]:
> githubpages: create .nojekyll file to publish the document on GitHub pages (y/n) [n]:
```

- `imgmath` 와 `mathjax` 는 같이 사용할 수 없다. (중복사용불가)

> `imgmath` 를 y 로 추가한 뒤 `mathjax` 를 y 로 추가하면 `imgmath` 는 자동으로 제거된다.

<br>

#### extensions list

위의 질의에서 y 를 택할 경우 아래와 같이 conf.py 의 extensions 리스트에 추가된다.

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

<br>

### Makefile

- makefile 을 통해서 Build 파일(HTML 결과물)을 쉽게 빌드할 수 있다. 

- 마지막 질의

```bash
> Create Makefile? (y/n) [y]:
> Create Windows command file? (y/n) [y]:
```

- Windows 사용자가 아니라면 두번째 질의는 n 을 권장 (y 를 택할시 `make.bat` 파일이 생성된다.)

<br>

#### Build Files

```bash
$ make html
```

위에서 생성한 `Makefile` 을 통해 빌드하는 명령어이다.  
Makefile 을 사용하지 않고 아래 명령어로도 가능하다.  

```bash
$ sphinx-build -b html <SourceDir> <BuildDir>
```

하지만 명령어가 길어지므로 `Makefile` 사용을 권장한다.  
빌드를 완료한 뒤 `build/html` 디렉토리에서 Master Document 에 해당하는 `index.html` 을 열어보자.  

<br>

![py-sphinx_quickstart](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-43-sphinx_quickstart.png?raw=true){: width="650px"}  

<br>

위와같이 기본적인 형태의 Documentation 페이지를 확인할 수 있을 것이다.

<br><br>

# Reference

- [Sphinx](http://www.sphinx-doc.org/en/master/)

- [Sphinx Documentation - quickstart](http://www.sphinx-doc.org/en/master/usage/quickstart.html)
