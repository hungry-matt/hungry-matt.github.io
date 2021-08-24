---
layout: default
title: Subnet
parent: AWS
---

Table of contents

- TOC
{:toc}

---

# Subnet

- 서브넷은 더 많은 네트워크망을 만들기 위해 메인 네트워크인 VPC를 잘개 쪼개어 만든것이다.

- VPC가 Region을 기준으로 생성되었다면 서브넷은 AZ(Availability Zone, 가용 영역)를 기준으로 생성되며 서브넷 안에는 EC2, S3 등의 리소스가 들어간다.

## Public Subnet & Private Subnet

VPC에서는 Public Subnet과 Private Subnet으로 구분된다.

### Public Subnet

- Public Subnet은 외부에서 접근 가능한 네트워크이다.

- Public IP와 Elastic IP를 가진 인스턴스를 내부에 가지고 있을 수 있다.

### Priavet Subnet

- 기본적으로 외부와 연결이 차단되어 있다.

- Private Subnet의 인스턴스들은 Private IP만 가질 수 있으며 다른 서브넷과의 연결만 가능하다.