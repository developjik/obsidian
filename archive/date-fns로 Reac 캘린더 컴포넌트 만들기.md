`React`와 `TypeScript`,  `Tailwind CSS` 그리고 `date-fns` 라이브러리를 사용하여 **재사용 가능한 캘린더 컴포넌트**를 만들어보겠습니다. 

## 준비사항

시작하기 전에 다음 의존성이 설치되어 있는지 확인해주세요:

- React
- TypeScript
- date-fns
- Tailwind CSS
- lucide-react (아이콘용)
---
## 컴포넌트 개요

우리가 만들 캘린더 컴포넌트는 다음 기능을 지원합니다:
- 이전/다음 버튼으로 월 단위 이동
- 날짜 선택과 콜백 함수
- 오늘 날짜 시각적 표시
- 현재 월과 다른 월의 날짜 스타일 구분
- 반응형 그리드 레이아웃
---
## 구현하기

단계별로 구현 과정을 살펴보겠습니다:

### 1. Props 인터페이스 정의

먼저 컴포넌트의 props 인터페이스를 정의합니다:

```typescript
interface CalendarProps {
  value?: Date;         // 선택된 날짜
  onChange?: (date: Date) => void;  // 날짜 선택 시 콜백
  className?: string;   // 추가 CSS 클래스
}
```

### 2. 캘린더 구조

캘린더는 세 가지 주요 섹션으로 구성됩니다:
1. 네비게이션 버튼과 현재 년월을 표시하는 헤더
2. 요일 헤더 (일-토)
3. 날짜 그리드

### 3. 날짜 관리

date-fns의 여러 함수들을 사용하여 날짜 계산을 처리합니다:

```typescript
const calendarDays = React.useMemo(() => {
  const firstDay = startOfMonth(currentDate);
  const lastDay = endOfMonth(currentDate);
  
  return eachDayOfInterval({ start: firstDay, end: lastDay });
}, [currentDate]);
```

이 코드는 현재 월의 모든 날짜를 가져옵니다. 그리고 이 날짜들을 주 단위로 정리합니다:

```typescript
const calendarWeeks = React.useMemo(() => {
  return calendarDays.reduce<Date[][]>((weeks, day) => {
    const currentWeek = weeks[weeks.length - 1];
    
    if (!currentWeek || currentWeek.length === 7) {
      weeks.push([day]);
    } else {
      currentWeek.push(day);
    }
    
    return weeks;
  }, []);
}, [calendarDays]);
```

### 4. 월 이동 기능

월 이동은 간단한 함수로 처리됩니다:

```typescript
const handleNavigate = (monthDelta: number) => {
  setCurrentDate(date => (monthDelta > 0 ? addMonths : subMonths)(date, Math.abs(monthDelta)));
};
```

### 5. 날짜 렌더링

각 날짜 버튼은 여러 시각적 상태를 포함합니다:
- 기본 날짜
- 오늘 날짜 (파란색 테두리)
- 선택된 날짜 (파란색 배경)
- 다른 월의 날짜 (회색 텍스트)
- 호버 효과

```typescript
<button
  onClick={() => onChange?.(day)}
  className={`
    p-2 w-full text-center rounded-lg
    ${!isSameMonthDate && 'text-gray-400'}
    ${isSelected && 'bg-blue-500 text-white'}
    ${isCurrentDay && !isSelected && 'border border-blue-500'}
    ${!isSelected && 'hover:bg-gray-100'}
  `}
>
  {format(day, 'd')}
</button>
```

