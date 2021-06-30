---
title: "[Git] reset, revert, reflog Commands Summary"
excerpt: 
last_modified_at: 2021-06-30

categories:
  - Git

tags:
  - git
  - reset
  - revert
  - reflog

---

# reset, revert, reflog Commands Summary

![git-version-2.29.2](https://img.shields.io/badge/Git-v2.29.2-yellow.svg)

## reset

> [Git Documentation: git-reset](https://git-scm.com/docs/git-reset)

**Commit 참조 방향 (Important!)**  
HEAD → Branch → Commit  
{: .notice--info}

- `git checkout ${CommitHash}`: HEAD가 Branch를 참조하지않고 독립적으로 commit을 직접 참조 (<font color="red">Detached HEAD</font>)
- `git reset ${CommitHash}`: HEAD가 참조 중인 Branch의 참조 commit을 변경

`${Target}`에는 commit hash, tag, branch 등이 가능하다.

```bash
$ git reset ${Target}
```

<br>

- 특정 파일만 reset 적용
  - `--soft`, `--hard` 옵션 사용 불가
  - `--mixed`의 경우, `git reset -- <paths>` 형태로 사용 권장

```bash
$ git reset ${Target} -- ${FilePath}
```

### Options

- `--soft`: Branch의 참조 commit만 변경된다.  
해당 옵션을 주게되면 이전 commit이 가지고 있던 변경사항들은 staging area에 저장되어있는 것을 확인할 수 있다.

<br>

- `--mixed`(Default): Staging area(Index) 리셋  
해당 옵션을 주게되면 이전 commit이 가지고 있던 변경사항들은 working directory에 있는 것을 확인할 수 있다.  

<br>

- `--hard`: Working directory, Staging area(Index) 모두 리셋  
해당 옵션을 주게되면 이전 commit이 가지고 있던 변경사항들은 전부 제거되므로 사용에 주의한다.  
(reflog 명령어를 활용하여 복구 가능하지만 주의하는 것이 좋다.)

<br><br>

## revert

> [Git Documentation: git-revert](https://git-scm.com/docs/git-revert)

특정 commit이 가진 내용을 제거하는 새로운 commit 생성  
`${Target}`에는 commit hash, tag, branch 등이 가능하다.

```bash
$ git revert ${Target}
```

### Options

- `--no-edit`: Commit message editor를 생략한다.
- `-n`(`--no-commit`): 새로운 commit을 바로 생성하지않고, 취소된 내용을 staging area에 추가한다.

<br><br>

## reflog

> [Git Documentation: git-reflog](https://git-scm.com/docs/git-reflog)

reflog(Reference logs)는 local repository의 branch tip(?)과 다른 참조들의 업데이트 사항들을 기록한다.  
따라서 reset이 되기전 commit hash 및 정보과 기록되어 있으므로  
의도치 않게 reset 또는 삭제한 branch를 복구가 가능하다.

```bash
$ git reflog
```

위의 명령어를 통해 복구하고자 하는 commit hash를 찾은 뒤  
아래의 명령어로 제거된 내용을 복구할 수 있다.

```bash
$ git reset --hard ${CommitHash}
```

의도치 않게 삭제한 branch의 경우는 관련 commit hash를 찾은 뒤,  
아래의 명령어로 branch를 복구할 수 있다.

```bash
$ git switch -c ${DeletedBranchName} ${CommitHash}
```
