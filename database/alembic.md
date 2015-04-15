# [alembic](https://bitbucket.org/zzzeek/alembic)

## 설치
alembic은 [SQLAlchemy](http://www.sqlalchemy.org/)를 사용하는 데이터베이스 마이그레이션
도구로 Python으로 작성한다.

pip가 설치되어 있다면 pip로 설치할 수 있다.

```
pip install alembic
```

## 사용방법
원하는 위치에서 `alembic init FOLDER_NAME`을 실행하면 다음과 같은 구조의 파일이 생긴다.

```shell
├── FOLDER_NAME
│   ├── README
│   ├── env.py
│   ├── env.pyc
│   ├── script.py.mako
│   └── versions/
└── alembic.ini
```

`alembic.ini` 파일에서 `sqlalchemy.url` 부분에 DB 정보를 추가한다.

```python
sqlalchemy.url = postgresql://wanderworld:wanderworld@localhost/wanderworld
```

디비에 변경사항이 있는 경우 `alembic revision -m ""`을 실행하면 `versions` 폴더 아래에
새로운 마이그래이션 파일이 생성된다.

```shell
$ alembic revision -m "create reset_password table"
  Generating wanderworld-web/bin/alembic/versions/3b90a575c8f3_create_reset_password_table.py ... done
```

생성된 파일은 다음과 같이 생겼다.

```python
"""add source column into travel

Revision ID: fa9aac668c7
Revises:
Create Date: 2015-04-15 15:37:19.644703

"""

# revision identifiers, used by Alembic.
revision = 'fa9aac668c7'
down_revision = None
branch_labels = None
depends_on = None

from alembic import op
import sqlalchemy as sa
from sqlalchemy.dialects.postgresql import JSON

def upgrade():
    pass

def downgrade():
    pass
```

revision 메세지에 넣은 내용이 파일명과 파일 상단에 들어가므로 메시지를 명시적으로 주는 것이 좋아
보인다. 상단에는 Alembic이 마이그레이션을 하기 위한 정보가 들어있고 실제 수정해야 할 부분은
`upgrade()`와 `downgrade()`부분이다. 이 각 부분에 마이그레이션을 수정되는 부분과
롤백에 대한 코드를 작성한다.(이 코드는 SQLAlchemy를 사용한 코드이다.)

```
from sqlalchemy.dialects.postgresql import JSON

def upgrade():
    op.add_column('travels', sa.Column('source', JSON))


def downgrade():
    op.drop_column('travels', 'source')
```

`alembic upgrade head`를 실행하면 마이그레이션이 디비에 마이그레이션 내용이 디비에 적용되고
`alembic_version`이라는 추가 테이블이 디비에 생성된다.

```shell
$ alembic upgrade head
INFO  [alembic.migration] Context impl PostgresqlImpl.
INFO  [alembic.migration] Will assume transactional DDL.
INFO  [alembic.migration] Running upgrade  -> fa9aac668c7, add source column into travel
```

SQL을 직접 사용하고 싶다면 `op.execute()`를 사용한다.

```python
def upgrade():
    op.execute("""
        CREATE TABLE IF NOT EXISTS reset_password (
            "userId" INTEGER CONSTRAINT reset_password_pkey PRIMARY KEY,
            hash VARCHAR(255) NOT NULL,
            "expiredAt" TIMESTAMP NOT NULL,
            "createdAt" TIMESTAMP NOT NULL,
            "updatedAt" TIMESTAMP NOT NULL
        )
    """)
    op.execute("""
        CREATE INDEX reset_password_hash_idx ON reset_password (hash)
    """)


def downgrade():
    op.execute("""
        DROP TABLE reset_password
    """)
    op.execute("""
        DROP INDEX reset_password_hash_idx
    """)
```
