---
title: "[ETC] Sphinx - Apidoc / Theme"
excerpt: 
last_modified_at: 2021-07-14

categories:
  - ETC

tags:
  - python
  - sphinx
  - documentation
  - apidoc
  - theme
  - sphinx_rtd_theme

---

![sphinx-version-4.1.0](https://img.shields.io/badge/Sphinx-v4.1.0-lightgreen.svg)

# Set Basic Environment

임의의 디렉토리에 테스트를 위한 임시 패키지(aaa)를 생성하고, 아래의 명령어로 sphinx 를 시작한다.  

```bash
$ sphinx-quickstart doc
```

> quickstart 관련 참조 : [[Python] Sphinx - Install / Quickstart](https://devbruce.github.io/python/py-43-sphinx_install,quickstart/)

<br>

`sphinx-quickstart` 질의에서 아래의 설정들을 가정한다.

- 소스파일과 빌드파일 분리 질의에 y 를 선택한 형태

- Project Name : tmp\_project

- Sphinx Extension : `autodoc` 사용

- 나머지는 기본값

<br>

완료하면 디렉토리 구조가 아래와 같이 될 것이다.

```
project
├── aaa ← 임시 패키지
│   ├── __init__.py
│   ├── first_a.py
│   └── second_b.py
└── doc ← Sphinx 파일
    ├── Makefile
    ├── build
    └── source
        ├── _static
        ├── _templates
        ├── conf.py
        └── index.rst
```

<br><br>

# How To Read Package From Sphinx

현재 구축한 환경에서 바로 `doc` 디렉토리에 진입 후 `make html` 을 할 경우에는 aaa 패키지를 인식하지 못한다.  
sphinx 가 aaa 패키지를 인식하기 위해선 아래의 작업이 요구된다.

- `sphinx-apidoc` 으로 aaa 패키지 관련 `.rst` 파일을 source 디렉토리에 생성

- `index.rst` toctree 에 `modules.rst` 추가 (필수X, 권장)

- PYTHONPATH 설정

<br><br>

## sphinx-apidoc

```bash
$ sphinx-apidoc [OPTIONS] -o <OUTPUT_PATH> <MODULE_PATH> [EXCLUDE_PATTERN, …]
```

- `<MODULE_PATH>` 에 지정한 패키지의 `.rst` 파일을 `<OUTPUT_PATH>` 에 생성한다.  

- 해당 명령어로 생성된 파일(toc 파일 제외)이 빌드되기 위해서는 Sphinx 의 Extension `autodoc` 이 필요하다.

<br>

### Output files

- `<PackageName>.rst`

- `modules.rst` (toc 파일)

<br>

### Options

- `-o` : 생성될 파일들이 저장될 경로 (지정한 경로가 없을 경우 경로를 생성)

- `-f` (= `--force`) : 이미 있는 파일일 경우 덮어쓴다.

- `--tocfile` : 생성될 toc 파일의 이름 지정 (기본값 : modules)

- `-T` (= `--no-toc`) : toc 파일을 생성하지 않는다. (`--full` 옵션이 주어질 경우 무시된다.)

- . . .

<br><br>

### Create .rst of aaa package

프로젝트의 루트 디렉토리로 이동한 뒤 아래와 같이 입력한다.

```bash
$ sphinx-apidoc -f -o doc/source aaa
```
\>\>\> `Creating file doc/source/aaa.rst.`  
\>\>\> `Creating file doc/source/modules.rst.`  

`doc/source` 에 aaa 패키지의  `.rst` 파일을 생성한다.

<br>

#### aaa.rst

`doc/source/aaa.rst`

```
aaa package
===========

Submodules
----------

aaa.first\_a module
-------------------

.. automodule:: aaa.first_a
    :members:
    :undoc-members:
    :show-inheritance:

aaa.second\_b module
--------------------

.. automodule:: aaa.second_b
    :members:
    :undoc-members:
    :show-inheritance:


Module contents
---------------

.. automodule:: aaa
    :members:
    :undoc-members:
    :show-inheritance:
```

해당 파일이 패키지의 내용들을 담고 있음을 알 수 있다.

<br>

#### modules.rst

`doc/source/modules.rst`

```
aaa
===

.. toctree::
   :maxdepth: 4

   aaa
```

aaa 패키지의 toc 파일이다.  
해당 파일을 살퍄보면 toctree 에 `aaa` 가 추가되어 있다.  
즉 `aaa.rst` 을 참조하고 있음을 알 수 있다.  

<br><br>

### Add modules to toctree of index

이제 이 파일을 `index.rst` 의 toctree 에 추가하여 홈화면에  
toctree 형태로 나타나도록 해보자.

> 추가하지 않을 경우 홈화면의 toctree 에 나타나지 않으며 경고메세지가 나타난다.  
> 해당 경고를 무시하고 toctree 에 추가하고 싶지 않을 경우 아래의 코드를 rst 파일 최상단에 추가한다.
> 
> ```
> :orphan:
> ```

<br>

`doc/source/index.rst`

```
.. tmp_project documentation master file, created by
   sphinx-quickstart on day mmm dd --:--:-- 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to tmp_project's documentation!
=======================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   modules


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
```

`caption: Contents:` 밑에 **한 줄을 비우고** modules 를 추가한다.

<br><br>

## Set PYTHONPATH

`doc/source/conf.py`

```python
import os
import sys
sys.path.insert(0, os.path.abspath('../..'))
```

aaa 패키지가 위치한 경로를 PYTHONPATH 에 추가한다.  
`../..` 은 `conf.py` 가 위치한 상위의 상위 디렉토리를 의미하며 프로젝트의 루트 디렉토리가 된다.  
해당 위치에 aaa 패키지가 위치하고 있다.

<br><br>

## Result

이제 필요한 작업을 다 했으므로 결과를 보자.  

`doc/` 로 이동한 뒤 아래의 명령어를 터미널에서 입력한다.

```bash
$ make html
```

빌드가 정상적으로 완료되면 `doc/build/html/index.html` 을 실행해보자.  

![py-sphinx_apidoc.png](./images/etc-03-sphinx_apidoc.png?raw=true){: width="650px"}

<br><br>

# Theme

Sphinx 에는 Builtin Theme 과 Third Party Theme 이 존재한다.  
그 중 Third Party Theme 에 해당하는  
`sphinx_rtd_theme` 을 적용하는 방법을 아래에 작성하였다.  

<br>

## Install sphinx\_rtd\_theme

```bash
$ pip install sphinx_rtd_theme
```

<br>

## Set Theme

Sphinx 구성파일 중 `conf.py` 의 변수 (`html_theme`) 를 다음과 같이 변경  

> 기본값으로는 `html_theme = 'alabaster'` 으로 되어있다.

```python
html_theme = "sphinx_rtd_theme"
```

위와 같이 변수를 변경하고 저장한다.  
그리고 `make html` 를 통해 빌드하여 결과를 확인해보자.  

![py-sphinx_apidoc_theme](./images/etc-03-sphinx_apidoc_theme.png?raw=true){: width="650px"}  

테마가 적용된 것을 확인할 수 있다.  

Sphinx 의 다양한 테마는 [Sphinx Themes](https://sphinx-themes.org/) 에서 확인가능하다.

<br><br>

# Reference

- [Sphinx Documentation - sphinx-apidoc](http://www.sphinx-doc.org/en/master/man/sphinx-apidoc.html)

- [Sphinx Documentation - File-wide metadata](https://www.sphinx-doc.org/en/master/usage/restructuredtext/field-lists.html#metadata)

- [Sphinx Documentation - Theming](http://www.sphinx-doc.org/en/master/usage/theming.html)

- [Sphinx Themes](https://sphinx-themes.org/)

- [sphinx\_rtd\_theme](https://github.com/rtfd/sphinx_rtd_theme)
