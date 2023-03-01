---
title: "[ETC] SSH 공개키 인증(PubkeyAuthentication)"
excerpt: 
last_modified_at: 2021-10-11

categories:
  - ETC

tags:
  - ssh
  - PubkeyAuthentication
  - id_rsa
  - authorized_keys
  - ubuntu
  - sshd_config
  - permission

---

# SSH 공개키 인증(PubkeyAuthentication)

## 인증 과정(Authentication Process)

### 서버 인증(Server Authentication)

- 클라이언트가 서버에 처음 접속 시도시 연결을 진행할지 여부를 물어본다.(yes /no)
- yes를 선택시 서버의 `/etc/ssh`에 있는 공개키 값을 클라이언트의 `~/.ssh/known_hosts`에 추가한다.
- 클라이언트에서 난수값 생성 → 해시값으로 변환 및 클라이언트에 저장 → 공개키로 해시값 암호화 → 서버에 전송
- 암호화된 해시값을 전달받은 서버는 비밀키로 복호화 → 클라이언트로 다시 전송
- 클라이언트는 저장해두었던 해시값과 서버로부터 받은 해시값을 비교하여 서버를 인증한다.
 
<br>

### 사용자 인증(User Authentication)

- 비대칭키를 생성한다.(클라이언트, 서버, 제 3서버 등 생성 위치는 상관없다.)
  - `ssh-keygen` 명령어 활용
  - 비밀키(Private key): 클라이언트 소유(일반적으로 `~/.ssh/id_rsa`가 기본값)
  - 공개키(Public key): 키 값을 서버의 `~/.ssh/authorized_keys`에 추가  
  (`~/.ssh/authorized_keys`는 여러개의 키값을 가질 수 있다.)

- 서버에서 난수값 생성 → 해시값으로 변환 및 서버에 저장 → 공개키로 해시값 암호화 → 클라이언트에 전송
- 암호화된 해시값을 전달받은 클라이언트는 비밀키로 복호화 → 서버로 다시 전송
- 서버는 저장해두었던 해시값과 클라이언트로부터 받은 해시값을 비교하여 유저를 인증한다.

<br><br>

## Ubuntu SSH 공개키 인증(PubkeyAuthentication) 설정

### SSH 설치

```bash
sudo apt install openssh-server
```

- 서버에 `openssh-server` 를 설치 후 처음 실행될 때 비대칭키 파일들이 자동 생성된다.
  - `/etc/ssh`에서 생성된 비대칭키 파일들을 암호화 알고리즘별로 확인가능하다.(`*.pub` 이 공개키)
  - `/etc/ssh`에 생성되어있는 비대칭키 파일들은 **서버 인증** 과정에서 사용된다.
  
<br><br>

### 비대칭키 생성

`ssh-keygen` 명령어를 사용하여 비대칭키를 생성한다.  
(아래의 코드는 생성예시)

```bash
ssh-keygen -t rsa
```

- `-t rsa`: rsa 암호화 알고리즘 사용하여 비대칭키 생성

- 공개키(Public key)값은 서버의 `~/.ssh/authorized_keys`에 추가한다.
- 비밀키(Private key)는 클라이언트의 `~/.ssh/id_rsa`로 저장한다.(`~/.ssh/id_rsa`가 ssh 명령어의 `-i`의 기본값이다.)  
(ssh 명령어의 `-i` 옵션으로 파일을 직접 지정할 수 있으므로 `~/.ssh/id_rsa`가 아니더라도 선택가능하다.)

- `~/.ssh` 폴더 및 하위 파일들은 아래의 Recommended Permissions을 참고하여 권한 설정을 해준다.

<br><br>

### sshd_config 파일 설정

- Path: `/etc/ssh/sshd_config`

- SSH 공개키 인증을 사용하기 위해서는 `/etc/ssh/sshd_config`의 특정 값들을 다음과 같이 변경한다.

```
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
```

- `PermitRootLogin`: root 계정으로 로그인 가능여부(보안상 no 권장)
- `PasswordAuthentication `: 암호 기반 인증(Password 방식) 로그인 여부
  - `PasswordAuthentication` 는 `/etc/ssh/sshd_config` 파일의 마지막 line에 기본값으로  
yes 로 지정되어있다.(설정할 때 해당값을 no로 변경하는 것을 권장)

<br><br>

### SSH 접속

```bash
ssh -i ${PrivateKeyPath} -p ${PortNumber} ${USER}@${IP_ADDR}
```

<br><br>

## Recommended Permissions

> Ref: <https://superuser.com/questions/215504/permissions-on-private-key-in-ssh-folder>

```
+------------------------+-------------------------------------+-------------+-------------+
| Directory or File      | Man Page                            | Recommended | Mandatory   |
|                        |                                     | Permissions | Permissions |
+------------------------+-------------------------------------+-------------+-------------+
| ~/.ssh/                | There is no general requirement to  | 700         |             |
|                        | keep the entire contents of this    |             |             |
|                        | directory secret, but the           |             |             |
|                        | recommended permissions are         |             |             |
|                        | read/write/execute for the user,    |             |             |
|                        | and not accessible by others.       |             |             |
+------------------------+-------------------------------------+-------------+-------------+
| ~/.ssh/authorized_keys | This file is not highly sensitive,  | 600         |             |
|                        | but the recommended permissions are |             |             |
|                        | read/write for the user, and not    |             |             |
|                        | accessible by others                |             |             |
+------------------------+-------------------------------------+-------------+-------------+
| ~/.ssh/config          | Because of the potential for abuse, |             | 600         |
|                        | this file must have strict          |             |             |
|                        | permissions: read/write for the     |             |             |
|                        | user, and not accessible by others. |             |             |
|                        | It may be group-writable provided   |             |             |
|                        | that the group in question contains |             |             |
|                        | only the user.                      |             |             |
+------------------------+-------------------------------------+-------------+-------------+
| ~/.ssh/identity        | These files contain sensitive data  |             | 600         |
| ~/.ssh/id_dsa          | and should be readable by the user  |             |             |
| ~/.ssh/id_rsa          | but not accessible by others        |             |             |
|                        | (read/write/execute)                |             |             |
+------------------------+-------------------------------------+-------------+-------------+
| ~/.ssh/identity.pub    | Contains the public key for         | 644         |             |
| ~/.ssh/id_dsa.pub      | authentication.  These files are    |             |             |
| ~/.ssh/id_rsa.pub      | not sensitive and can (but need     |             |             |
|                        | not) be readable by anyone.         |             |             |
+------------------------+-------------------------------------+-------------+-------------+
```
