## 소개

오늘은 `React`와 `TypeScript`로 구현한 `File Upload Component`를 소개합니다. 이 컴포넌트는 Drag & Drop을 지원하며, 다중 파일 업로드와 파일 타입 제한 기능을 포함하고 있습니다.

## 주요 기능

- 드래그 앤 드롭 지원
- 클릭하여 파일 선택 가능
- 단일/다중 파일 업로드
- 파일 형식 제한 기능
- 최대 파일 개수 제한
- 중복 파일 자동 필터링
- 파일 삭제 기능

## 사용 방법

```tsx
import FileDropZone from './FileDropZone';

function App() {
  const [files, setFiles] = useState<File[]>([]);

  return (
    <FileDropZone
      value={files}
      onChange={setFiles}
      accept=".pdf,.docx"
      max={3}
    />
  );
}
```

## Props 설명

| Prop | 타입 | 필수 여부 | 설명 |
|------|------|-----------|------|
| value | File[] | 필수 | 현재 업로드된 파일 목록 |
| onChange | (files: File[]) => void | 필수 | 파일 목록 변경 시 호출되는 함수 |
| accept | string | 선택 | 허용할 파일 형식 (예: ".pdf,.jpg") |
| max | number | 선택 | 최대 파일 개수 (기본값: Infinity) |

## 특징

### 1. 제어 컴포넌트 패턴

**파일 상태를 부모 컴포넌트**에서 관리하는 `제어 컴포넌트 패턴`을 사용하여 **유연성을 높였습니다**.

### 2. 사용자 경험 개선

- 드래그 중일 때 시각적 피드백 제공
- 파일 형식과 최대 개수 안내
- 간편한 파일 삭제 기능

### 3. 파일 중복 방지

동일한 이름의 파일이 이미 존재할 경우, 자동으로 필터링됩니다.

## 사용 예시

### 단일 PDF 파일 업로드
```tsx
<FileDropZone
  value={files}
  onChange={setFiles}
  accept=".pdf"
  max={1}
/>
```

### 이미지 파일만 허용
```tsx
<FileDropZone
  value={files}
  onChange={setFiles}
  accept="image/*"
  max={5}
/>
```

### 엑셀 파일 업로드
```tsx
<FileDropZone
  value={files}
  onChange={setFiles}
  accept=".xls,.xlsx,.xlsm"
/>
```

## 마무리

이 컴포넌트는 간단하면서도 실용적인 파일 업로드 기능을 제공합니다. TypeScript와 Tailwind CSS를 활용하여 타입 안정성과 스타일링의 편의성을 모두 갖추었습니다.

다음 업데이트에서는 파일 크기 제한, 미리보기 기능, 업로드 진행률 표시 등의 기능을 추가할 예정입니다.