---
## 전체 코드
```typescript
import React from 'react';
import {
  format,
  startOfMonth,
  endOfMonth,
  eachDayOfInterval,
  isSameMonth,
  isToday,
  addMonths,
  subMonths
} from 'date-fns';
import { ChevronLeft, ChevronRight } from 'lucide-react';

/**
 * Calendar 컴포넌트의 props 타입 정의
 * @interface CalendarProps
 * @property {Date} [value] - 선택된 날짜. undefined일 경우 날짜 선택되지 않은 상태
 * @property {(date: Date) => void} [onChange] - 날짜 선택 시 호출되는 콜백 함수
 * @property {string} [className] - 추가적인 스타일링을 위한 CSS 클래스
 */
interface CalendarProps {
  value?: Date;
  onChange?: (date: Date) => void;
  className?: string;
}

/** 요일 레이블 배열 */
const weekDays = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'] as const;

/**
 * 날짜 선택을 위한 캘린더 컴포넌트
 * 
 * @component
 * @example
 * ```tsx
 * <Calendar
 *   value={new Date()}
 *   onChange={(date) => console.log(date)}
 *   className="my-calendar"
 * />
 * ```
 */
export const Calendar: React.FC<CalendarProps> = ({ value, onChange, className }) => {
  // 현재 표시되는 월의 기준이 되는 날짜 상태
  const [currentDate, setCurrentDate] = React.useState(value || new Date());

  /**
   * 현재 월의 모든 날짜를 계산
   * startOfMonth와 endOfMonth를 사용하여 현재 월의 시작일과 마지막일을 구하고
   * eachDayOfInterval을 사용하여 그 사이의 모든 날짜 배열을 생성
   */
  const calendarDays = React.useMemo(() => {
    const firstDay = startOfMonth(currentDate);
    const lastDay = endOfMonth(currentDate);
    
    return eachDayOfInterval({ start: firstDay, end: lastDay });
  }, [currentDate]);

  /**
   * 날짜 배열을 주 단위로 그룹화
   * 7일씩 묶어서 2차원 배열로 변환
   * 이를 통해 캘린더 그리드 형태로 렌더링 가능
   */
  const calendarWeeks = React.useMemo(() => {
    return calendarDays.reduce<Date[][]>((weeks, day) => {
      const currentWeek = weeks[weeks.length - 1];
      
      if (!currentWeek || currentWeek.length === 7) {
        weeks.push([day]);
      } else {
        currentWeek.push(day);
      }
      
      return weeks;
    }, []);
  }, [calendarDays]);

  /**
   * 월 이동 핸들러
   * @param monthDelta - 이동할 월 수. 음수는 이전, 양수는 다음으로 이동
   */
  const handleNavigate = (monthDelta: number) => {
    setCurrentDate(date => (monthDelta > 0 ? addMonths : subMonths)(date, Math.abs(monthDelta)));
  };

  return (
    <div className={`w-full max-w-md border rounded-lg shadow-sm bg-white ${className}`}>
      {/* 캘린더 헤더 - 월 이동 버튼과 현재 월 표시 */}
      <div className="flex items-center justify-between p-4 border-b">
        <button 
          onClick={() => handleNavigate(-1)}
          className="p-2 hover:bg-gray-100 rounded-full"
        >
          <ChevronLeft className="w-5 h-5" />
        </button>
        
        <h2 className="font-semibold">
          {format(currentDate, 'MMMM yyyy')}
        </h2>
        
        <button 
          onClick={() => handleNavigate(1)}
          className="p-2 hover:bg-gray-100 rounded-full"
        >
          <ChevronRight className="w-5 h-5" />
        </button>
      </div>

      {/* 요일 헤더 */}
      <div className="grid grid-cols-7 gap-1 p-4 pb-2 text-center text-sm font-medium text-gray-600">
        {weekDays.map(day => (
          <div key={day}>{day}</div>
        ))}
      </div>

      {/* 날짜 그리드 */}
      <div className="p-4 pt-0">
        {calendarWeeks.map((week, weekIndex) => (
          <div key={weekIndex} className="grid grid-cols-7 gap-1">
            {week.map((day, dayIndex) => {
              // 날짜 상태 계산
              const isSelected = value && 
                day.getDate() === value.getDate() && 
                day.getMonth() === value.getMonth() && 
                day.getFullYear() === value.getFullYear();
              
              const isSameMonthDate = isSameMonth(day, currentDate);
              const isCurrentDay = isToday(day);

              return (
                <button
                  key={dayIndex}
                  onClick={() => onChange?.(day)}
                  className={`
                    p-2 w-full text-center rounded-lg
                    ${!isSameMonthDate && 'text-gray-400'} // 현재 월이 아닌 날짜는 회색으로
                    ${isSelected && 'bg-blue-500 text-white'} // 선택된 날짜는 파란색 배경
                    ${isCurrentDay && !isSelected && 'border border-blue-500'} // 오늘 날짜는 파란색 테두리
                    ${!isSelected && 'hover:bg-gray-100'} // 선택되지 않은 날짜 호버 효과
                  `}
                >
                  {format(day, 'd')}
                </button>
              );
            })}
          </div>
        ))}
      </div>
    </div>
  );
};

export default Calendar;
```
## 사용 방법

캘린더 컴포넌트는 다음과 같이 사용할 수 있습니다:

```tsx
<Calendar
  value={new Date()}
  onChange={(date) => console.log(date)}
  className="my-calendar"
/>
```
---
## 주요 특징

1. **메모이제이션**: 캘린더 그리드 생성과 같은 비용이 큰 계산에는 `useMemo`를 사용하여 성능을 최적화했습니다.

2. **타입 안정성**: `TypeScript`를 사용하여 적절한 prop 타입을 보장하고 개발 중 오류를 방지합니다.

3. **반응형 디자인**: `Tailwind CSS`를 사용하여 다양한 화면 크기에서 작동하는 반응형 그리드 레이아웃을 구현했습니다.

4. **깔끔한 아키텍처**: 코드는 관심사의 명확한 분리와 잘 문서화된 함수들로 구성되어 있습니다.
---
## 향후 개선 고려 사항

- 날짜 범위 선택
- 시간 선택
- 최소/최대 날짜 제한
- 커스텀 날짜 포맷팅
- 접근성 개선
- 커스텀 스타일링 옵션
- 이벤트 표시기

