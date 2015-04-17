# AWS 의 RDS 백업/복구

AWS 의 RDS 는 백업 기능으로 `자동백업` 과 `스냅샷` 기능을 지원한다. RDS 를 복구해야하는 상황은 여러가지가 있겠지만, 보통의 경우는 특정 시점 이후로 서버/클라이언트 버그 혹은 DB 마이그레이션 실패로 인해 데이터가 유실되는 상황일 것이다. 따라서 정상적으로 운영되던 상태의 데이터로 복구해야하는데 이런 상황에서는 `자동백업` 기능을 활용하면 된다. `스냅샷` 기능은 `자동백업` 상황 외 특정 의미있는 순간을 백업하는 용도로 활용하면 되지 않나 싶은데 DB 마이그레이션, 서버/클라이언트 메이저 업데이트 같은 상황이다.

### 1. RDS 자동백업 설정하기
`자동백업` 의 경우, AWS 에서 1일 간의 백업은 무료로 제공해준다. 처음 RDS 인스턴스를 생성할 때 설정할 수 있고, 이후에 수정가능하다. `Backup Retention Period` 설정으로 백업 기간을 설정 할 수 있다. 예를들어 Volo 의 자동백업 설정은 아래와 같다.
- Volo-Production
  - Backup Retention Period : 7 days
  - Backup Window : Start Time - 19:00 UTC(한국 기준 새벽 4시)

### 2. RDS 자동백업으로 복원하기
긴급한 상황에서 RDS 백업데이터를 기준으로 복원하는 경우, RDS 인스턴스에서 `Restore to Point in Time` 기능을 활용하면 된다. `Backup Retention Period` 로 설정한 기간 안의 선택한 특정 시점(1분 단위) 기준의 데이터 복원이 가능하다. 복원은 10-20분 정도 소요되며, 새로운 RDS 인스턴스로 생성된다. 해당 인스턴스 설정은 기본으로 백업된 인스턴스와 같으며 수정 가능하다.