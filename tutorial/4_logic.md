# 논리

## if

```javascript
{#if user.loggedIn}
    <button on:click={toggle}>
        Log out
    </button>
{/if}
```

## else

```javascript
{#if user.loggedIn}
    <button on:click={toggle}>
        Log out
    </button>
{:else}
    <button on:click={toggle}>
        Log in
    </button>
{/if}
```

`#` 문자는 항상 블록을 여는 태그이며, `/` 는 항상 블록을 닫는 태그이다.

`:` 는 블록의 연속태그를 나타낸다.

## else if

```javascript
{#if x > 10}
    <p>{x} is greater than 10</p>
{:else if 5 > x}
    <p>{x} is less than 5</p>
{:else}
    <p>{x} is between 5 and 10</p>
{/if}
```

## each

```javascript
{#each cats as cat, i}
    <li>
        <a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
        {i + 1}: {cat.name}
        </a>
    </li>
{/each}
```

## keyed each

```javascript
{#each things as thing (thing.id)}
    <Thing current={thing.color}/>
{/each}
```

`each` 의 각 고유 식별자를 지정하여 Svelte 에게 변경 방법을 알려준다.

`thing` 으로도 식별할 수 있지만 `thing.id` 와 같은 식별자를 사용하는 것이 안전하다.

## await

대부분의 애플리케이션은 특정 시점에 비동기 데이터를 처리한다.

Svelte 는 마크업에서 `await` 문법으로 사용할 수 있다.

```javascript
{#await promise}
    <p>...waiting</p>
{:then number}
    <p>The number is {number}</p>
{:catch error}
    <p style="color: red">{error.message}</p>
{/await}
```
