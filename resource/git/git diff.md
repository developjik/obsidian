---
sticker: lucide//github
tags:
  - git
  - github
date: 2024-03-31
---
## `git diff` 란?

`git diff` 명령어는 Git 저장소에서 변경된 내용을 비교하는 데 사용됩니다. 
주로 다음과 같은 두 가지 방법으로 사용됩니다:

1. **작업 디렉토리와 인덱스(Staging Area) 간의 차이 비교**: 이 명령어를 사용하여 작업 디렉토리의 변경 사항과 인덱스(Staging Area)에 있는 변경 사항 사이의 차이를 비교할 수 있습니다. 이것은 아직 커밋되지 않은 변경 사항을 검토할 때 유용합니다.

2. **커밋 간의 차이 비교**: 두 개의 커밋 또는 브랜치 간에 차이를 비교할 수 있습니다. 이를 통해 어떤 변경 사항이 어떤 시점에서 일어났는지 이해할 수 있습니다.

일반적으로 `git diff` 명령어는 다음과 같은 형식을 가집니다:

```
git diff [<옵션>] [<commit/branch/tag> [<commit/branch/tag>]]
```

예를 들어:

- `git diff`: 작업 디렉토리와 인덱스 사이의 차이를 보여줍니다.
- `git diff HEAD`: 작업 디렉토리와 최신 커밋 사이의 차이를 보여줍니다.
- `git diff --staged`: 인덱스와 최신 커밋 사이의 차이를 보여줍니다.
- `git diff commit1 commit2`: 두 개의 커밋 간의 차이를 보여줍니다.

`git diff` 명령어를 실행하면 변경된 파일과 해당 변경 사항이 표시됩니다. 
이는 라인 수준의 변경, 추가된 파일, 삭제된 파일 등을 포함할 수 있습니다.

## `git diff` 예제

1. **작업 디렉토리와 인덱스(Staging Area) 간의 차이 확인**:

```
git diff
```

이 명령은 작업 디렉토리의 변경 사항과 인덱스(Staging Area)에 있는 변경 사항 사이의 차이를 보여줍니다.

2. **인덱스(Staging Area)와 최신 커밋 간의 차이 확인**:

```
git diff --staged
```

이 명령은 인덱스(Staging Area)에 있는 변경 사항과 최신 커밋 간의 차이를 보여줍니다.

3. **최신 커밋과 작업 디렉토리 간의 차이 확인**:

```
git diff HEAD
```

이 명령은 최신 커밋과 현재 작업 디렉토리 간의 차이를 보여줍니다.

4. **두 커밋 간의 차이 확인**:

```
git diff <commit1> <commit2>
```

`<commit1>`과 `<commit2>` 사이의 변경 사항을 비교합니다.

5. **변경된 파일의 이름만 확인**:

```
git diff --name-only
```

이 명령은 변경된 파일의 이름만 표시합니다.

6. **변경된 파일의 이름과 상태 확인**:

```
git diff --name-status
```

이 명령은 변경된 파일의 이름과 상태(추가된, 수정된, 삭제된 등)를 표시합니다.

7. **커밋 간의 특정 문자열을 찾기**:

```
git diff -S"search_string"
```

이 명령은 지정된 문자열이 추가되거나 삭제된 커밋을 찾습니다.

8. **정규 표현식을 사용하여 변경된 내용 찾기**:

```
git diff -G"regex_pattern"
```

이 명령은 변경된 내용 중에서 지정된 정규 표현식과 일치하는 라인을 찾습니다.

이렇게 `git diff` 명령어를 활용하여 다양한 변경 사항을 비교하고 탐색할 수 있습니다.

## `git diff` 옵션

`git diff` 명령어에는 다양한 옵션이 있습니다. 주요 옵션들은 다음과 같습니다:

1. `-U<n>`, `--unified=<n>`: 변경된 파일의 컨텍스트를 설정합니다. `<n>`은 변경된 라인 주변에 표시할 추가 컨텍스트 라인 수를 나타냅니다.

2. `--color[=<when>]`: 출력에 컬러를 적용합니다. 기본적으로 Git은 터미널에 컬러를 적용합니다.

3. `--no-color`: 컬러를 비활성화합니다.

4. `-R`, `--reverse`: 변경 사항을 반대로 표시합니다. 즉, 이전 커밋에서 현재 작업 디렉토리로 변경된 내용을 보여줍니다.

5. `--no-renames`: 이름 변경을 감지하지 않고 변경된 파일만 표시합니다.

6. `--name-only`: 변경된 파일의 이름만 표시합니다.

7. `--name-status`: 변경된 파일의 이름과 상태(추가된, 수정된, 삭제된 등)를 표시합니다.

8. `--cached`, `--staged`: 인덱스(Staging Area)와 현재 커밋 사이의 차이를 보여줍니다.

9. `--no-index`: Git 저장소가 아닌 두 개의 디렉토리 또는 파일 간의 차이를 표시합니다.

10. `-S<string>`, `--pickaxe-regex=<string>`: 지정된 문자열을 포함하는 라인이 추가되거나 삭제된 커밋을 찾습니다.

11. `-G<regex>`, `--pickaxe-regex=<regex>`: 변경된 내용 중에서 지정된 정규 표현식과 일치하는 라인을 찾습니다.

이 외에도 다양한 옵션이 있으며, `git diff --help` 명령어를 사용하여 모든 가능한 옵션을 확인할 수 있습니다.