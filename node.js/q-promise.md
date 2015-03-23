# [Q](https://github.com/kriskowal/q)

## `promise.nodeify(callback)`
[Sequelize](http://docs.sequelizejs.com/en/latest/)에서 기본으로 Promise를 사용하고
있지만 어플리케이션에서는 callback pattern을 주로 사용하고 있으므로 Promise는 Persistant
계층에서만 사용하고 이후로는 callback으로 변환해서 반환하는 방식을 사용하고 있다. Promise를 쓰면
다른 곳에도 Promise를 계속 써야하는 문제가 있는데 이를 억지로 변환하다 보니 `then()`에서 콜백을
호출했을 때 콜백내에서 오류가 발생하면 이 부분이 원래 호출한 Promise의 `catch()`부분으로 전달되어서
콜백패턴의 오류가 제대로 처리되지 않고 있다. 특히 Test에서 `assert` 실패가 발생할 경우 이 부분이
mocha에서 제대로 잡히지 않고 타임아웃이 걸리게 된다.

Q에 [promise.nodeify](https://github.com/kriskowal/q/wiki/API-Reference#promisenodeifycallback)라는 함수가 존재해서 `promise`를 노드의 일반적인 콜백패턴으로 변환해 주는 함수가 존재한다.

```javascript
function createUser(userName, userData, callback) {
  return database.ensureUserNameNotTaken(userName)
  .then(function () {
    return database.saveUserData(userName, userData);
  })
  .nodeify(callback);
}
```

이를 사용하면 위의 코드처럼 Promise와 callback을 병행해서 사용하거나 Promise를 callback로
변경할 수 있다. `.nodeify`를 사용하면 `catch`에 걸렸을 때 `callback(err)`를 호출하고
`then`일 때는 `callback(null, data)`를 자동으로 호출해 주기 때문에 자연스럽게 Promise에서
Callback으로 변환할 수 있다.

현재 기본적인 형태외에 `Error`객체를 변환해 주는 등의 처리등이 추가되어 있어서 이 부분까지 자연스럽게
callback으로 전환하려면 좀 더 봐야 함.
