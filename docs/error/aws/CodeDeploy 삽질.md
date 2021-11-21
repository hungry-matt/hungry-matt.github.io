---
layout: default
title: CodeDeploy 삽질
parent: AWS
grand_parent: Error
---

# 문제

신규 프로젝트 자동 배포 중 Deploy 단계에서 오류가 발생하였다.

Pipeline의 오류 메시지는 다음과 같이 출력 되었지만 정확한 원인을 알 수 없었다.

```
ERROR: Deployment failed. Fetching deployment information...
```

좀 더 정확한 원인 확인을 위해 AWS CodeDeploy의 로그를 확인해 보니 다음과 같다...

```
The overall deployment failed because too many individual instances failed deployment, too few healthy instances are available for deployment, or some instances in your deployment group are experiencing problems.
```

# 해결

## 1. 배포 대상 서버에 CodeDeploy Agent 실행 여부 확인

```
sudo service CodeDeploy-agent status
```

## 2. CodeDeploy Log 확인

codedeploy는 정상적으로 실행 되고 있었고 로그를 열어 확인해 보니 배포 권한이 없다고 한다...

```
$ vi /var/log/aws/codedeploy-agent/codedeploy-agent.log
```

ec2 인스턴스를 새로 만들면서 CodeDeploy AMI 역할 부여를 깜박한거 같다.

AMI 역할 수정후에는 CodeDeploy-Agent를 다시 시작해줘야 한다.

```
$ sudo service codedeploy-agent restart
```

끝.

# Reference

- https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/codedeploy-agent-operations-verify.html
- https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/codedeploy-agent-operations-install-linux.html
- https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/troubleshooting-deployments.html