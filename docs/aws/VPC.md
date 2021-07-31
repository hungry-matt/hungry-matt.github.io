---
layout: default
title: VPC
parent: AWS
---

Table of contents

- TOC
{:toc}

---

# VPC

- Virtual Private Cloud

- 클라우드 위에 생성한 리소스들은 기본적으로 네트워크를 통해 접근할 수 있다. 이 때 하나의 계정에서 생성한 리소스들의 `격리된 네트워크를 만들어주는 기능이 VPC이다.`

- EC2 인스턴스나 RDS 데이터베이스 같은 AWS 리소스등이 격리된 네트워크 위에서 생성되는 것이다.

- 논리적으로 격리된 네트워크이기 때문에 다른 사람들은 접근할 수 없다.

### VPC는 IPv4 CIDR 블럭을 필수적으로 가진다.

- CIDR(사이더) 블럭은 IP의 범위를 지정하는 방식이다.

- IP 주소와 슬래시(/) 뒤에 따라오는 넷마스크 숫자로 구성되어 있다.

- `IP 주소` + `/` + `넷마스크`

- 넷마스크 숫자는 IP의 범위를 나타낸다.

- 예를 들어 192.168.0.0/32는 192.168.0.0을 가리키며 범위는 지정된 IP부터 `2^(32-n)`개가 된다.

- 192.168.0.0/24는 2^(32-24)=256개의 IP 주소를 의미하며 192.168.0.0에서 192.168.1.255까지의 IP를 의미한다.

# Reference

- [Amazon VPC 작동 방식](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/how-it-works.html)
- [가장 쉽게 VPC 개념잡기](https://medium.com/harrythegreat/aws-%EA%B0%80%EC%9E%A5%EC%89%BD%EA%B2%8C-vpc-%EA%B0%9C%EB%85%90%EC%9E%A1%EA%B8%B0-71eef95a7098)
- [만들면서 배우는 아마존 VPC](https://www.44bits.io/ko/post/understanding_aws_vpc#vpc)
