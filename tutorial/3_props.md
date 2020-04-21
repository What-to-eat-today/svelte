# 요소

## 요소 선언

지금까지는 내부 상태만을 처리하였다. 즉, 주어진 컴포넌트에서만 값을 접근할 수 있었다.

그러나 실제 애플리케이션은 하위 컴포넌트에 데이터를 전달해야한다.

이를 위해선, `export` 키워드로 이를 수행한다.

```javascript
<script>
    export let answer;
</script>
```

> `$:` 문법과 마찬가지로 어색하게 느껴질 것이며, `export` 키워드는 일반 Javascript 모듈에서 작동하는 방식과는 다르다.

## 기본 값

요소의 기본 값 또한 쉽게 선언 가능하다.

```javascript
<script>
    export let answer = 'a mystery';
</script>
```

## 전파 요소

요소가 객체로 있는 경우, 각 객체를 지정하는 요소를 전파할 수 있다.

```javascript
<script>
    import Info from './Info.svelte';

    const pkg = {
        name: 'svelte',
        version: 3,
        speed: 'blazing',
        website: 'https://svelte.dev'
    };
</script>

<Info name={pkg.name} version={pkg.version} speed={pkg.speed} website={pkg.website}/>
```

예제에서는 요소를 각각 전파하였지만, 객체로도 바인딩 가능할 것으로 보인다.
