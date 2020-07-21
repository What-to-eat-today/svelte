# 컨텍스트 API

[Mapbox GL]: https://docs.mapbox.com/mapbox-gl-js/overview/
[life_cycle]: ./7_lifecycle
[store]: ./8_stores

컨텍스트 API 는 컴포넌트가 데이터와 함수를 프로퍼티로 전달하거나, 많은 이벤트를 전달하지 않고 서로 '대화' 할 수 있는 메커니즘을 제공한다.

고급 기능으로 이는 꽤 유용한 기능이다.

## setContext 와 getContext

[Mapbox GL][Mapbox GL] 을 사용하여 맵을 만들어보도록 한다.

`<MapMarker>` 컴포넌트를 사용하여 마커를 표시하고 싶지만, 기본 Mapbox 인스턴스에 대한 참조를 각 컴포넌트의 프로퍼티로 전달할 필요는 없다.

컨텍스트 API 는 `setContext` 와 `getContext` 두 가지 부분이 있다.

컴포넌트가 `setContext(key, context)` 를 호출하면 모든 하위 컴포넌트가 `const context = getCOntext(key)` 를 사용하여 컴포넌트를 검색 할 수 있다.

우선 컨텍스트를 설정한다.

`Map.svelte` 에서 `svelte` 를 통해 `setContext` 를 가져오고 `mapbox.js` 에서 키를 가져온 후, setContext 를 호출한다.

```javascript
import { onMount, setContext } from 'svelte';
import { mapbox, key } from './mapbox.js';

setContext(key, {
    getMap: () => map
});
```

컨텍스트 객체는 원하는 것이 될 수 있다.

[라이프 사이클 함수][life_cycle]와 마찬가지로 컴포넌트 초기화 중 `setContext` 및 `getContext` 를 호출해야한다.

컴포넌트가 마운트 될 때까지 `map` 이 생성되지 않기 때문에 컨텍스트 객체에는 `map` 자체가 아닌 `getMap` 함수가 포함되게 된다.

동등하게 반대편에 있는 `MapMarker.svelte` 에서 이제 Mapbox 인스턴스에 대한 참조를 얻을 수 있다.

```javascript
import { getContext } from 'svelte';
import { mapbox, key } from './mapbox.js';

const { getMap } = getContext(key);
const map = getMap();
```

마커는 이제 지도에 자신을 추가할 수 있게 된다.

> 보다 완성 된 `<MapMarker>` 버전은 제거 및 프로퍼티 변경도 처리하지만 여기서는 컨텍스트를 보여줄뿐이다.

## 컨텍스트 키

`mapbox.js` 는 다음 줄이 있다.

```javascript
const key = {};
```

무엇이든 키로 사용할 수 있다.

> `setContext('mapbox', ...)`

문자열 사용의 단점은 다른 컴포넌트 라이브러리가 실수로 동일한 라이브러리를 사용 할수 있다는 것이다. (라이브러리 중첩)

그러나 위처럼 객체 리터럴을 사용하게 되면 어떤 상황에서도 키가 충돌하지 않게 된다.

> 여러 컴포넌트 계층에서 작동하는 여러 개의 다른 컨텍스트가 있는 경우에도 객체는 자신에 대한 참조 동등성을 가지기 때문이다. (`{} !== {}` ... `"x" === "x"`)

## 컨텍스트와 스토어

언뜻 보면 컨텍스트와 [스토어][store]는 비슷해보인다.

그러나 분명하게 다른 점을 가진다.

스토어는 애플리케이션 어느 부분에서나 사용 가능하고 컨텍스트는 **컴포넌트 및 하위 항목**에서만 사용할 수 있다.

이는 하나의 상태가 다른 상태를 방해하지 않고 컴포넌트의 여러 인스턴스를 사용하려는 경우 유용하다.

실제로는 둘을 함께 사용할 수 있다.

컨텍스트가 반응하지 않기 때문에 시간에 지남에 따라 변경되는 값은 스토어로 표시하는 방법이다.

```javascript
const { these, are, stores } = getContext(...);
```
