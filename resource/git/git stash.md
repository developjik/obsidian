---
sticker: lucide//github
tags:
  - git
  - github
date: 2024-03-28
---
## `git stash` 란?

`git stash` 명령은 현재 작업 중인 변경 사항을 일시적으로 저장하는 데 사용됩니다. 
이는 작업 중인 변경 사항을 커밋하지 않고 임시로 저장할 수 있게 해줍니다. 
일반적으로 현재 작업 중인 변경 사항을 커밋하기에는 아직 준비가 되지 않았거나, 다른 브랜치로 이동해야 하거나, 다른 작업을 해야 할 때 유용합니다.

`git stash`를 사용하면 변경 사항을 스택에 저장할 수 있습니다. 그 후에는 작업 디렉토리가 이전 커밋의 상태로 돌아갑니다. 이때 저장된 변경 사항은 숨겨진 스택에 보관되어 있습니다.

## `git stash` 사용법

1. **변경 사항 저장**: 현재 작업 중인 변경 사항을 저장합니다.

    ```bash
    git stash
    ```

2. **변경 사항 확인**: 변경 사항을 임시로 저장한 후에는 작업 디렉토리가 깨끗한 상태로 돌아갑니다. 변경 사항을 확인하려면 `git stash list`를 사용합니다.

    ```bash
    git stash list
    ```

3. **변경 사항 적용**: 변경 사항을 다시 적용하려면 `git stash apply` 명령을 사용합니다.

    ```bash
    git stash apply
    ```

    이 명령은 가장 최근에 저장한 변경 사항을 적용합니다. 특정 stash를 적용하려면 `git stash apply stash@{n}`와 같이 stash 인덱스를 지정합니다.

4. **변경 사항 제거**: 변경 사항을 스택에서 제거하려면 `git stash drop` 명령을 사용합니다.

    ```bash
    git stash drop
    ```

    특정 stash를 삭제하려면 `git stash drop stash@{n}`과 같이 stash 인덱스를 지정합니다.

5. **변경 사항 적용 및 제거**: 변경 사항을 스택에서 제거하면서 적용하려면 `git stash pop` 명령을 사용합니다.

    ```bash
    git stash pop
    ```

    이 명령은 가장 최근에 저장한 변경 사항을 적용하고 스택에서 제거합니다.

`git stash`를 사용하면 작업 중인 변경 사항을 안전하게 저장하고 나중에 다시 적용할 수 있습니다. 
이는 특히 변경 사항을 일시적으로 저장하고 다른 작업을 할 때 유용합니다.


## `git stash` 옵션

1. **git stash save**: `git stash` 명령을 사용할 때 이전에 사용되었던 옵션입니다. 현재 변경 사항을 스태시에 저장합니다. `save`는 생략 가능하며, 스태시 메시지를 함께 저장할 수 있습니다.

    ```bash
    git stash save "Your stash message"
    ```

2. **git stash push**: `git stash`와 동일한 역할을 수행하지만, 새로운 스태시를 만들 때 `--`를 사용하여 더 이상 옵션을 사용하지 않고 파일 이름을 지정할 수 있습니다.

    ```bash
    git stash push -m "Your stash message" -- <file1> <file2>
    ```

3. **git stash list**: 저장된 스태시 목록을 확인합니다.

    ```bash
    git stash list
    ```

4. **git stash apply**: 가장 최근에 저장한 스태시를 적용합니다. 스태시는 스택에 남아 있습니다.

    ```bash
    git stash apply
    ```

5. **git stash pop**: 가장 최근에 저장한 스태시를 적용하고 스태시를 스택에서 제거합니다.

    ```bash
    git stash pop
    ```

6. **git stash drop**: 스태시를 제거합니다.

    ```bash
    git stash drop stash@{n}
    ```

7. **git stash clear**: 모든 스태시를 제거합니다.

    ```bash
    git stash clear
    ```

8. **git stash show**: 스태시에 저장된 변경 사항을 보여줍니다.

    ```bash
    git stash show
    ```

9. **git stash branch**: 새 브랜치를 만들고 스태시를 해당 브랜치에 적용합니다.

    ```bash
    git stash branch <branch_name>
    ```

이러한 옵션들을 사용하여 `git stash`를 더욱 효과적으로 활용할 수 있습니다.