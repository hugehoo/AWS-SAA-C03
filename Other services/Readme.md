### Benefits of CloudFormation
- Infra as code
- 클라우드 작동 방식의 변경을 코드로 검증/검토할 수 있다.
- 비용적으로 절감할 수 있는 부분 파악 가능
- 생산성
  - 하나하나 다 만들지 않아도 된다. (supports all aws resources)
- CloudFormation은 AWS 의 코드형 인프라의 기반이 되는 서비스다.
- 서비스간의 다이어그램도 자동으로 만들어준다.

### SES
- Simple Email Service
- fully managed service to send emails securely, globally and at scale

### Pinpoint
- SMS 보낼 때 사용
- 보내고, 답장 받는 것도 가능
- 마케팅 이메일 대량 전송
- sns, kinesis data firehose 등 서비스와 결합하여 자동화 할 수 있다. 
- 메시지 템플릿, 전달 일정, 대상 세그먼트, 전체 캠페인 등을 직접 만들지 않고 Pinpoint 서비스로 관리할 수 있다.
  - 반면 SNS 또는 SES 에서는 여러분이 각 메시지의 대상, 내용, 전달 일정을 관리해야 함.

### SSM Session Manager
- allows you to start a secure shell on EC2 and On-premise servers
- ssh access, bastion hosts, or SSH keys 필요없다.
- 22번 포트도 필요없어지므로 보안이 더욱 강화됨

### Cost Explorer (비용 탐색기)
- 사용자 정의 보고서를 생성해 비용과 데이터 사용량 분석
- 청구서 요금을 낮추기 위한 최적의 절감형 플랜도 제공


### Elastic Transcoder
- converting media file
- put medie file in s3 bucket -> transcoding pipeline 
-> converted video file being put into output bucket 
-> smartphones, Tablets 등으로 전송 가능

- 트랜스코딩된 시간만큼만 비용을 지불하면 된다. 
- high scalable, fully managed, cost effective


###  AWS Batch
- fully managed batch processing service at any scale
- runs 100,000s of computing batch jobs
- 배치 작업은 시작 시점이 있다. 동적으로 EC2 인스턴스와 스팟 인스턴스를 시작함. 
- 배치 작업 정의 -> ECS 에서 실행될 수 있는 것이라면 배치에서도 실행될 수 있다.

Lambada vs Batch
- Lambda
  - Time limited(15mins)
  - Limited Runtimes
  - Limited Temporary disk space
  - Serverless
- Batch
  - No time limit
  - any runtime as long as it's packaged as a Docker Image
  - Rely on EBS for disk space
  - Relies on EC2(which means not serverless)


### Amazon Appflow
- SalesForce, Zendesk, SAP, Slack 등의 다양한 SaaS 를 통합할 수 있다.
- data transformation, encrypted 등 다양한 기능 제공
- sources -> destinations 로 appflow 롤 통해 데이터를 전송할 수 있다.

