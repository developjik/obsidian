---
sticker: lucide//save
tags:
  - git
  - github
---
### submodule & subtree 언제 사용할까?

`Git` 저장소 안에 또 다른 저장소가 필요한 경우 사용한다.  

실제로는 여러 개의 개별 저장소이지만 개발자의 로컬에는 하나의 저장소로 관리할 수 있다.

### submodule ?

![[Pasted image 20231017114431.png]]

Git 저장소 안에 다른 저장소가 들어가 있는 개념이다.

상위 저장소에서 submodule을 SHA 값, 하나의 바이너리처럼 취급하기 때문에 병합에 있어 복잡하다.  
저장소가 병합되는 것이 아닌 최신 커밋의 내용으로 교체된다. 또한, 직접 submodule을 업데이트한 뒤 병합 후 푸쉬해야한다.  
  
.gitmodules를 통해 메인 프로젝트에서 sub 프로젝트를 관리할 수 있다.
.gitmodules에는 submodule로 사용할 프로젝트의 저장소 주소와 해당 프로젝트가 위치할 폴더명 등이 적혀있다. 

#### submodule 명령어

- 새로운 서브 프로젝트를 연결하여 서브모듈로 추가하는 명령어

```
# ../{git repo}.git 저장소의 {branch}를 서브모듈로 추가한다.
# -b master는 생략할 수 있다.
git submodule add -b {branch} ../{git repo}.git
```

위의 명령어를 실행하면 해당 저장소, 브랜치의 데이터가 서브 모듈로 추가된다. '.gitmodule' 파일을 확인하면 이에 대한 정보를 볼 수 있다.  
새롭게 서브모듈을 추가하고 나면 해당 정보를 유지하기 위해 메인 프로젝트를 커밋해야한다.  

- 메인 프로젝트에 submodule 정보를 가지고 있는 경우, submodule을 설정 및 업데이트 명령어 

```
# .gitmodules 정보를 기반으로 로컬 환경설정파일이 설정된다.
# .gitmodules 정보를 .git/config에 등록한다.
git submodule init

# 서브모듈의 원격 저장소에서 데이터를 가져오고 현재 스냅샷에서 checkout 해야 할 정보를 가져와 checkout 한다.
# git submodule update --init 커맨드를 통해서 init과 update를 동시에 처리할 수 있다.
git submodule update

# 모든 submodule을 foreach 문으로 돌면서 master branch로 checkout 한다.
# 처음 submodule update를 하게되면 detached HEAD 상태로 어떤 브랜치에도 속하지 않는 상태이다.
git submodule foreach git checkout master

# update --init은 현재 메인 프로젝트에 링크되어 있는 서브 프로젝트의 정보를 가져와 업데이트 한다.
# 그렇기 때문에 서브 프로젝트의 최신 정보가 아닌 메인 프로젝트에 커밋된 당시의 스냅샷을 받아온다.
# update --remote는 서브 프로젝트의 저장소에서 현재 최신 정보를 가져와서 업데이트 하게된다.
# 원격 저장소에 새로운 커밋이 있는 경우 현재 링크를 최신 정보의 링크로 업데이트 한다.
# --remote 옵션을 통해 서브모듈을 최신화 하였다면, 메인 프로젝트를 새로 커밋하여 해당 정보를 저장해야 한다.
# --merge 옵션이 없으면 서브모듈은 다시 detached head 상태가 된다.
git submodule update --remote --merge
```

- 메인 프로젝트에 서브 프로젝트를 수정 명령어
메인 프로젝트에서 서브 프로젝트를 수정하기 위해서는 먼저 서브 프로젝트의 브랜치 상태를 확인해야 한다. submodule update를 통해 서브 프로젝트를 가져온 경우에는 detached HEAD 상태로 어떤 브랜치에도 속하지 않은 상태가 되기 때문에 git checkout master 등을 수행하여 원하는 브랜치로 checkout 해줘야 한다.  
또한 수정 이후 커밋을 하는 순서도 유의해야 한다. 서브 프로젝트의 수정이 있는 경우, 서브 프로젝트를 먼저 커밋한 후에 메인 프로젝트를 커밋해야 한다. 만약 메인 프로젝트를 먼저 커밋하는 경우, 서브 프로젝트의 최신 커밋을 참조하지 않게 되기 때문이다.  
  

