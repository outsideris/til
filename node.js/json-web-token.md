# [Json Web Token](http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32)

## `alg`를 조작하는 보안 취약점
[Critical vulnerabilities in JSON Web Token libraries](https://auth0.com/blog/2015/03/31/critical-vulnerabilities-in-json-web-token-libraries/)
를 보고 정리한 내용.(완전히 이해했는지 정확하지 않음)

JWT에는 헤더에 사용한 알고리즘을 지정하는 `alg`필드가 있는데 공격자가 이를 조작해서 보낼 수 있다.
`alg`에는 `none`값도 존재하는데 이는 이미 인증한 토큰을 재인증하지 않으려고 사용하고 있으나
일부 구현체에서 `none`을 사용한 경우 인증된 토큰으로 취급해 버려서 토큰을 강제로 인증시킬 수 있는
문제가 있었으나 지금은 거의 다 해결되었다.

하지만 공격자가 `alg`를 조작할 수 있다는 기본적인 문제는 여전히 존재한다. 그래서 비대칭키를 사용하는
시스템에서 HMAC으로 바꾸어서 공격하는 것이 가능하다. 토큰 인증 API는
`verify(string token, string verificationKey)`와 같은 형태이다. 그래서 HMAC을
사용한다면 `verify(clientToken, serverHMACSecretKey)`가 되고 비대칭키를 사용하는 경우에는
`verify(clientToken, serverRSAPublicKey)`가 된다. 여기서 중요한 부분은 두번째 인자가
비대칭키라면 공개키를 사용하지만 HMAC이라면 HMAC 시크릿을 사용하게 된다.

그래서 시스템이 RSA같은 비대칭키 알고리즘을 사용하고 있다면 공개키는 누구나 알고 있으므로 다음과 같이
공개키를 사용해서 HMAC 알고리즘으로 토큰을 생성할 수 있다.

```
forgedToken = sign(tokenPayload, 'HS256', serverRSAPublicKey)
```

서버는 이를 HMAC 방식으로 간주하므로 공개키로 인증을 하면 시크릿키가 동일하므로 당연히 통과하게 되고
인증되는 토큰을 강제로 만들어 낼 수 있다.

이는 현재 JWT 스펙의 취약점으로 보이고 글에서도 `verify`에서 알고리즘을 지정할 수 있도록 해서
공격자가 공격을 못하게 하거나 스펙에서 `alg`를 빼는 부분을 제안하고 있다.
