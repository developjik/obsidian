---
sticker: lucide//github
tags:
  - git
  - nextjs
date: 2024-04-02
---
## `git chery-pick` 란?

`git cherry-pick`은 Git의 명령어 중 하나로, 특정 커밋을 현재 브랜치로 가져오는 기능을 제공합니다. 
이 명령어는 일반적으로 다른 브랜치에서 작업한 특정 변경 사항을 현재 브랜치로 가져오는 데 사용됩니다. 

예를 들어, 다른 브랜치에서 발생한 중요한 수정 사항이 있는데, 이를 현재 작업 중인 브랜치에도 적용하고 싶다면 `git cherry-pick`을 사용할 수 있습니다.

사용법은 다음과 같습니다:

```
git cherry-pick <커밋 해시>
```

여기서 `<커밋 해시>`는 가져오고자 하는 커밋의 고유 식별자입니다. `git log`나 다른 Git 로그 명령어를 사용하여 해당 커밋의 해시를 확인할 수 있습니다.

주의할 점은 `git cherry-pick`을 사용할 때 충돌이 발생할 수 있습니다. 이는 가져오려는 변경 사항이 현재 브랜치의 변경 사항과 충돌할 때 발생합니다. 충돌이 발생하면 충돌을 해결하고 `git cherry-pick --continue` 명령어를 사용하여 계속 진행할 수 있습니다.

`git cherry-pick`은 유용한 도구이지만, 주의해서 사용해야 합니다. 특히, 이 명령을 사용할 때 해당 커밋이 현재 브랜치에 필요한 변경 사항과 어떻게 상호작용하는지 신중하게 검토해야 합니다.

## `git chery-pick` 활용 예제

가정: 여러 개의 브랜치가 있고, 특정 브랜치에서 발생한 변경 사항을 현재 작업 중인 브랜치에 적용해야 합니다.

1. 다른 브랜치에서 변경 사항이 있는지 확인합니다. 예를 들어, `feature-branch` 브랜치에서 발생한 커밋의 리스트를 확인합니다.

    ```sh
    git log feature-branch
    ```

2. `feature-branch` 브랜치에서 가져오고자 하는 특정 커밋의 해시를 확인합니다.

3. 현재 브랜치로 돌아갑니다. 예를 들어, `main` 브랜치로 돌아가는 명령은 다음과 같습니다.

    ```sh
    git checkout main
    ```

4. `git cherry-pick` 명령을 사용하여 변경 사항을 가져옵니다. 예를 들어, `feature-branch`에서 가져온 특정 커밋의 해시는 `<커밋 해시>`라고 가정합니다.

    ```sh
    git cherry-pick <커밋 해시>
    ```

5. 변경 사항이 충돌하지 않으면, cherry-pick이 완료됩니다. 그러나 충돌이 발생할 경우 충돌을 해결하고 `git cherry-pick --continue` 명령을 사용하여 계속 진행합니다.

6. 변경 사항이 적용되면서 커밋이 생성됩니다. 이제 현재 브랜치에 `feature-branch`의 변경 사항이 적용되었습니다.

이러한 기능을 통해 특정 브랜치에서 발생한 중요한 수정 사항을 다른 브랜치로 쉽게 적용할 수 있습니다.

## `git chery-pick` 옵션들

1. **-e, --edit**: 커밋을 cherry-pick하기 전에 커밋 메시지를 편집할 수 있게 에디터를 엽니다.

    ```sh
    git cherry-pick -e <커밋 해시>
    ```

2. **-x**: cherry-pick한 커밋의 원본 커밋 해시를 커밋 메시지에 추가합니다.

    ```sh
    git cherry-pick -x <커밋 해시>
    ```

3. **-n, --no-commit**: 변경 사항을 작업 디렉토리에 적용하지만 커밋하지 않습니다. 이 옵션을 사용하면 변경 사항을 검토하고 추가 변경을 수행할 수 있습니다.

    ```sh
    git cherry-pick -n <커밋 해시>
    ```

4. **-s, --signoff**: cherry-pick을 실행한 사람의 이름과 이메일을 커밋 메시지에 서명합니다.

    ```sh
    git cherry-pick -s <커밋 해시>
    ```

5. **--strategy**: cherry-pick에 사용할 전략을 지정합니다. 기본적으로는 recursive 전략이 사용되지만, 다른 전략도 사용할 수 있습니다. 예를 들어, `-Xtheirs` 옵션을 사용하여 충돌이 발생할 경우 대상 브랜치의 변경 사항을 우선합니다.

    ```sh
    git cherry-pick --strategy=recursive -Xtheirs <커밋 해시>
    ```

6. **--allow-empty**: cherry-pick할 커밋이 빈 커밋인 경우에도 cherry-pick을 허용합니다.

    ```sh
    git cherry-pick --allow-empty <커밋 해시>
    ```

7. **-m, --mainline**: 병합 커밋의 부모 중 하나를 선택합니다. 이 옵션은 병합 커밋에서 수정을 가져오는 경우에 사용됩니다.

    ```sh
    git cherry-pick -m <숫자> <커밋 해시>
    ```

더 자세한 내용은 `git cherry-pick --help` 명령을 사용하여 Git 도움말을 확인할 수 있습니다.