# 컴포넌트 조합

엘리먼트가 자식 엘리먼트를 가지는 것처럼 컴포넌트 또한 자식 엘리먼트를 가질 수 있다.

그러나 컴포넌트가 자식 컴포넌트를 수용하려면 먼저 어디에 배치해야 하는지 알아야 한다.

## 슬롯

`<slot>` 을 사용하여 이 작업을 수행 할 수 있다.

```javascript
<div class="box">
    <slot></slot>
</div>
```

```javascript
<script>
    import Box from './Box.svelte';
</script>

<Box>
    <h2>Hello!</h2>
    <p>This is a box. It can contain anything.</p>
</Box>
```

## 슬롯 기본 값

`<slot>` 에 내용이 비어있는 슬롯애 대한 기본 값을 지정할 수 있다.

```javascript
<div class="box">
    <slot>
        <em>no content was provided</em>
    </slot>
</div>
```

이제 자식 없이 `<Box>` 인스턴스를 만들 수도 있다.

```javascript
<Box/>
```

## 이름 있는 슬롯

이전 예제는 컴포넌트의 직접 하위를 렌더링하는 기본 슬롯이 포함되었다.

때때로 이 `<ContactCard>` 와 같이 배치를 세밀하게 제어할 필요가 있다.

이경우 명명 된 슬롯을 사용할 수 있다.

```javascript
<article class="contact-card">
    <h2>
        <slot name="name">
            <span class="missing">Unknown name</span>
        </slot>
    </h2>

    <div class="address">
        <slot name="address">
            <span class="missing">Unknown address</span>
        </slot>
    </div>

    <div class="email">
        <slot name="email">
            <span class="missing">Unknown email</span>
        </slot>
    </div>
</article>
```

```javascript
<ContactCard>
    <span slot="name">
        P. Sherman
    </span>

    <span slot="address">
        42 Wallaby Way<br>
        Sydney
    </span>
</ContactCard>
```

## 슬롯 속성

이번 애플리케이션에서는 마우스가 현재 마우스 위에 있는지 여부를 추적하는 `<Hoverable>` 컴포넌트이다.

슬롯 형 컨텐츠를 업데이트 할수 있도록 해당 데이터를 상위 컴포넌트로 다시 전달해야한다.

이를 위해서 슬롯 속성(*slot props*) 를 사용한다.

`Hoverable.svelte` 에서 이 `hovering` 값을 슬롯에 전달한다.

> 원하는 경우 `{hovering}` 속기를 사용할 수도 있다.

`<Hoverable>` 컴포넌트 내용에 `hovering` 을 표시하기 위해 `let` 표현식을 사용한다.

```javascript
<Hoverable let:hovering={hovering}>
    <div class:active={hovering}>
        {#if hovering}
            <p>I am being hovered upon.</p>
        {:else}
            <p>Hover over me!</p>
        {/if}
    </div>
</Hoverable>
```

원하는 경우 변수의 이름을 바꿀 수 있다. 부모 컴포넌트에서 변수 `active` 로 선언한다.

```javascript
<Hoverable let:hovering={active}>
    <div class:active>
        {#if active}
            <p>I am being hovered upon.</p>
        {:else}
            <p>Hover over me!</p>
        {/if}
    </div>
</Hoverable>
```

컴포넌트의 값은 원하는 만큼 가질 수 있으며, 선언된 컴포넌트에 대해서 로컬로 유지된다.

> 이름이 있는 슬롯도 속성을 가질 수 있다. 컴포넌트 대신 `slot="..."` 속성이 있는 엘리먼트에 `let` 디렉티브를 사용한다.
