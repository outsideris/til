# Sequelize

## Models

### max - 최대값 찾기

특정 필드의 값에서 최대 값을 찾아야 하는 경우가 있는데, 
이런 경우는 [max](https://github.com/kriskowal/q/wiki/API-Reference#promisenodeifycallback) 함수를 활용하면 된다.

```javascript
// insertVersion 필드에서 최대값 찾기
Cell.max('insertVersion', {where: {travelId: travelId}})
  .then(function(result) {
    console.log(‘max insertVersion is ’ + result);
  });

// deleteVersion 필드에서 최대값 찾기
Cell.max(‘deleteVersion’, {where: {travelId: travelId}})
  .then(function(result) {
    console.log(‘max deleteVersion is ’ + result);
  });
```

만약 `insertVersion`, `deleteVersion` 필드에서 각각 최대값을 한번에 뽑아야하는 경우는 `max` 함수를 아래처럼 활용하면 된다.

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
하지만 이렇게 최대값만 찾아내는 작업이 아니라, 찾아낸 최대값을 활용해서 다른 DB 작업을 수행하는 경우에는 
그 사이에 최대값이 변경될 수 있는 여지가 있기 때문에 다른 방법을 써야한다.



