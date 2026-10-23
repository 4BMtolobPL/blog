+++
date = '2026-10-24T03:05:51+09:00'
draft = false
title = 'Key dispatch'
description = "GPUI Doc | Key Dispatch 기계번역"
+++

# 키 디스패치

GPUI는 키보드 중심의 상호 작용을 위해 설계되었습니다.

마우스로 기능을 사용하려면 클릭 핸들러가 있는 버튼을 렌더링하면 됩니다.

키보드로 기능을 사용하려면 _키 컨텍스트_에서 _액션_을 바인딩하면 됩니다.

액션은 `MouseDown`, `KeyDown` 등과 같은 프레임워크 수준의 이벤트와 유사하지만, 직접 정의할 수 있습니다:

```rust
mod menu {
    #[gpui::action]
    struct MoveUp;

    #[gpui::action]
    struct MoveDown;
}
```

액션은 주로 단일 구조체(unit struct)이며, 이를 위한 매크로가 제공됩니다. 위 코드는 다음과 같이 작성할 수도 있습니다:

```rust
mod menu {
    actions!(gpui, [MoveUp, MoveDown]);
}
```

액션은 더 복잡한 타입일 수도 있습니다:

```rust
mod menu {
    #[gpui::action]
    struct Move {
        direction: Direction,
        select: bool,
    }
}
```

액션을 바인딩하려면 요소에 `on_action`을 체인하세요:

```rust
impl Render for Menu {
    fn render(&mut self, window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .on_action(|this: &mut Menu, move: &MoveUp, window: &mut Window, cx: &mut Context<Menu>| {
                // ...
            })
            .on_action(|this, move: &MoveDown, cx| {
                // ...
            })
            .children(unimplemented!())
    }
}
```

키를 액션에 바인딩하려면 `key_context`를 호출하여 요소 트리의 일부에 대한 _키 컨텍스트_를 선언해야 합니다.

```rust
impl Render for Menu {
    fn render(&mut self, window: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div()
            .key_context(“menu”)
            .on_action(|this: &mut Menu, move: &MoveUp, window: &mut Window, cx: &mut Context<Menu>| {
                // ...
            })
            .on_action(|this, move: &MoveDown, cx| {
                // ...
            })
            .children(unimplemented!())
    }
}
```

이제 키맵에서 컨텍스트를 지정할 수 있습니다. 키맵에서 액션이 완전한 유형 이름으로 식별된다는 점에 유의하세요.

```json
{
  “context”: “menu”,
  “bindings”: {
    “up”: “menu::MoveUp”,
    “down”: “menu::MoveDown”
  }
}
```

더 복잡한 타입 정의를 선택한 경우, 이름과 함께 액션의 직렬화된 표현을 제공해야 합니다:

```json
{
  “context”: “menu”,
  “bindings”: {
    “up”: [“menu::Move”, {direction: “up”, select: false}]
    “down”: [“menu::Move”, {direction: “down”, select: false}]
    “shift-up”: [“menu::Move”, {direction: “up”, select: true}]
    “shift-down”: [“menu::Move”, {direction: “down”, select: true}]
  }
}
```
