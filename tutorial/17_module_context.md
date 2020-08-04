# 모듈 컨텍스트

## 코드 공유(대화)

지금까지 본 모든 예제는 `<script>` 블록에는 각 컴포넌트의 인스턴스가 초기화 될 때 실행되는 코드가 포함되어 있다.

대부분의 컴포넌트에는 이 내용이 필요한 전부이다.

때때로 개별 컴포넌트 인스턴스 외부에서 일부 코드를 실행해야 할 필요가 있다.

예를 들어, 5 개의 오디오 플레이어를 모두 동시에 재생할 수 있다.

이 때 하나를 재생하면 다른 하나를 모두 중단하면 더 좋을 것이다.

`<script context = "module">` 블록을 선언하여 해소할 수 있다.

내부에 포함 된 코드는 컴포넌트가 인스턴스화 될 때가 아니라 모듈이 **처음 평가** 될 때 한 번만 실행된다.

```javascript
<script context="module">
    let current;
</script>
```

이제 상태 관리없이 컴포넌트가 서로 '대화' 할 수 있게 된다.

```javascript
function stopOthers() {
    if (current && current !== audio) current.pause();
    current = audio;
}
```

## 익스포트

`context="module"` 스크립트 블록에서 내보낸 것은 모듈 자체에서 내보낸다.

`AudioPlayer.svelte` 에서 `stopAll` 함수를 내보내는 예제

```javascript
<script context="module">
    const elements = new Set();

    export function stopAll() {
        elements.forEach(element => {
            element.pause();
        });
    }
</script>
```

그런 다음 `App.svelte` 에서 가져올 수 있다.

```javascript
<script>
    import AudioPlayer, { stopAll } from './AudioPlayer.svelte';
</script>
```

그리고 이벤트 핸들러에서 사용이 가능하다.

```javascript
<button on:click={stopAll}>
    stop all audio
</button>
```

> 컴포넌트가 기본 내보내기이므로 기본 내보내기를 수행 할 수 없다.
