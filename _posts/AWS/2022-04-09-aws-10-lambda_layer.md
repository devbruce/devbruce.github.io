---
title: "[AWS] Lambda - How to Use Layer"
excerpt: 
last_modified_at: 2022-04-13

categories:
  - AWS

tags:
  - AWS
  - Lambda
  - FaaS
  - layer
  - python

---

# How to Use Lambda Layer

> Document: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>

<br>

## Layer 추가

### 1\. 디렉토리 생성

최상단 디렉토리의 이름은 사용하려는 런타임마다 다르다.  
이 글에서 사용할 런타임은 `Python 3.8` 이므로, 최상단 디렉토리의 이름은 `python`이어야만 한다.  
사용할 런타임이 Python이 아니라면 도큐먼트를 참고하여 런타임에 맞게 변경이 필요하다.

- `python` 디렉토리 생성

```bash
mkdir python
```

<br>

### 2\. 패키지 설치

- `python` 디렉토리에 패키지 설치(예시로 `pandas` 설치)

```bash
pip install pandas -t python
```
`python` 디렉토리를 살펴보면 다음과 같다.
```
python
├── __pycache__
├── bin
├── dateutil
├── numpy
├── numpy-1.22.3.dist-info
├── numpy.libs
├── pandas
├── pandas-1.4.2.dist-info
├── python_dateutil-2.8.2.dist-info
├── pytz
├── pytz-2022.1.dist-info
└── six-1.16.0.dist-info
```

**Note**  
`__pycache__`, `*.dist-info`와 같은 파일은 제거해도 상관없다.
{: .notice--info}

<br>

### 3\. zip 파일 생성

```bash
zip -r py38-pandas.zip ./python
```

**Note**  
zip 파일의 이름은 변경해도 상관없다.  
예시와 같은 `py38-pandas.zip`이 아닌 `lambda_layer_test.zip`과 같이 변경하더라도,  
최상단 디렉토리인 `python`이 압축해제 결과물이기 때문이다.
{: .notice--info}

<br>

### 4\. zip 파일 업로드

**Note**  
zip파일 업로드 최대사이즈는 50.0 MB 이다.  
scikit-learn과 같이 사이즈가 큰 패키지는 직접 업로드가 아닌 S3 활용이 필요하다.
{: .notice--danger}

1\. Add a layer 클릭

![lambda_layer01](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/AWS/images/aws-10-lambda_layer01.png?raw=true){: width="450px"}

2\. 새로운 layer를 생성하는 것이므로 create new를 클릭한다.

![lambda_layer02](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/AWS/images/aws-10-lambda_layer02.png?raw=true){: width="450px"}

3\. 아래 이미지와 같이 이름, 설명 등을 설정하고 zip 파일을 업로드한다.

![lambda_layer03](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/AWS/images/aws-10-lambda_layer03.png?raw=true){: width="450px"}

4\. 생성한 layer를 해당 lambda function에 추가한다.

![lambda_layer04](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/AWS/images/aws-10-lambda_layer04.png?raw=true){: width="450px"}

5\. 이제 pandas를 사용할 수 있게된다.

![lambda_layer05](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/AWS/images/aws-10-lambda_layer05.png?raw=true){: width="450px"}

<br>

그러나 OS 호환 문제로 에러가 발생할 수 있다.  
자세한 내용은 [OS 호환 에러](./#os-호환-에러) 문단 참조

<br><br>

## OS 호환 에러

아래 에러는 numpy에서 발생한 호환 문제이다.

```
[ERROR] Runtime.ImportModuleError: Unable to import module 'lambda_function': 

IMPORTANT: PLEASE READ THIS FOR ADVICE ON HOW TO SOLVE THIS ISSUE!

Importing the numpy C-extensions failed. This error can happen for
many reasons, often due to issues with your setup or how NumPy was
installed.

We have compiled some common reasons and troubleshooting tips at:

    https://numpy.org/devdocs/user/troubleshooting-importerror.html

Please note and check the following:

  * The Python version is: Python3.8 from "/var/lang/bin/python3.8"
  * The NumPy version is: "1.22.3"

and make sure that they are the versions you expect.
Please carefully study the documentation linked above for further help.

Original error was: No module named 'numpy.core._multiarray_umath'
```

[Document](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html)에서 언급하듯이,
컴파일된 바이너리들은 [Amazon Linux](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)와 호환이 되어야한다.  

> 모든 패키지가 위와같은 문제가 발생하는 것은 아니지만, 가능하면 OS 호환성을 고려하자

이러한 문제를 해결하기 위해 EC2에서 Amazon Linux 인스턴스를 할당받아 해결하는 방법도 있겠지만,  
Docker를 활용한다면 로컬에서 간단하게 해결할 수 있다.

<br><br>

## Docker를 활용한 패키지 다운로드

- 1\. `python:3.8.13` 도커 이미지 pull  
(python이 아닌 다른 런타임이라면, 그에맞는 docker image를 찾아 사용하면 된다.)

```bash
docker pull python:3.8.13
```

<br>

- 2\. 다운받은 도커 이미지(`python:3.8.13`)로 컨테이너 생성 후,  
컨테이너 환경에서 pandas 패키지를 python 폴더로 다운로드  

**Note**  
Volume mount 설정시, 관련 디렉토리는 자동생성된다.
{: .notice--info}

```bash
docker run --rm -v $(pwd)/python:/py_pkgs python:3.8.13 pip install pandas -t py_pkgs
```

<br>

- 3\. 필요없는 파일 제거(필수X)

```bash
rm -rf python/__pycache__
```

```bash
rm -rf python/*.dist-info
```

<br>

- 4\. zip 파일로 압축

```bash
zip -r py38-pandas.zip ./python
```

<br>

- 5\. zip 파일 업로드

<br><br>

## Layer 제거

~~AWS Console에서는 생성한 Lambda layer를 제거하는 UI가 없다(?).~~  
AWS Console의 Layers(Lambda Layer 리스트 페이지) 항목에서 제거가 가능하다.  

#### with AWS CLI

```bash
aws lambda delete-layer-version --layer-name ${LayerName} --version-number ${Version}
```

그러나 Layer를 제거하더라도 버전 히스토리가 남는 것으로 보인다.  
만약 `py38-pandas` layer의 version 1을 삭제했다면,  
같은 이름인 `py38-pandas` layer를 새로 생성시 이전 버전 히스토리로 인해 version 2부터 생성가능하다.

<br><br>

## Reference

- <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>
