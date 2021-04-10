---
layout: post
title: "amazon RDS (PostgreSQL)를 접근하는 aws Lambda 함수 만들기."
date:   2020-02-01
author: 변준석
categories: Serverless, Python, aws, Lambda, RDS
comments: true
---

# `psycopg2` 임포트하기
사용하고 있는 RDS에 접근해서 쿼리 요청이 오면 결과를 내어줘야하는 함수를 만들어야 합니다. 지금 사용하고 있는 RDS는 PostgreSQL이며, 이 데이터베이스에 접근하기 위해서는 `psycopg2` 라이브러리를 사용해야합니다. 

```
"Unable to import module 'lambda_function': No module named 'psycopg'"
```
aws Lambda 에서는 기본 패키지로 `psycopg2`를 지원하지 않습니다. C 구현체 `libql`의 파이썬 Wrapper이기 때문인 것 같습니다. 

실제로, `platform.platform()`을 통해 커널 정보를 확인해보니 아래와 같습니다.
```
"Linux-4.14.138-99.102.amzn2.x86_64-x86_64-with-glibc2.2.5"
```
아마존 리눅스의 AMI 를 기반으로 Lambda가 실행되기 때문에 `libpq-dev`라는 라이브러리가 포함되지 않았다고 생각할 수 있습니다.

깃허브에 누군가가 올려놓은 `psycopg2` 빌드를 사용해봤고 사실 python 3.8을 쓰고싶어서 직접 빌드도 해봤습니다. 

![](/assets/post_image/2020-02-06/ami.png)

```
sudo yum install git gcc bison flex -y
git clone https://github.com/postgres/postgres.git
cd postgres
./configure --prefix /home/ec2-user/postgres/ --without-readline --without-zlib
make
make install
```
아래의 과정을 거치면 보개되는 말은 `PostgreSQL installation complete.` 이며, Postgres를 빌드했으니 `Psycopg2`를 빌드할 차례. 그 전에 Python 3.8을 빌드해야 합니다.
```
sudo yum install gcc openssl-devel bzip2-devel libffi-devel -y
cd /opt
sudo wget https://www.python.org/ftp/python/3.8.0/Python-3.8.0.tgz
sudo tar xzf Python-3.8.0.tgz
cd Python-3.8.0
sudo ./configure --enable-optimizations
sudo make altinstall
sudo rm /usr/src/Python-3.8.0.tgz
```

```
cd ~
git clone https://github.com/psycopg/psycopg2.git
vim ./setup.cfg
```
vim으로 파일을 수정해줍니다.
* `pg_config=/home/ec2-user/postgres/bin/pg_config`
* `static_libpq=1`

마지막으로 빌드를 수행합니다.
```
python3.8 setup.py build
```

그럼 개발용 랩탑에서 새로운 터미널을 띄운 후 파일을 가져와줍니다.
```
scp -r -i diko_byun_aws.pem ec2-user@ec2-XXX-XXX-XXX-XXX.ap-northeast-2.compute.amazonaws.com:~/psycopg2 ~/Downloads
```

이 파일을 lambda 의 환경에 .zip파일 또는 S3로 업로드합니다.

![](/assets/post_image/2020-02-06/lambda.png)

# lambda 함수 작성
aws Lambda 가 제공해주는 환경 변수 기능을 이용하여 데이터베이스의 정보를 하드코딩하지 않고 사용할 수 있습니다.

```python
import json
import os
import psycopg2

conn = psycopg2.connect(
        host=os.environ['DB_HOST'],
        database=os.environ['DB_NAME'],
        user=os.environ['DB_USER'],
        password=os.environ['DB_PASSWORD'],
        port=os.environ['DB_PORT'],
    )

def lambda_handler(event, context):
    # TODO implement
    return {
        'statusCode': 200,
        'body': json.dumps(os.environ['DB_HOST'])
    }
```

