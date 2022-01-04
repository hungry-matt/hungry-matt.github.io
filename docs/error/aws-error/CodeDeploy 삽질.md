---
layout: default
title: CodeDeploy 삽질
parent: aws-error
grand_parent: Error
---

Table of contents

- TOC
{:toc}

---

# 배포 권한 미설정

신규 프로젝트 자동 배포 중 Deploy 단계에서 오류가 발생하였다.

Pipeline의 오류 메시지는 다음과 같이 출력 되었지만 정확한 원인을 알 수 없었다.

```
ERROR: Deployment failed. Fetching deployment information...
```

좀 더 정확한 원인 확인을 위해 AWS CodeDeploy의 로그를 확인해 보니 다음과 같다...

```
The overall deployment failed because too many individual instances failed deployment, too few healthy instances are available for deployment, or some instances in your deployment group are experiencing problems.
```

## 해결

### 1. 배포 대상 서버에 CodeDeploy Agent 실행 여부 확인

```
sudo service codedeploy-agent status
```

### 2. CodeDeploy Log 확인

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

## Reference

- https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/codedeploy-agent-operations-verify.html
- https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/codedeploy-agent-operations-install-linux.html
- https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/troubleshooting-deployments.html

# 인스턴스의 날짜와 시간이 잘못 설정 되었을 경우

아무 문제가 없이 배포가 잘 되다가 어느날 배포 단계에서 오류가 발생했다.

codedeploy-agent 상태를 확인 했지만 문제가 없었고 로그를 확인 해보니 다음과 같았다.

```
2022-01-04 00:05:18 ERROR [codedeploy-agent(1073)]: InstanceAgent::Plugins::CodeDeployPlugin::CommandPoller: Cannot reach InstanceService: Aws::CodeDeployCommand::Errors::InvalidSignatureException - Signature not yet current: 20220104T000518Z is still later than 20220104T000203Z (20220103T235703Z + 5 min.)
```

오류 내용은 agent의 시간과 인스턴스의 시간이 맞지 않아 `InvalidSignatureException` 예외가 발생했다.

맞지 않는 시간을 다시 맞춰주면 쉽게 해결 할 수 있다.

해결 방법은 아래 AWS 공식 문서 내용을 그대로 따라하였다.

## Reference
- https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/set-time.html#configure-amazon-time-service-ubuntu
- https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/troubleshooting-ec2-instances.html#troubleshooting-instance-time-failures
- https://sarc.io/index.php/aws/1274