```
# submodule의 브랜치 설정 및 최신화를 진행한다.
git checkout master
git pull

# 메인 프로젝트를 push 하기 위해서는 서브 프로젝트를 먼저 push 해야한다.
# 이를 위해서 다음과 같이 옵션을 주어 push 하는 방법들이 있다.

# submodule이 모두 push 된 상태인지 확인하고, 확인 완료되면 메인 프로젝트를 push
git push --recurse-submodules=check

# submodule을 모두 push 하고 성공하면 메인 프로젝트를 push 
git push --recurse-submodules=on-demand
```

> ※ Detached HEAD  
> 
> 보통 브랜치는 특정 커밋의 revision number를 가리키고 HEAD는 이 브랜치를 가리킨다.  
> HEAD -> branch -> commit (revision number) 순으로 commit을 가리키는 상태를 attached HEAD 상태라고 한다. 반면에 HEAD가 브랜치를 통해 간접적으로 commit을 가리키는 것이 아닌 직접적으로 commit을 가리키는 것을 Detached HEAD 라고 한다.  
>   
> git은 브랜치를 통해 커밋들을 관리하기 때문에 Detached HEAD 상태에서는 커밋의 관리가 불가능하다. 그렇기 때문에 git에서는 브랜치를 새로 생성하거나 기존 브랜치를 checkout 하는 식으로 브랜치를 연결하여 커밋을 관리하는 것을 추천한다.

###  subtree ?

![[Pasted image 20231017114501.png]]

`subtree`는 여러 저장소를 통합하는 개념이다. 

상위 저장소에 파일을 직접 추가하고 추적\한다. 
`subtree` 의 파일 및 변경사항도 상위 저장소에 기록된다.  
`subtree` 의 원격 저장소와 `subtree` 를 추가한 저장소의 소스가 달라도 '_subtree merge' 를 활용하여 양쪽의 변경사항을 모두 반영할 수 있다.  

#### subtree 명령어

```shell
# 메인 프로젝트 git clone
git clone {git addr}

# 현재 프로젝트에 subtree로 사용할 원격 저장소 추가
git remote add {repo} {repo URL}

# 원격 저장소 확인
git remote -v

# subtree 추가
# --prefix 옵션으로 서브트리를 클론할 디렉토리를 지정한다
git subtree add --prefix={local directory} {repo} {branch}

# 서버트리를 pull, push 할 때는 prefix 옵션을 주어서 해당 서브트리가 위치한 디렉토리 정보와
# 저장소, 브랜치를 주어서 pull, push를 수행하도록 한다.

# pull
git subtree pull --prefix={local directory} {repo} {branch}

# push
git subtree push --prefix={local directory} {repo} {branch}
```
  
#### subtree 장점
메인 프로젝트에서 서브 프로젝트를 바로 수정하고 `push` 할 수 있다.

#### subtree 단점
자유도가 높기 때문에 메인 프로젝트에서 서브 프로젝트들에 영향을 끼칠 수 있다는 것은 단점이자 개발 시 주의해야할 점이다.  
  

### 3. 차이점

### submodule

- `submodule`은 `CBD` (component-based development)에 적합한 모델이며, 메인 저장소는 다른 컴포넌트들에 의존적이다.  
- `submodule`은 실제 저장소의 파일들을 가지는 것이 아니라 링크로 연결된 것이다.  
- `submodule`은 저장소를 여러개의 저장소로 나눌때 사용한다. 만약 서브모듈에서 변경을 한다면 서브 모듈 안에서 커밋/푸쉬를 한 후에 메인 저장소에서 한번 더 커밋/푸쉬를 해야한다.

### subtree

- `subtree`는 `SBD` (system-based development)에 더욱 가까우며, 모든 저장소는 모든 저장소의 파일들을 포함하고 있어 각 부분을 수정 가능하다.

- `subtree`는 실제 저장소를 복사한 것이다. `subtree`를 사용하면 다른 저장소를 하나의 저장소로 히스토리와 함께 통합할 수 있다. 통합하게 되면 저장소의 크기는 커지지만 코드와 히스토리를 재사용하기에는 더욱 좋다.