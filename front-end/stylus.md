# [Stylus](http://learnboost.github.io/stylus/)

## media query에 변수 사용하기
media query를 사용하면 한 사이트에서 사용하는 해상도의 범위가 보통 몇 가지로 정해져 있으므로
반복해서 계속 사용하다 보면 금새 귀찮아진다.(특히 미디어 쿼리는 좀 복잡하기도 하고...)

```sass
foo = 'width'
bar = 30em
@media (max-{foo}: bar)
  body
    color #fff
```

위처럼 미디어 쿼리내에서도 변수를 사용할 수 있지만 거의 같은 미디어 쿼리를 반복해서 사용하게 되므로
다음과 같이 미디어 쿼리 자체를 변수로 만들어서 사용할 수도 있다.
`@media`부분가지 변수에 넣지 않은 이유는 다른 Stylus 코드와 잘 구분이 안되서 오히려 헷갈릴 것
같아서이다.

```
desktop-media = "screen and (min-width: 801px)"
mobile-media = "screen and (max-width: 800px)"

@media desktop-media
  #lhero-h
    bottom 50%
@media mobile-media
  #lhero-h
    bottom 50%
```
