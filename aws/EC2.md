# AWS EC2 배포 전략

## EC2 런타임 변경 전략

어플리케이션의 런타임 환경이 변경되는 경우(Docker 기반의 환경을 적용하는 상황 등), 
새롭게 변경된 EC2 인스턴스 그룹의 가장 간단한 배포 및 적용 방법은 ELB 를 사용한 인스턴스 그룹 스위칭이다. 
ELB 를 활용하면, 간단하게 무중단으로 런타임 환경이 다른 인스턴스 그룹으로의 전환이 가능하다.

아래는 간단한 EC2 런타임 변경 시나리오이다.
- Docker 기반의 어플리케이션을 EC2 에 구축하고 테스트한다.
- ELB 를 EC2 인스턴스 그룹 앞단으로 배치하고, Warm-Up 한다.
- Route53 을 활용해서 기존의 ELB 를 새로운 ELB 로 바로 변경하거나 R-R 처리하면서 서서히 변경한다.
- 롤백하는 경우, Route53 에서 기존 ELB 로 변경한다.

물론, 서버 구성이 점점 복잡해지면 더 세밀한 전략이 필요할 것 같다. 아래 자료를 참고해서 조금 더 연구해야 할 것 같다.
- http://www.slideshare.net/awskr/aws-kr-ug-1
- http://www.slideshare.net/awskr/aws-kr-ug-1-aws
- http://www.slideshare.net/serialxnet/kgc2013-1?qid=6a8cdf77-7570-4c91-93b9-0123d8823e74&v=default&b=&from_search=9
