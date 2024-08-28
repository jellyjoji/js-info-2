# defer, async 스크립트

## defer 속성이란?

웹 페이지의 구조가 모두 만들어진 후에 스크립트가 실행되게 하는것.

## defer 사용 이유

- 스크립트 로딩이 웹 페이지 렌더링을 차단하는 것을 방지하여 웹 페이지 로딩 속도 향상
- 스크립트가 HTML 파싱에 영향을 주지 않도록 하여스크립트 실행 순서 조절

## defer 속성과 async 속성의 차이점

- defer : 순차적
  HTML 파싱이 완료된 후, DOMContentLoaded 이벤트 전에 실행됩니다. 스크립트 실행 순서는 보장됩니다.
  `<script defer src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>`
- async : 먼저 다운되는대루 병렬 실행
  스크립트가 다운로드되는 즉시 실행됩니다. 다른 스크립트나 HTML 파싱을 기다리지 않습니다. 스크립트 실행 순서는 보장되지 않습니다.
  `<script async src="https://javascript.info/article/script-async-defer/long.js"></script>`

## defer 속성과 async 속성의 공통점

다운로드 시 페이지 렌더링을 막지 않는다.
따라서 async와 defer를 적절히 사용하면 사용자가 오래 기다리지 않고 페이지 콘텐츠를 볼 수 있게 할 수 있습니다.
