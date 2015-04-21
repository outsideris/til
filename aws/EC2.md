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

# AWS EC2 Auto Scale-Up 하기 

현재는 EC2 2개 + ELB 로 운영 하고 있지만 기본적으로 EC2 인스턴스는 그룹으로 관리를 하고, 
그룹 단위로는 기본적인 Scale-Up + ELB 셋팅을 하는 것이 좋을 것 같다. 
이렇게 관리를 하면 나중에 Zone 단위의 EC2 환경 확장이 용이할 뿐만 아니라, 트래픽 분산 및 폭증에 유연하게 대응할 수 있기 때문이다.

EC2 의 Scale-Up 기능 적용을 위해서는 아래의 작업 절차를 수행해야 한다.

1. AWS Auto Scaling Command Line Tools 를 설치한다.
2. 환경변수를 설정한다.
3. Launch Configuration 생성한다.
4. Auto Scaling Group 생성한다.
5. Policy 생성한다.
6. Cloud Watch 를 활용해서 Trigger 설정한다.

… 테스트 후 추가 ...