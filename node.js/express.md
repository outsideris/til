# [Express](http://expressjs.com/)

## Express 에서 Query String 의 정수 변환하기

`GET` 방식의 API 호출을 하는 경우 Query String 으로 클라이언트의 요청값을 받는 경우,
이 값이 정수인 경우 `parseInt(value)` 처리를 하지 않으면 당연히 String 으로 받아서 문제가 생기는 경우가 종종 있다. 
예를 들어 start=10 값과 end=10 값을 받았는데 이 값을 정수로 변환하지 않고 코드를 작성하다 보면 
`start+end` 같은 코드를 실수로 작성하게 될 수 있는데, 원하는 값인 20 이 아닌 1010 으로 동작해서 버그가 생긴다.

따라서 Express 의 Route 쪽에서 아래처럼 정수로 변환해서 써야한다. 
일단 Query String 중에서 mandatory 인 값과 optional 인 값을 구별해서 isNaN() 함수로 Error 를 처리하게 했는데, 
더 좋은 방법이 있는지 고민해봐야겠다.

```
req.query.mandatory = req.query.mandatory ? parseInt(req.query.mandatory) : undefined;
req.query.optional = req.query.optional ? parseInt(req.query.optional) : null;
if(isNaN(req.query.mandatory) || isNaN(req.query.optional)) {
  return next(error);
}
```
