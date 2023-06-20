
### Cloudwatch metrics
- 계정에서 일어나는 모든일에 지표를 제공
- Dimension : Attribute of a metric (instance Id, Environment, etc ...)
- Up to 10 dimensions per Metric

### Metric Streams
- near-real time deleiver and low latency

### Cloudwatch logs
- AWS 에서 로그를 저장하는 최고의 장소
- 로그 그룹 : arbirary name, usually representing an application
- Log streams : instances within application / log files/ containers
- 로그 만료 정책을 정할 수 있다

### CloudWatch Logs - Sources
- Beanstalk : collection of logs from application

- Filter expression 을 사용해, 어느 아이피의 로그인지, 에러인지 찾을수 있다.

### Subscription Filter
- Cloudwatch logs 상단에 적용하여, 원하는 목적지로 로그를 보내는 필터를 의미함.

### Cloudwatch Alarms
- states
    - OK
    - Insufficient-data
    - alarm

### EventBridge
(old)Cloudwatch event -> (New) EventBridge
- schedule cron jobs
- event pattern : ex) IAM root user sign in Event -> SNS topic with Email Notification
- Trigger lambda functions, send SQS/SNS messages

- **CloudTrail** 과 조합이 찰떡임 -> any API Call 가로채서 eventBridge 에서 사용 가능
- Event Filtering 도 가능 -> Json 으로 어떤 이벤트인지 document 화 할 수 있다.


### EventBus
- 이벤트를 받는 역할.
- default EventBus 에 전송된 이벤트는 Rule 을 통해 사용자가 설정한 이벤트 패턴을 기반으로 필터링 되고 필요한 대상에게 전달됨.

- 기본 이벤트 버스와 파트너 이벤트 버스 덕분에, 계정에서 발생하는 이벤트에 반응 가능
- 사용자 지정 이벤트 버스로 자체 이번트에 반응할 수 있다.
- 스키마 레지스트리 기능 (?): event 의 스키마를 읽을 수 있다.
- 리소스 기반 정책 : 다른 계정의 이벤트를 수락/거부할 수 있다.



## CloudWatch Insights / Operational Visibility
### CloudWatch Container Ingsights
- ECS, EKS, K8s on EC2, Fargate 서비스의 metrics / logs 측정할 때 사용
### Lambda Insights
- Serverless 애플리케이션의 세부 모니터링이 필요할 때 사용됨
### Contributor Insights
- you can find bad hosts, find heaviest network user
- 로그를 통해 상위 contributors(네트워크 사용자)를 찾을 수 있다.
### CloudWatch Application Insights
- 자동 생성 대시보드로 AWS Service 나 애플리케이션의 트러블 슈팅을 지원


### CloudTrail
- enabled by Default
- get an history of all events/API calls made within my aws account by:
    - console
    - sdk
    - cli
    - aws services
- 클라우드트레일의 모든 로그를 S3 나 클라우드 와치에 모아 볼 수 있다.
- 모든 리전에 적용할수도 있고, 단일 리전에 적용도 가능
- Events
    - Management Events:
        - 내 AWS 계정에서 일어나는 작업
        - 기본적으로 클라우드트레일은 management events 를 기록하도록 설정돼있다.
    - data events:
        - by default, are not logged (because high volume operations)
        - s3 object-level activity : can seperate Read and Write Events
        - AWS Lambda function execution activity
    - CloudTrail Insights Events:
        - 추가 비용 지불
        - detect unusual patterns
- Events Retention
    - stored for 90 days in CloudTrail
    - log them to S3 and use Athena for further storing
- EX)
    - DynamoDB 에서 테이블을 삭제할 때, API call 나감
    - Log Api Call into CloudTrail (사실 모든 종류의 API call logging 한다)
    - CloudTrail 에서 event 발행하여 EventBrdige에 전송하면, EventBridge 에서 SNS 로 alert 를 보낸다.

### AWS Config
- Auditing / Recording compliance of your AWS resources
- Helps record configurations and changes over time
- AWS Config 가 도움을 줄 수 있는 문제들
    - 버킷에 public access 가 있었는지 확인할 때
    - ALB 설정이 몇번이나 수정됐는지
    - 허가되지 않은 SSH 접근이 있었는지
- 각 리전별로 설정해야함
- Configurations Data 를 S3에 따로 저장하여 Athena 로 분석할 수도 있다.

- 75가지 미리 규정된 룰이 있고, 룰을 자체 제작할 수도 있다.
- 유료 서비스로 굉장히 비싸질 수 있다

### Cloudwatch vs CloudTrail vs Config 비교

### CloudWatch
- 성능 모니터링 (metrics, CPU, network etc) & Dashboards
- Events & Alerts
- Log Aggregation & Analysis

### CloudTrail
- API Call 기록이 주 목적
- 특정 리소스의 Trails 정의 가능
- Global service

### Config
- 구성 변경을 기록하고, 규정준수 규칙에 따라 리소스를 평가.
- Get timeline of changes and compliance

### ELB 예시
- 클라우드 와치
    - 유입 트래픽 모니터링
    - 에러 코드 비율 시각화
    - 로드밸런서 성능 대시보드 생성
- Config
    - ELB 의 시큐리티 그룹 규칙 추적
    - 규칙 변경 기록
    - SSL 인증서가 항상 보장되는지 추적
- CloudTrail
    - ELB API call 을 통해 어떤 변경사항이 누구에 의해 일어났는지 추적
