# Svelte Script Modal

A singleton modal management solution for Svelte that eliminates boilerplate code when creating multiple modals.

preview:
```
https://svelte-modal.vercel.app/
```

## Dependencies
- Tailwind CSS
- Daisy UI 5
- Svelte 5 (rune)

## Source Code
The modal component is located at `src/lib/components/Modal.svelte`.

## Usage

### Setup
First, declare the Modal in your global layout:

```svelte


{@render children()}
```
The Toast component is optional but useful for modal feedback.

### Basic Modal

```typescript
const onBaseModalClick = () => {
  modal.open("title", "content \n content2")
}
```

This basic modal is useful for simple notifications that are too significant for a toast but need more style than a browser alert.

```typescript
function open(
  newTitle: string,
  newContent: string,
  confirmCallback?: (() => void | Promise) | null
): boolean;
```

Without a confirmCallback, the modal will only show a close button.

### Modal with Confirmation

```typescript
const onConfirmModalClick = () => {
  modal.open("title", "content", () => {toast.info('You clicked the modal.')})
}
```

When a confirmation callback is provided, the modal displays both close and confirm buttons.

The modal automatically handles Promise objects:

```typescript
const onPromiseModalClick = () => {
  modal.open("title", "content", async () => {
    await new Promise((resolve) => {
      setTimeout(() => {
        toast.info('You clicked the promise modal.');
        resolve();
      }, 2000);
    });
  });
}
```

By default, clicking confirm will execute the callback and then close the modal:

```typescript
try {
  const result = onConfirm();
  if (result instanceof Promise) await result;
  close();
}
```

If you want to handle this differently, you can use the exposed properties:

```typescript
get isOpen() { return isOpen; },
get isLoading() { return isLoading; },
```

### Component-Based Modal

The real power of this modal system is the ability to use Svelte components as content:

```typescript
const onContentModalClick = () => {
  const handleItemClick = (item: Item) => {
    modal.close();
    toast.info(`${item.title}이(가) 선택되었습니다.`);
  };
  
  modal.open(
    "title",
    ListContent,
    {
      items: items,
      onItemClick: handleItemClick
    }
  );
}
```

The API provides type-safe props for components:

```typescript
function open>(
  newTitle: string,
  newContent: string | T,
  propsOrCallback?: ComponentProps | (() => void | Promise) | null,
  confirmCallback?: (() => void | Promise) | null
)
```

### Step Modal

The most powerful feature is the ability to create multi-step modals without boilerplate:

```typescript
const step1Modal = () => {
  modal.open("step1", Step1, { onNext: step2Modal })
}

const step2Modal = () => {
  modal.open("step2", Step2, {
    onPrev: step1Modal,
    onNext: step3Modal
  })
}

const step3Modal = () => {
  modal.open("step3", Step3, {
    onPrev: step2Modal,
    onConfirm: () => {
      modal.close()
      toast.info('작업이 완료되었습니다.')
    }
  })
}
```

**Important:** To avoid lifecycle issues, manage state in the parent component and use callbacks in child components to avoid side effects.

## Contact
For inquiries: jinmail0949@gmail.com

---

# Svelte Script Modal

이 모달 컴포넌트는 모달을 싱글턴으로 관리하며 여러 모달을 생성할 때 발생하는 보일러플레이트 코드를 줄이는 것을 목적으로 합니다.

## 의존성
- Tailwind CSS
- Daisy UI 5
- Svelte 5 (rune)

## 소스 코드
모달 소스 코드는 `src/lib/components/Modal.svelte`에 있습니다.

## 사용 방법

### 설정
전역 레이아웃에 Modal을 선언합니다:

```svelte


{@render children()}
```
Toast는 모달 피드백을 보기 위한 선택적 컴포넌트입니다.

### 기본 모달

```typescript
const onBaseModalClick = () => {
  modal.open("title", "content \n content2")
}
```

이 기본 모달은 간단하게 상태를 알릴 때 유용합니다. 토스트로 표시하기엔 중요하고, 브라우저 alert는 미적으로 부족할 때 좋은 선택입니다.

```typescript
function open(
  newTitle: string,
  newContent: string,
  confirmCallback?: (() => void | Promise) | null
): boolean;
```

confirmCallback이 없으면 닫기 버튼만 표시됩니다.

### 확인 기능이 있는 모달

```typescript
const onConfirmModalClick = () => {
  modal.open("title", "content", () => {toast.info('You clicked the modal.')})
}
```

확인 콜백이 제공되면 모달에 닫기와 확인 버튼이 모두 표시됩니다.

모달은 자동으로 Promise 객체를 처리합니다:

```typescript
const onPromiseModalClick = () => {
  modal.open("title", "content", async () => {
    await new Promise((resolve) => {
      setTimeout(() => {
        toast.info('You clicked the promise modal.');
        resolve();
      }, 2000);
    });
  });
}
```

기본적으로 확인 버튼을 클릭하면 콜백이 실행된 후 모달이 닫힙니다:

```typescript
try {
  const result = onConfirm();
  if (result instanceof Promise) await result;
  close();
}
```

이 동작을 다르게 처리하고 싶다면 노출된 속성을 활용할 수 있습니다:

```typescript
get isOpen() { return isOpen; },
get isLoading() { return isLoading; },
```

### 컴포넌트 기반 모달

이 모달 시스템의 진정한 강점은 Svelte 컴포넌트를 컨텐츠로 사용할 수 있다는 점입니다:

```typescript
const onContentModalClick = () => {
  const handleItemClick = (item: Item) => {
    modal.close();
    toast.info(`${item.title}이(가) 선택되었습니다.`);
  };
  
  modal.open(
    "title",
    ListContent,
    {
      items: items,
      onItemClick: handleItemClick
    }
  );
}
```

API는 컴포넌트에 대한 타입 안전 props를 제공합니다:

```typescript
function open>(
  newTitle: string,
  newContent: string | T,
  propsOrCallback?: ComponentProps | (() => void | Promise) | null,
  confirmCallback?: (() => void | Promise) | null
)
```

### 단계별 모달

가장 강력한 기능은 보일러플레이트 없이 다단계 모달을 만들 수 있다는 점입니다:

```typescript
const step1Modal = () => {
  modal.open("step1", Step1, { onNext: step2Modal })
}

const step2Modal = () => {
  modal.open("step2", Step2, {
    onPrev: step1Modal,
    onNext: step3Modal
  })
}

const step3Modal = () => {
  modal.open("step3", Step3, {
    onPrev: step2Modal,
    onConfirm: () => {
      modal.close()
      toast.info('작업이 완료되었습니다.')
    }
  })
}
```

**중요:** 라이프사이클 문제를 방지하기 위해 부모 컴포넌트에서 상태를 관리하고 자식 컴포넌트에서는 콜백을 사용하여 사이드 이펙트를 피하는 것이 좋습니다.

## 연락처
문의사항: jinmail0949@gmail.com