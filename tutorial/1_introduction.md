# 개요

## 기초

Svelte 는 React 및 Vue 와 같은 프레임워크이다.

가장 중요한 차이점은 Svelte 는 런타임에 코드를 해석하지 않고 빌드시 애플리케이션에 Javascript 로 변환한다.

## 동적 속성

```javascript
<img src={src}>

// 짧은 속성
<img {src}>
```

## 중첩 컴포넌트

```javascript
<script>
    import Nested from './Nested.svelte';
</script>

// ...

<Nested/>
```

`Nested` 컴포넌트는 `<p>` 태그 임에도 중첩 컴포넌트 이기 때문에 스타일에 영향을 받지 않으며, lowercase 인 HTML 과 구분하기 위해 capitalised 를 채택하였다.

## HTML tags

일반적인 문자열은 `<` 와 `>` 를 포함하지 않은 텍스트이다.

```javascript
<p>{@html string}</p>
```

Svelte 는 표현식이 DOM 에 삽입되기 전 내부에 어떤 전처리도 하지않는다.

따라서 신뢰할 수 없는 소스에 제공되는 HTML 은 수동으로 이스케이프해야한다.

XSS 공격에 노출 될 수 있다.
