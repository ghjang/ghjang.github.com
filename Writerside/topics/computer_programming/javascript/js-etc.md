# ...

## 'defer' 속성

`defer` 속성은 스크립트를 실행을 미루는 속성이다. `defer` 속성을 사용하면 HTML 파싱이 끝난 후에 스크립트를 실행한다. `defer` 속성은 스크립트가
여러 개 있을 때 순서를 보장한다. `defer` 속성은 `async` 속성과 함께 사용할 수 없다.

```html
<script defer src="script.js"></script>
```

경우에 따라서는 `body` 태그의 `onload` 이벤트에서 실행하던 스크립트를 `defer` 속성을 사용하여 `head` 태그에 두는 것이 좋다. `onload` 이벤트는
모든 리소스가 다운로드된 후에 실행되기 때문에 스크립트가 실행되기까지 시간이 걸린다. `defer` 속성을 사용하면 HTML 파싱이 끝난 후에 스크립트를 실행하기
때문에 스크립트가 실행되기까지 시간이 단축된다.

## 'async' 속성

`async` 속성은 스크립트를 비동기적으로 실행하는 속성이다. `async` 속성을 사용하면 HTML 파싱을 멈추고 스크립트를 다운로드한 후 실행한다. `async` 속성은
스크립트가 여러 개 있을 때 순서를 보장하지 않는다. `async` 속성은 `defer` 속성과 함께 사용할 수 없다.

```html
<script async src="script.js"></script>
```
