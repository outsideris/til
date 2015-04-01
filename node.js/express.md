# [Express](http://expressjs.com/)

## Express 에서 Query String 의 정수 변환하기

`GET` 방식의 API 호출을 하는 경우 Query String 으로 클라이언트의 요청 값을 받게되는데 
이 값이 정수일 때 `parseInt(value)` 처리를 하지 않으면 String 으로 처리하기 때문에 문제가 생기는 경우가 종종 있다. 
예를 들어 `start=10` 값과 `end=10` 값을 받는 경우 각각의 값을 바로 정수로 변환하지 않으면 실수로 
`start+end` 같은 코드를 작성할 수 있는데, 결과적으로 원하는 값인 `20` 이 아닌 `1010` 으로 처리해서 버그가 생긴다.

따라서 Express 의 Route 쪽에서(혹은 다른 어딘가에서) 아래처럼 정수로 변환해서 써야한다. 
일단 Query String 중에서 mandatory 인 값과 optional 인 값을 구별해서 isNaN() 함수로 Error 를 처리하게 해봤는데 
더 좋은 방법이 있는지 고민해봐야겠다.

```
req.query.mandatory = req.query.mandatory ? parseInt(req.query.mandatory) : undefined;
req.query.optional = req.query.optional ? parseInt(req.query.optional) : null;
if(isNaN(req.query.mandatory) || isNaN(req.query.optional)) {
  return next(error);
}
```
