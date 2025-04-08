# fastify-bull2
[![npm version](https://badge.fury.io/js/fastify-bull2.svg)](https://badge.fury.io/js/fastify-bull2)

Use bull queues from your fastify application. Utilize fastify decorators to access shared connections, logger etc.

## 설치

```bash
npm install fastify-bull2 --save

# or

yarn add fastify-bull2
```

## 사용법

### 큐 핸들러

큐 핸들러 예제입니다.

```js
// queues/example.queue.js

const name = 'example';
function handler(server, job, done) { // 비동기 핸들러도 가능합니다
  /* 큐 처리 로직 */
  done()
}

module.exports = {
  name,
  handler,
};
```

### Fastify에 추가하기

```js
const fastify = require('fastify');

const server = fastify();

server.register(require('fastify-bull2'), {
  paths: 'queues/**/*.queue.js', // 큐 파일을 찾을 경로
  redisUrl: 'redis://localhost:6379', // Redis 연결 URL
  // bullOptions: {} // Bull 추가 옵션 (선택 사항)
})

const start = async () => {
  try {
    await server.listen(3000, "0.0.0.0");

    // 큐에 작업 추가 (자세한 내용은 Bull 문서를 참조)
    server.queues['example'].add({ hello: 'world' }, { priority: 1 });

  } catch (err) {
    console.log(err);
    server.log.error(err);
    process.exit(1);
  }
};

start();
```

### ESM에서 사용하기

```js
import Fastify from 'fastify';
import fastifyBull from 'fastify-bull2';

const server = Fastify();

server.register(fastifyBull, {
  paths: 'queues/**/*.queue.js',
  redisUrl: 'redis://localhost:6379',
});

// ... 이하 동일
```

### 큐에 작업 추가하기

```js
fastify.queues['my-queue'].add({ data: 'some data' });
```

## 옵션

* `paths: <String | Array>`: 큐 핸들러가 있는 폴더를 지정합니다.
* `redisUrl: <String>`: Redis 연결 URL입니다.
* `redisOptions: <Object>`: Redis 연결 옵션입니다 (선택 사항).
* `bullOptions: <Object>`: Bull 큐에 전달할 추가 옵션입니다 (선택 사항).
* `onFailed: <Function>`: 큐 "failed" 이벤트 핸들러입니다 (기본값으로 fastify 로거를 사용).
* `onError: <Function>`: 큐 "error" 이벤트 핸들러입니다 (기본값으로 fastify 로거를 사용).

## 정보

### Bull과 Redis 연결

이 플러그인은 [ioredis](https://github.com/luin/ioredis)를 사용하여 Redis에 연결합니다. 큐 간에 Redis 연결을 공유하도록 사전 구성되어 있어 연결 제한이 있는 환경에서 유용합니다.

## 작성자

Cho Yunsung

## TODO

- TypeScript 타입 정의 추가
- fastify-redis 지원 추가
- 옵션으로 Redis 연결 객체 지원 추가
- Bull이 Redis 연결을 처리하는 방식을 구성할 수 있는 방법 찾기
- 중첩 디렉토리 지원 추가
- paths에 대한 기본 매개변수 추가

## 라이센스

[MIT](./LICENSE) 라이센스에 따라 사용이 허가됩니다.
