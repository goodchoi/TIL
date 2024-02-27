## Git 깊이 들어가기

### git의 3가지 공간

![git-status.png](img%2Fgit-status.png)

#### working directory

+ untracked : add 된적 없는 파일
+ tracked : add 된적 있고, 변경내역이 있는 파일

#### staging area

+ 커밋을 위한 준비 단계

#### repository

+ 커밋된 상태.

---

### 파일 staging area에서 working directory로

    git restore --staged (파일명)

--staged를 빼면 working directory에서도 제거됨

---

### Reset의 3가지 옵션

+ --soft: repository에서 staging area로 이동
+ --mixed (default): repository에서 working directory로 이동
+ --hard: 수정사항 완전히 삭제

---

### HEAD

특정 브랜치의 가장 최신 커밋

    git checkout HEAD^

+ `^` 갯수 만큼 이전으로 이동

> 💡사실은 이렇다.
>
> checkout 한 후 HEAD상태를 보면
>
> --> HEAD의 현재 위치는 12666d0 beta 1st 이라는 텍스트를 확인할 수 있고,
>
> 브랜치를 확인하면
> --> * (HEAD 12666d0 위치에서 분리됨)
>
> 즉, 커밋id로 생성된 익명의 브랜치에 위치함을 의미한다.

원래대로 돌아가려면? `git switch <branch>`

---

### Fetch vs Pull

+ `fetch`: 원격 저장소의 최신 커밋을 로컬로 가져오기만 함
+ `pull` : 원격 저장소의 최신 커밋을 로컬로 가져와 merge 또는 rebase (default : merge)

`fetch`를 이용하면 원격저장소의 변경사항을 먼저 확인한 뒤 내 로컬 브랜치로 병합할 수 있다는 장점이있다.

---

### 널리 사용되는 컨벤션

```
type: subject

body (optional)
...
...
...

footer (optional)
```

예시

```
feat: 압축파일 미리보기 기능 추가

사용자의 편의를 위해 압축을 풀기 전에
다음과 같이 압축파일 미리보기를 할 수 있도록 함
 - 마우스 오른쪽 클릭
 - 윈도우 탐색기 또는 맥 파인더의 미리보기 창

Closes #125
```

| 타입       | 설명 |
|----------|-----|
| fix      | 버그수정 |
| docs     | 문서 수정|
| style    |공백, 세미콜론 등 스타일 수정|
| refactor |코드 리팩토링|
| perf     |성능 개선|
| test     |테스트 추가|
| chore    |빌드 과정 또는 보조 기능(문서 생성기능 등) 수정|

---

### 보다 세심하게 스테이징하기

    git add -p

이 `-p`옵션을 주고 add를 하면 `hunk`별로 stage할 수 있다! 
즉, 한 파일 내부에서도 쪼개서 커밋하거나 스테이징할 수 있다는 뜻.


    git commit -v

내가 지금 커밋하고자 하는 변경사항을 직접확인.

----

### 커밋하기 애매한 변화 치워두기 Stash
    git stash
작업을 한창 하던중에 핫픽스라던지 오류가 생기면 지금 하던것들을 git에서 다른 공간에 치워둔다.

    git stash pop
원하는 시점, 브랜치에서 다시 적용

--- 

### 커밋 수정하기
    git commit --ammend

커밋의 이름을 변경하거나, 미처 커밋하지못한 파일을 같이 커밋할 수 있다.

#### 과거 커밋 수정

    

```gitexclude
git rebase -i [대상 바로 이전 커밋]

pick 969faa7 캐릭터 귤맨 추가
pick cd6be04 시작메뉴 디자인 변경
pick 15a4384 성능 개선
```
git commit 의 내역이 보여지며 내가 수행할 작업을 지정할 수있다. 원리는 
대상 이전 커밋에서 새로운 분기 브랜치를 생성하여 수정, 삭제, 커밋 병합 등 원하는 작업을 진행한후
다시 원래 브랜치로 `rebase`하는 것이다.


|명령어 | 설명|
|------|----|
|p, pick	|커밋 그대로 두기|
|r, reword	|커밋 메시지 변경|
|e, edit	|수정을 위해 정지|
|d, drop	|커밋 삭제|
|s, squash	|이전 커밋에 합치|

`pick` 부분을 지우고 명령어를 입력한다.

---
### git reset 한 것을 되돌리기 : git reflog
    git reflog 

reflog는 프로젝트가 위치한 커밋이 바뀔 때마다 기록되는 내역을 보여주고
이를 사용하여 reset하기 이전 시점으로 프로젝트를 복구할 수 있다.

---

### fast-forward vs 3-way-merge
merge의 대표적인 두종류를 비교해보자.
1. `fast-forward`

![fast-forward.png](img/fast-forward.png)

두 브랜치의 공통조상을 `base`라고 한다. 이때 main브랜치와 분기해나간 브랜치가 
동일 선상에 있을때 `fast-forward`상태에 있다고한다.

이때, merge를 실행하면 새로운 commit을 생성하지 않는다. 그림에서  main브랜치가 dev1의 
마지막 commit으로 빨리감기 하듯 머지 할 수있기때문에 `fast-forward`라고 명명되었다.

2. 3-way-merge

![3-way-merge.png](img%2F3-way-merge.png)

`base`와 두개의 브랜치가 참조하는 `commit`을 기준으로 병합하기 때문에
`3-way`라고 불린다.

---
### Cherry Pick - 다른 브랜치에서 원하는 커밋만 가져오기

    git cherry-pick [commit]

---
### 다른가지의 잔가지만 가져오기

    git rebase --onto (도착 브랜치) (출발 브랜치) (이동할 브랜치)

---
### 다른 커밋들을 하나로 묶어서 가져오햐
    
    git merge --squash (대상 브랜치)

merge --squash : B 브랜치의 마디들을 복사해다가 한 마디로 모아 A 브랜치에 붙임 (staged 상태로)
---
### Git Flow
협업을 위한 브랜칭전략

| 브랜치           | 용도                 |
|---------------|--------------------|
| main          | 제품 출시/배포           |
| develop       | 다음 출시/배포를 위한 개발 진행 |
| release       | 출시/배포 전 테스트 진행(QA) |
| feature	| 기능 개발              |
|hotfix|	긴급한 버그 수정|



