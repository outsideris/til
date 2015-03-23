# Sequelize - 2개 이상의 필드에서 최대값 찾기

두 개 이상의 필드에서 각각 최대값을 뽑아내서 서로 비교해야하는 상황이 있다. 예를 들면 insertVersion 필드와 deleteVersion 필드의 최대 값을 뽑아내는 경우이다. 기본적으로 Sequelize 의 Class Method 로 [max](https://github.com/kriskowal/q/wiki/API-Reference#promisenodeifycallback) 라는 함수가 존재해서 각 필드의 최대 값은 아래처럼 구할 수 있다.

```javascript
Cell.max('insertVersion', {where: {travelId: travelId}})
  .then(function(result) {
    console.log(‘max insertVersion is ’ + result);
  });

Cell.max(‘deleteVersion’, {where: {travelId: travelId}})
  .then(function(result) {
    console.log(‘max deleteVersion is ’ + result);
  });
```
하지만 `insertVersion`, `deleteVersion` 최대값을 각각 뽑아서 다시 비교하는 것 보다 아래 코드를 통해서 한번에 뽑아서 비교하는 편이 더 깔끔하다.

```javascript
Cell.find({
  attributes: [
    [sequelize.fn('max', sequelize.col('insertVersion')), 'maxInsertVersion'],
    [sequelize.fn('max', sequelize.col('deleteVersion')), 'maxDeleteVersion']
  ]
}, {
  raw: true
}).then(function(result) {
  console.log(‘max insertVersion is + result .maxInsertVersion+ ‘max deleteVersion is + result.maxDeleteVersion);
});
``` 
