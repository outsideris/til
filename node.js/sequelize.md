# Sequelize

## Models

### max - 최대값 찾기

특정 필드의 값에서 최대 값을 찾아야 하는 경우가 있는데, 이런 경우는 
[max](http://docs.sequelizejs.com/en/latest/docs/models/#max-get-the-greatest-value-of-a-specific-attribute-within-a-specific-table) 
함수를 활용하면 된다.

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
위의 경우에는 inserVersion, deleteVersion 필드에서 직접 max 값을 구하지말고, 
해당 version 을 다른 Table 의 field 로 관리하여 해결 할 수 있다. 
Sequelize 에서는 SET column = column + X 을 지원하는
[increment](http://sequelize.readthedocs.org/en/latest/api/instance/#incrementfields-options-promisethis)
함수를 지원한다. 이를 이용해서 atomic 하게 version 을 update 하면서 max 값으로 활용 할 수 있다.
```
instance.increment('number') // increment number by 1
instance.increment(['number', 'count'], { by: 2 }) // increment number and count by 2
instance.increment({ answer: 42, tries: 1}, { by: 2 }) // increment answer by 42, and tries by 1.
                                                       // `by` is ignored, since each column has its own value
```

## Raw Query

### SQL Injection

SQL Injection 은 개발자가 직접 작성한 쿼리문에 예상하지 못한 SQL문을 추가하게 만들어서 데이터베이스를 비정상적으로 조작하는 공격 방법이다.
Sequelize 를 포함해서 DB 프레임워크를 사용할 때 SQL Injection 은 항상 주의해야 하는 점이다. 
(언제인지는 기억이나지 않지만 관련 내용을 배웠었고, 앞으로 주의해야지라고 생각했었지만 오늘 실수를 저지르고 있었다.)

아래와 같이 사용하면 SQL Injection 에 노출된다. 
```
sequelize.query(“SELECT * FROM users WHERE ID=?” + id + “ AND PW= ?” + password)
  .then(function(user) {
    // 만약 id 값으로 “OR ‘1’=‘1’ — ” 또는 password 값으로 “OR ‘1’=‘1’ 이 들어온다면, 무조건 로그인에 성공한다.
    if(user) { console.log(‘로그인 성공’) } 
  })
```


따라서 쿼리문을 직접 사용하는 경우 사용자가 입력하는 부분에 대해서는 항상 유효성을 검증해야하는데, 일반적으로 아래의 방법처럼 사용하면 문법 오류를 필터링 할 수 있다.
```
sequelize.query(“SELECT * FROM users WHERE ID=? AND PW= ?”, [id, password])
  .then(function(user) {
    console.log(user)
  })
```


