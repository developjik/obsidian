---
sticker: lucide//github
tags:
  - git
  - github
date: 2024-04-07
---

git에서 한 브랜치에서 다른 브랜치로 합치는 방법은 Merge와 Rebase다.

## `git rebase`
- Merge와 Rebase의 실행결과는 같지만 커밋 히스토리가 달라진다.
- Merge는 쉽고 안전하지만 커밋히스토리가 지저분할 수 있다 반면 Rebase는 잘 모르고 사용할 경우 위험할 수 있어 까다롭지만 커밋히스토리를 깔끔하게 관리할 수 있다.
- Rebase는 base를 새롭게 설정한다는 의미로 이해하면 좋다.
- `$ git rebase [newbase]`

그럼 **두 개의 브랜치로 나뉘어진 커밋 히스토리가 있는 상황**에서 `Merge` 와 `Rebase`를 비교해 본 후 `Rebase`에 대해 좀 더 자세히 알아보겠습니다.

# Rebase와 Merge의 차이점

Merge로 통합하기![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2F40b844b8-aca4-4fda-a90f-9e6aa994f033%2Fimage.png)experiment & master 두 브랜치를 합치는 가장 쉬운 방법은 merge 명령을 이용해 3-way Merge로 새로운 커밋을 만들어내는 것입니다.  이 때 내부적으로 공통조상인 C2를 이용하게 됩니다.

![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2F36ec664c-c5ed-4872-b656-e7a93628292d%2Fimage.png)

Rebase로 통합하기
experiment & master 두 브랜치가 나뉘어 있는 아까와 같은 상황에서 시작합니다.

![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2Fd3f91e4a-cbc0-44d4-9ae5-dedea55cca55%2Fimage.png)

`experiment` 브랜치로 이동해 master를 base삼아 Rebase 하겠다는 의미입니다.

```bash
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

그러면 내부에서는 master가 base가 되고, C3과 C4의 차이를 임시 저장하는 공간에 저장합니다. 이 임시저장 공간을 **`Patch`**라고 합니다.

- 공통 커밋(**C2**)에서 시작해서 현재 체크아웃한 experiment 브랜치가 가리키는 커밋까지 `diff`를 차례대로 만들어 `Patch`에 저장
- experiment브랜치가 master브랜치를 가리키게 함
- C3에 Patch를 순서대로 적용  
    ![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2F8c779a0b-ce4b-4760-97c9-9f68bca74656%2Fimage.png)

커밋들이 여러갈래로 있었던 Merge와 다르게 커밋 히스토리가 한 줄로 깔끔하게 정렬된 것을 볼 수 있습니다.

이제 마지막으로 master브랜치를 `Fast-forward` 시킵니다.

```null
$ git checkout master
$ git merge experiment
```

![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2F777b0f21-ae9d-400e-9265-6adc77188ed0%2Fimage.png)

Merge의 결과에서의 `C5`가 위 사진의 `C4'`는 내용이 같습니다.  
결과적으로 봤을 때는 서로 다를게 없습니다. 하지만 Rebase가 좀 더 깨끗한 히스토리를 만듭니다.  
Rebase는 그래서 보통 리모트 브랜치에 커밋을 깔끔하게 적용하고 싶을 때 사용합니다.  
또 다른 차이점은 Rebase는 브랜치의 변경사항 Patch를 이용한다는 점, Merge의 경우는 두 브랜치의 최종 결과만을 가지고 합친다는 점입니다.

# Rebase 활용

아래의 상황을 가정해보겠습니다.

- Release된 master브랜치
- 개발중인 server브랜치
- 개발중인 client브랜치

이렇게 세 브랜치가 있습니다.  
그런데 client 브랜치를 급하게 릴리즈버전에 업데이트 한다고 할 때, `C8`과 `C9`는 Merge되어야 합니다.  
이런 경우 server브랜치와 server커밋 히스토리에 영향을 주지않고 `Rebase`할 수 있는 옵션이 있습니다.

![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2F7178e967-7ae6-4e67-b411-b560db68f982%2Fimage.png)

```null
$ git rebase --onto master server client
```

`git rebase --onto [newbase] [upstream] [branch]` 의 형식입니다.

- newbase인 `master`
- upstream 과 branch의 공통조상 `C3`
- 대상 브랜치인 `client`

이 세개를 이용한 명령입니다.

![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2F7939a671-3be2-4a5d-84c8-76dfac242664%2Fimage.png)

이번에도 역시 `Fast-forward` 시켜줍니다.

```null
$ git checkout master
$ git merge client
```

![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2Fd0acc912-7d15-45e1-9193-41291d75d24e%2Fimage.png)

성공적으로 client브랜치의 수정사항을 master브랜치에 적용했습니다.  
마지막으로 serve도 포함시킵니다.

```null
$ git rebase master server
$ git checkout master
$ git merge server
```

![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2Ffa52d244-a70d-454c-86af-d37e1ad52293%2Fimage.png)

필요없어진 브랜치 `client`와 `server`를 삭제합니다.

![](https://velog.velcdn.com/images%2Fkwonh%2Fpost%2F0ed67f89-0b76-4580-bd2a-a7a31f6a553d%2Fimage.png)

*_커밋 히스토리가 직렬로 깔끔하게 정리된 걸 볼 수 있습니다 *_

# Rebase의 위험성

협업을 하고있고 언제 사용할지 모르겠다면 push하기전에만 rebase를 사용하세요!

- 이미 공개해서 사람들이 사용하는 커밋을 Rebase하면 반드시 문제가 생긴다는 것입니다.
- 협업 없이 혼자 사용하는 경우는 문제될 것이 없습니다.
- `git pull` 명령을 실행할 때 기본적으로 `--rebase` 옵션이 적용되도록 pull.rebase 설정을 추가할 수 있습니다. `git config --global pull.rebase true` 명령으로 추가합니다.

---

여기까지 입니다.  
시리즈에 포스팅한 내용들은 Github을 사용하기 전에 알면 좋을 Git에 대한 내용 정리였습니다.

다음 포스팅은 Github에서의 협업 워크플로를 위해 사용하는 `Pull Request`에 대해 알아보게습니다.