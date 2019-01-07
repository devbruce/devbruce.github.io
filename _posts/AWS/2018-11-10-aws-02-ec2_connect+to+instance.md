---
title: "[AWS] EC2 - Connect To Instance"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - AWS

tags:
  - AWS
  - deploy
  - EC2
  - instance
  - pem
  - chmod
  - ssh
  - scp

---

![OS-macOS](https://img.shields.io/badge/OS-macOS-blue.svg)
![AWS-EC2](https://img.shields.io/badge/AWS-EC2-brightgreen.svg)

<br>

이전 포스트 [[AWS] EC2 - Launch Instance (Intro)](https://devbruce.github.io/aws/aws-01-ec2_intro/) 에 이어서  
해당 인스턴스에 접근하는 방법을 작성해보겠다.

<br>

# SSH (Secure Shell)

- Shell 에 원격 접속하기 위한 암호화된 프로토콜

- 기본 포트 : 22

<br><br>

## Connect Using SSH 

인스턴스에 연결하기 위해서 SSH 를 활용한다.

ssh 명령어는 다음과 같은 형태이다.

```bash
$ ssh -i <PrivaeKeyFile> <UserName>@<PublicDNS>
```

<br>

인스턴스를 생성할 때, AMI 로 Ubuntu 를 선택하였다면 각 항목은 아래와 같이 정리할 수 있다.


- `<Username>` : Default 값은 **ubuntu** 이다.  


- `<PublucDNS>` : 본인의 인스턴스를 살펴보았을 때 **퍼블릭 DNS(IPv4)** 의 값이 해당된다.  


- `<PrivateKeyFile>` : 인스턴스 생성 마지막 7단계에서 키페어 생성으로 다운받은 `.pem` 파일이 해당된다.  
**이 때, `.pem` 파일의 접근권한을 `400` 으로 변경하여야한다. (아래의 코드를 터미널에서 입력한다.)**
  

```bash
$ chmod 400 <PrivateKeyFile>
```
PrivateKeyFile 을 user 가 읽기 권한만 가지도록 설정

<br>

### chmod 숫자의 의미

|   | 의미             |
|---|------------------|
| 4 | 읽기 r (Read)    |
| 2 | 쓰기 w (Write)   |
| 1 | 실행 x (Execute) |

- 숫자 `7` → `4+2+1` 로 모든 권한(rwx)이 있다는 의미  

- 숫자 `5` → `4+1` 로 읽기, 실행권한(r-x)이 있다는 의미  

<br>
 
#### 각 자릿수의 의미

|      | 첫번째 자릿수 | 두번째 자릿수 | 세번째 자릿수 |
|------|---------------|---------------|---------------|
| 권한 | User          | Group         | Other         |


<br>

즉 `400` 의미는 아래와 같다.

|      | 첫번째 자릿수      | 두번째 자릿수 | 세번째 자릿수 |
|------|--------------------|---------------|---------------|
| 권한 | User               | Group         | Other         |
| 값   | 4                  | 0             | 0             |
|      | r--                | ---           | ---           |
| 의미 | User 읽기권한 제공 | Group 권한 X  | Other 권한 X  |

<br>

접근권한을 `400`으로 변경하지 않을경우, 해당 PrivateKeyFile 파일을 통해 ssh 접속할 때 **permission denied** 에러가 발생한다.

<br>

ssh 명령어를 입력하면  
*호스트의 신뢰성을 확인할 수 없다는 메세지와 함께 RSA 키 값을 보여준다. 그리고 연결을 계속할것인지*  
메세지가 나타나는데, 연결을 계속하려면 `yes` 를 입력한다.  
명령어에 문제가 없다면 본인이 생성한 ubuntu 인스턴스에 접속하게 된다.  

<br><br>

# SCP (Secure Copy)

- SSH 에서 제공되는 파일 전송 명령어

- SSH 와 동일한 22번 포트를 사용

<br>

## Upload files to instance

```bash
$ scp -i <PrivateKeyFile> <FileFrom> <UserName>@<PublicDNS>:<FileTo>
```

- `<FileFrom>` 에 해당하는 파일을 해당 인스턴스의 `<FileTo>` 의 경로와 이름으로 업로드한다.  

- 디렉토리(폴더)를 업로드할 경우 `<FileFrom>` 앞에 옵션값 `-r` 을 주어야한다.  

- `<FileTo>` 의 시작이 `/` 일 경우 절대경로로 인식된다.  

- `<FileTo>` 의 시작이 `/` 이 아닐경우, `/home/ubuntu` 를 기준으로 상대경로가 된다.  

- 인스턴스에 해당경로가 존재하지 않을경우 에러가 발생한다. (경로에 해당하는 디렉토리를 자동으로 만들어주지는 않는다.)  

<br>

### Notice

`<FileFrom>` 의 파일을 `<FileTo>` 의 경로로 업로드하는 것이 아니다.  
예를들어 `<FileTo>` 의 형태가 `/home/tmp/tmpname.txt` 과 같은 형태일 경우,  
`/home/tmp` 경로에 `tmpname.txt` 이라는 이름으로 파일을 업로드 하는 것이 된다.  
즉 최종적으로 **파일자체의 이름까지 정해서 업로드하는 것**이 되므로 혼동하지말자. (바로 다음 내용인 Download 도 이와 동일한 형태가 된다.)

<br>

## Download files from instance

```bash
$ scp -i <PrivateKeyFile> <UserName>@<PublicDNS>:<FileFrom> <FileTo>
```

`<FileFrom>` 에 해당하는 파일을 `<FileTo>` 의 경로와 이름으로 다운로드한다.

> 디렉토리(폴더)를 다운로드할 경우 `<UserName>@<PublicDNS>:<FromFile>` 앞에 옵션값 `-r` 을 주어야한다.

<br><br>

# Reference

- [AWS UserGuide - Conneting Using SSH](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)
