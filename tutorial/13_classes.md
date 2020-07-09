# 클래스

## 표현식

다른 속성과 마찬가지로 JS 속성을 사용하여 `css` 클래스를 지정할 수 있다.

```javascript
<button
    class="{current === 'foo' ? 'active' : ''}"
    on:click="{() => current = 'foo'}"
>foo</button>

<button
    class:active="{current === 'foo'}"
    on:click="{() => current = 'foo'}"
>foo</button>
```

## 축약

종종 클래스 명은 변수 명과 동일한 값을 가진다.

마찬가지로 축약하여 적용할 수 있다.

```javascript
<div class:big>
    <!-- ... -->
</div>
```
