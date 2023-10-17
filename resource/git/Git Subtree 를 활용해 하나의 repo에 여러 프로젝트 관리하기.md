---
sticker: lucide//save
tags:
  - "#git"
  - "#github"
---
> [!NOTE]
> ContentNext 13 Study 하면서 만든 여러 Next 13 Toy Project를 하나의 저장소(repository)에서 관리하고 싶었다.

### 1. Github에 자식 저장소(repository)를 만들고 프로젝트를 하나 올린다.

```shell
git init
git add .
git commit -m "커밋 메시지"
git remote add origin "자식 저장소(repository) 주소"
git branch -M main
git push -u origin main
```

### 2. Github에 부모 저장소(repository)를 만든다.

```shell
git init
git add .
git commit -m "커밋 메시지"
git remote add origin "부모 저장소(repository) 주소"
git branch -M main
git push -u origin main
```

### 3. git subtree를 이용해 부모 저장소(repository)에 자식 저장소(repository)를 추가하기


```shell
git subtree add --prefix=폴더이름 자식저장소(repository)주소 branch이름
```

- 폴더 이름은 github 부모 저장소(repository)에 저장될 폴더 이름이다.

ex) `git subtree add --prefix=chlid1 http://...git main`

### 4. git commit & push

```shell
git commit -m "커밋메시지"

git push origin main
```

### 5. 부모 저장소(repository)에  지정한 폴더이름으로  자식 저장소(repository) 파일 및 폴더들 추가되었는지 확인하기

![[Pasted image 20231017111306.png]]

### (6).  자식 저장소(repository) 삭제하기

자식 저장소(repository)를 삭제해도 부모 저장소(repository)에 올라간 파일 및 폴더들은 삭제 되지 않는다

###  이후 부모 저장소(repository)에  자식 저장소(repository) 파일 및 폴더들 추가하기

```shell
git clone 부모 리포지토리 주소
cd 부모 리포지토리 이름
git subtree add --prefix=폴더이름 자식리포지토리주소 branch이름
git commit -m "커밋 메시지"
git push origin main
```