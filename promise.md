# javascript에서의 비동기 처리란 무엇이냐?
정의는 "특정 코드의 연산이 끝날 때까지 코드의 실행을 멈추지 않고 다음 코드를 먼저 실행하는
자바스크립트의 특성"을 의미한다. 일단 코드부터 보자
```
console.log('1');
setTimeout(() => {
    console.log('2');
}, 2000);
console.log('3');
```
위의 코드를 보면 순서대로 1,2,3이 출력될거 같지만 js의 비동기 처리로 인해서
1,3,2가 출력된다.

비동기 처리 문제를 해결하기 위해 callback function을 사용하거나 Promise를 사용할 수 있다
두 번째 방법인 Promise에 대해 정리해 보겠다.

Promise의 정의는 "자바스크립트 비동기 처리에 사용되는 객체이다."

## Promise가 필요한 이유?
```
function getData(callbackFunc) {
  $.get('url 주소/products/1', function(response) {
    callbackFunc(response); // 서버에서 받은 데이터 response를 callbackFunc() 함수에 넘겨줌
  });
}

getData(function(tableData) {
  console.log(tableData); // $.get()의 response 값이 tableData에 전달됨
});
위 코드는 비동기 처리에 callback function을 사용했다 이것을 Promise로 바꾸면

function getData(callback) {
  return new Promise(function(resolve, reject) {
    $.get('url 주소/products/1', function(response) {
      resolve(response);
    });
  });
}

getData()
.then(function(tableData) {
  console.log(tableData);
});
```
일단 Promise에 대해 코드로 잠깐 알아봤다. 이제 본격적으로 Promise에 대해 알아보자
Promise 상태(Promise의 처리과정)는 new Promise()를 생성하고 종료될 때까지
3가지 상태를 갖는다

1. Pending(대기)
    new Promise() 메서드를 호출하면 pending 상태가 된다.
2. Fulfilled(이행 또는 완료)
    resolve()를 실행하면 fulfilled 상태가 된다.
3. Rejected(실패)
    reject()를 호출하면 Rejected 상태가 된다.
    Rejected 상태가 되면 실패한 이유를 catch()로 받을 수 있다.

## Promise 에러처리 방법 2가지
```
function getData() {
  return new Promise(function(resolve, reject) {
    reject('failed');
  });
}

// 1. then()의 두 번째 인자로 에러를 처리하는 코드
getData().then(function() {
  // ...
}, function(err) {
  console.log(err);
});

// 2. catch()로 에러를 처리하는 코드
getData().then().catch(function(err) {
  console.log(err);
});
```

가급적이면 catch()를 사용하자 그 이유는

### 1.then()의 두 번째 인자로 에러처리
```
  // then()의 두 번째 인자로는 감지하지 못하는 오류
function getData() {
  return new Promise(function(resolve, reject) {
    resolve('hi');
  });
}

getData().then(function(result) {
  console.log(result);
  throw new Error("Error in then()"); // Uncaught (in promise) Error: Error in then()
}, function(err) {
  console.log('then error : ', err);
});

```
이러면 then()의 첫 번째 콜백 함수 내부에서 오류가 나는 경우를 제대로 잡지 못한다. 그래서 아래와 같은 코드로 에러 처리를 해준다

### 2. catch()로 에러처리

```
getData().then(function(result) {
  console.log(result); // hi
  throw new Error("Error in then()");
}).catch(function(err) {
  console.log('then error : ', err); // then error :  Error: Error in then()
});
```