---
title: "[MLOps] DVC Summary"
excerpt: 
last_modified_at: 2021-11-12

categories:
  - MLOps

tags:
  - mlops
  - dvc
  - git

---

# DVC (Data Version Control)

git이 코드의 버전관리를 위한 도구라면, DVC는 data 관리를 위한 도구이다.  
dvc에서 **git은 버전관리를 위해 사용**되기 때문에 dvc의 필수 요구사항 중 하나이다.  

<br>

## DVC Install

> <https://dvc.org/doc/install>

```bash
pip install dvc
```

사용하려는 remote storage에 따라서 선택적으로 종속성 패키지를 설치한다.  

- `[s3]`, `[azure]`, `[gdrive]`, `[gs]`, `[oss]`, `[ssh]`, `[all]`

<br>

### Example

**`[`, `]`는 정규표현식 문자이기 때문에 `"`로 감싸서 문자열로 변경이 필요하다.**  
**또는 `noglob pip` 명령어를 사용한다.**  
{: .notice--info}

```bash
pip install "dvc[s3]"
```

```bash
noglob pip install dvc[s3]
```

<br><br>

## dvc init

- git 저장소 초기화  
특정 디렉토리를 dvc로 관리하기 위헤서는 먼저 git 저장소로 초기화가 필요하다.  

해당 과정을 skip할 경우, dvc init 이 되지 않는다.  
{: .notice--warning}

```bash
git init
```

<br>

- dvc 저장소로 초기화

```bash
dvc init
```

dvc 저장소로 초기화가 성공하면 다음과 같은 문구가 나타난다.

```
Initialized DVC repository.

You can now commit the changes to git.

+---------------------------------------------------------------------+
|                                                                     |
|        DVC has enabled anonymous aggregate usage analytics.         |
|     Read the analytics documentation (and how to opt-out) here:     |
|             <https://dvc.org/doc/user-guide/analytics>              |
|                                                                     |
+---------------------------------------------------------------------+

What's next?
------------
- Check out the documentation: <https://dvc.org/doc>
- Get help and share ideas: <https://dvc.org/chat>
- Star us on GitHub: <https://github.com/iterative/dvc>
```

<br><br>

## dvc add

DVC에서 data를 tracking 하기 위해서는 git 과 동일하게 add 명령어를 사용한다.  
이 때 git 과 다른 점은, 특정 파일을 add 하게되면 해당 파일이 stating area에 올라가는 것이 아니다.  
대신 `.gitignore`에 해당 원본파일이 추가되고, `${OriginFile}.dvc`가 생성된다.  

즉, git에서는 대량의 무거운 파일을 tracking 할 수 없으므로  
dvc에서 생성해준 `*.dvc`만을 관리하도록 하는 것이다.  

```bash
dvc add ${OriginFile}
```
```
To track the changes with git, run:

        git add ${OriginFile}.dvc .gitignore
```

dvc add 명령어를 입력하게 되면 다음과 같이, git이 track 하도록 하기 위해서  
`git add ${OriginFile}.dvc .gitignore` 를 실행하라고 하는 것을 확인할 수 있다.  

해당 명령어 실행 후, git commit 명령어를 통해 commit을 수행하면 해당 상태를 저장하게 된다.  

<br><br>

## dvc remote add

dvc push를 위해서는 dvc remote 저장소를 먼저 추가해야한다.  
remote 저장소를 추가하는 방법은 storage 마다 다르므로,  
<https://dvc.org/doc/command-reference/remote/add> 를 참고한다.  

<br>

## dvc push

```bash
dvc push
```

지정한 Remote storage로 데이터를 업로드한다.  
업로드된 파일을 확인해보면 원본의 파일형태와 다르지만  
다운로드하여 내용을 확인해보면 동일한 파일임을 확인할 수 있다.  

Remote storage에 업로드되어있는 디렉토리와 파일이름의 이름은 `*.dvc`의 md5 해쉬값의 일부임을 알 수 있다.  
(디렉토리 이름은 md5 해쉬값의 초기값 일부, 파일이름은 나머지 값인 것을 확인가능)  
{: .notice--info}

<br><br>

### dvc pull

```bash
dvc pull
```

`*.dvc`파일을 통해 지정한 Remote storage로부터 데이터를 다운로드한다.  
특정 버전의 data를 download 받고싶은 경우, git의 기능을 활용한다.

<br><br>

## Reference

- [DVC Documentation](https://dvc.org/doc)
