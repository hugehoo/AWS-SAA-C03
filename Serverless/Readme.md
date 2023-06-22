
### 서버리스
- 서버를 관리할 필요가 없음
- deploy function
- Serverless == FaaS
- Serverless 란 서버가 없다는 것이 아니라, 서버가 보이지 않거나 서버를 프로비저닝 하지 않는 다는 의미.

### 람다!
- 람다는 어떤 도움이 되나?
    - EC2 : 프로비저닝이 필요함 -> 프로비저닝할 메모리와 CPU 크기가 제한됨
- 제한시간이 있어 실행시간이 짧다.(15분 : Timeout)
- Run On-Demand : 람다를 호출하지 않으면 실행되지 않고, 람다를 호출할 때만 실행됨 (비용이 청구)
- 스케일링도 자동화 됨
- Easy Pricing
    - pay per requested and compute time
- Easy Monitoring through AWS Cloudwatch

- Lambda container image
    - ⭐️ 람다에서 컨테이너 이미지를 실행하려면 Lambda Runtime API 를 구현해야한다
    - 그렇지 않으면, ECS/Fargate 에서 컨테이너를 실행해야 한다.

- ex) Serverless Thumbnail creation
    - S3에 이미지 업로드 시
        - 람다 트리거 하여, 섬네일 이미지 생성하여 S3버킷에 별도 저장 가능
        - 혹은 RDS에 이미지 메타데이터 가공하여 저장 가능.
    - 즉 main api server는 s3 이미지 업로드만 책임지면, lambda에서 그외의 작업 수행
- Execution
    - memory allocation : 128mb - 10Gb
    - maximum execution time : 900 seconds(15mins)
    - 디스크 용량 in function container (in/tmp) 512mb ~ 10gb
- Deployment
    - lambda function deployment size : (commpressed zip) : 50mb
    - size of uncompressed deployment : (code + dependencies) : 250mb
      **⭐️⭐️⭐️ 예를 들어, 30Gb Ram 과 30분의 실행 시간이 필요하고, 3Gb의 큰 파일이 있는 상황에선 워크로드 처리에 람다 함수가 적합하지 않다는걸 파악할 수 있어야 한다.**

### Customization at the Edge
- cloud front 를 사용할 땐 콘텐츠를 엣지 로케이션을 통해 배포한다.
- Edge function:
    - code attached to cloudfront distributions
    - runs close to users to minimize latency
- use case : customize the cdn content

### Cloudfront functions & lambda@Edge use case
• Website Security and Privacy  
• Dynamic Web Application at the Edge  
• Search Engine Optimization (SEO)  
• Intelligently Route Across Origins and Data Centers • Bot Mitigation at the Edge  
• Real-time Image Transformation  
• A/BTesting  
• User Authentication and Authorization  
• User Prioritization  
• User Tracking and Analytics

### Cloudfront functions
- 고성능, 고확장성 필요할 때 뷰어 요청과 뷰어 응답에만 사용할 수 있따.
- nodejs 지원

### Lambda@Edge
- nodejs, python 지원

### Lambda in VPC
- Dynamo DB 는 VPC 내부에 넣을 수없나? (public source 라길래)
- 람다가 private subnet 내부에 Elastic Network Interface 를 생성하여 내부 리소스에 접근 가능
    - 근데 걍 vpc 내부에 람다를 설치하면 되는 문제 아닌가?

### RDS Proxy with Lambda
- 람다가 DB에 곧바로 접근 시 너무 많은 부하 생길 수 있다.
- RDS proxy
    - 이 경우엔 람다 함수는 vpc 내부에 반드시 배포되어야 함.
    - rds proxy는 private subnet 에 있어 외부에서 접근 불가이기에

### Dynamo DB
- Fully managed db
- highly Available
- NoSQL service
- support transaction (how?)
- distributed db
- fast and consistent in performance
- intergrated with IAM for security, auth, and administration
- No maintenance or patching required. always available
- 2types of Table class
    - Standard : frequently accessed
    - Infrequent Access(IA)


DynamoDB-Basic
- 테이블로 구성되며, DB를 생성할 필요가 없다
- DB가 이미 존재하는 서비스
- 각 테이블엔 기본키가 생성됨
- 행을 무한히 추가할 수 있다.
- maximum size of item is 400kb -> 큰 객체를 저장할 땐 적합하지 않다.
- 시험 : 스키마를 빠르게 전개(evolve)할 때 dynamo db를 선택하면 좋다.

Read/Write Capacity modes
- 기본 설정은 프로비저닝 되어 있음
- Provisioned Mode (default)
    - 미리 capacity 를 고려해야한다.
    - pay for provisioned Read Capa Units(RCU) & Write Capa Units(WCU)
    - 오토스케일링 되므로 RCU, WCU 를 자동으로 늘릴 수 있다.
    - 서서히 오토스케일링 되기 때문에 급격히 트랜잭션이 증가할 땐 적합한 선택이 아니다. (on-demand를 선택해야 함)

- On-Demand Mode
    - 미리 용량 계획을 하지 않으므로, RCU 나 WCU 개념 자체가 없다.
    - 읽기/쓰기 용량이 워크로드에 따라 자동으로 확장됨
    - 정확히 사용한 만큼 비용으 ㄹ지불함.
    - 워크로드를 예측 할 수 없거나 급격히 증감할 때 사용할 수 있다.


**Q. 근데 서버리스랑 dynamoDB 랑 뭔상관이야?**
- 특성이 비슷하지 않나? 관리할 필요가 없는 db 이니까.

### Advanced Feature
- DAX : DynamoDB Accelerator(DAX)
- DAX (**in-memory cache** for dynamo DB)
- help solve read congestion(읽기 작업 병목) by caching
    - `ProvisionedThroughputExceededException` 예외 해결
- microseconds latency for cached data
- 5mins TTL for cache(default)
-
### DAX vs Elastic Cache
- DAX 는 Dynamo 앞에 위치하여, individual objects 를 캐싱하고, query & scan cache 에 유용함
- ElasticCache 는 aggregation result 저장에 유용

### Stream Processing
- DynamoDB Streams
    - 24hours retention
    - limited number of consumers

### Global Tables
- 여러 리전간 복제가능
- 어느 리전이든 테이블에 read/write 가능
- two way replications
- DynamoDB Streams 를 미리 설정해야함

TTL -> 자동으로 item 삭제 after a expiry timestamp
Web Session Handling -> 세션을 두시간 동안 저장, 모든 어플리케이션에ㅔ서 접근 가능 -> 두시간 후엔 삭제하는 방식으로 작ㄷ오

### DR
- 지정시간 복구를 설정할 수 있다.
- 복구 진행할 경우 새로운 테이블 생성
- 온디멘드 백업
    - 직접 삭제할 때까지 보존된다.
    - 퍼포먼스나 성능에 영향 주지 않음
    - AWS Backup Service 에 의해 관리되고 설정할 수 있다

### S3
- export to s3 (must enable PITR)
- import from s3

### API Gateway
- 역할이 많은 것 같은데 정확히 하는 게 뭐야
- REST API 를 만들어준다는건 무슨 의미이지?

### Integrations  High Level

- Lambda Function
    - invoke lambda functions
    - easy way to expose REST API by lambda

- HTTP
    - HTTP 엔드포인트에서 Gateway 역할을 할 수 있다.
    - why? add rate limiting, caching, authentications, API keys, etc...

- API gateway - Endpoint Types
    - Edge optimized (default) : **for global clients**
        - requests are routed through the CloudFront (latency 개선)
        - API gateway 는 생성된 특정 리전에 위치하지만, 모든 Cloudfront Edge location 에서 액세스 될 수 있다.
    - Regional
        - **For clients within the same region**
    - Private
        - can only be accessed from your VPC using an interface VPC endpoint (ENI)


#오답노트
> Q. DynamoDB를 데이터 저장소로 사용하는 모바일 애플리케이션을 개발했습니다. 신규 사용자가 가입한 후 환영 이메일을 자동으로 보내려고 합니다. 이를 달성하는 가장 효율적인 방법은 무엇일까요?
- CloudWatch Event를 사용해 Lambda 함수를 매 분 실행하여 테이블에 등록된 새로운 사용자를 스캔하게 만들기
- SNS와 DynamoDB 통합을 활성화
- DynamoDB Streams을 활성화하고 이메일을 보내기 위해 Lambda 함수를 호출하도록 구성 (Answer)
- 해설 : DynamoDB Streams을 사용하면, DynamoDB 테이블의 항목 레벨 수정의 시간 순서를 파악할 수 있습니다. 이는 AWS Lambda에 통합되어 있기 때문에, 이벤트에 실시간으로 자동 응답하는 트리거를 생성할 수 있습니다.


#오답노트
>Q. 엣지 최적화 API 게이트웨이를 사용할 경우, API Gateway가 모든 AWS 리전의 CloudFront 엣지 로케이션에 존재하게 됩니다.
- 아닙니다(answer)
- 맞습니다
- 해설 : 엣지 최적화 API Gateway는 지리적으로 분산된 클라이언트에 가장 적합합니다. API 요청은 가장 가까운 CloudFront 엣지 로케이션으로 라우팅되며, 이는 지연 시간을 향상시킵니다. API Gateway는 여전히 하나의 AWS 리전에 존재합니다.


**Lambda@Edge 는 CloudFront 의 기능**
- 사용자에 가깝게 코드를 실행시켜 성능을 향상시키고 지연 시간을 줄여준다.

#오답노트
> 여러분이 가진 모바일 애플리케이션에서 애플리케이션 사용자에게 S3 버킷에 있는 개인 공간에 액세스할 수 있는 권한을 부여하려고 합니다. 어떻게 해야 합니까?
- 각각의 애플리케이션 사용자에 대해 IAM 사용자 자격 증명 생성한다.
- Amazon Cognito 아이덴티티 페더레이션을 사용한다.(해설)
- SAML 아이덴티티 페더레이션을 사용한다.
- 버킷 정책을 사용해 버킷을 퍼블릭으로 만든다.
- 해설 : Amazon Cognito을 사용해 모바일 사용자 계정을 페더레이션하거나 사용자에게 IAM 권한을 제공할 수 있으며, 사용자는 이를 통해 S3 버킷에 있는 개인 공간에 액세스할 수 있습니다.

#오답노트
> 여러분은 AWS에 호스팅할 예정인 새로운 웹 및 모바일 애플리케이션을 개발하고 있으며, 현재 로그인 및 회원 가입 페이지를 구현하는 작업을 진행하고 있습니다. 애플리케이션 백엔드는 서버리스로 되어 있으며 구현에 Lambda, DynamoDB, API Gateway를 사용하고 있습니다. 다음 중 백엔드에 대한 인증을 구성하는 데 가장 쉽고 적합한 방식은 무엇입니까?
- KMS로 암호화된 DynamoDB 테이블에 사용자의 자격 증명을 저장한다.
- KMS로 암호화된 S3 버킷에 사용자의 자격 증명을 저장한다.
- Cognito 사용자 풀을 사용한다.(해설)
- 사용자 자격 증명을 AWS Secrets Manager에 저장한다.

#오답노트
> 여러분이 운영 중인 모바일 애플리케이션에서 등록된 사용자들 모두 S3 버킷에 있는 자신의 폴더에서 이미지를 업로드 또는 다운로드할 수 있게 하려고 합니다. 또한 사용자들이 페이스북과 같은 소셜 미디어 계정을 통해 가입 및 로그인할 수 있게 하고자 합니다. 어떤 AWS 서비스는 사용해야 합니까?
- AWS Identity and Access Management (IAM)
- AWS IAM Identity Center
- Amazon Cognito (answer)
- Amazon CloudFront
- 해설 : Amazon Cognito를 사용하면 웹 및 모바일 앱에 사용자 가입, 로그인, 액세스 제어 기능을 쉽고 빠르게 추가할 수 있습니다. Amazon Cognito는 사용자를 수백만까지 확장할 수 있으며 SAML 2.0 및 OpenID Connect를 통해 Apple, Facebook, Google, Amazon과 같은 소셜 자격 증명 공급자 및 엔터프라이즈 자격 증명 공급자의 로그인을 지원합니다.


- DynamoDB : 서버리스면서 확장이 잘되는 DB
- Cognito : 서버리스 인증 계층

- mobile client 가 s3에 직접 접근하고 싶다면, cognito 와 STS 에서 임시 접근 권한을 발급받는 것이 좋다. AWS 자격 증명을 **모바일 클라이언트에 저장하는 것은 올바르지 않은 방법**이다.
- Serverless Archi 에선 API Gateway 에서 REST Request 를 캐싱할 수 있다.

- 블로그를 서버리스로 배포할 수 있나?
- 보통은 jekyl 같은걸 사용하니까 서버리스긴 한거지...?

- OAC : Origin Access Control
    -  S3가  Cloudfront를 통해서만 인가하도록 Bucket policy 를 추가.



- API gateway 는 caching 가능
- Lambda is not

### DynamoDB Global Table
- DynamoDB Streams 을 먼저 생성해야 함
- DynamoDB Streams은 DynamoDB가 변경 로그를 가져오고, 이를 사용해 다른 AWS 리전에 있는 복제 테이블이 서로의 데이터를 복제할 수 있는 기능을 활성화해 줍니다.

#오답노트
>DynamoDB Streams을 사용해 DynamoDB 테이블로 항목이 추가될 때마다 Lambda 함수가 각 아이템을 실행하도록 구성한 상황입니다. 이 함수는 추후 장기적인 처리 업무를 위해 메시지를 SQS 대기열로 삽입하게 되어 있습니다. Lambda 함수를 매번 호출해보면 DynamoDB Stream을 읽을 수는 있으나 SQS 대기열로 메시지를 삽입하지는 못하는 것으로 보입니다. 이 경우, 무엇이 문제일까요?
- SQS 대기열로 메시지를 삽입할 때는 Lambda를 사용할 수 없으므로, EC2 인스턴스를 대신 사용해야 함
- Lambda 실행 IAM 역할에 권한이 없음 (Answer)
- Lambda 보안 그룹이 SQS에 대한 아웃바운드 액세스를 허용해야만 함
- SQS 보안 그룹이 AWS Lambda를 허용하도록 수정되어야 함

Q. SNS 는 데이터 보관 안되는가? SQS 는 메시지를 며칠간 보관할 수 있댕
- Pub/Sub 구조라 안되는가?

#오답노트
> Q. 여러분은 전 세계에서 이미지를 다운로드하는 사진 공유 웹사이트를 실행하고 있습니다. 여러분은 15GB가 넘는 크기의 아름다운 산 이미지의 마스터 팩을 매 달 웹사이트에 게시합니다. 이 콘텐츠는 Elastic File System(EFS)에 호스팅되어 있으며, Application Load Balancer와 한 세트의 EC2 인스턴스을 통해 배포되었습니다. 여러분은 매달 높은 양의 트래픽을 경험하고 있으며, 이로 인해 EC2 인스턴스의 작업량과 네트워크 비용이 증가하고 있는 상황입니다. 웹사이트를 리팩터링하지 않고 EC2 부하와 네트워크 비용을 줄이기 위해서는 어떤 방법이 권장될까요?
- 마스터 팩을 S3으로 호스팅
- Application Load Balancer 캐싱 활성화
- EC2 인스턴스 스케일 업
- Amazon CloudFront 배포 생성 (answer, 아래 내용과 연관)

### Software updates offloading?
- Using Cloudfront
    - will cache software update files at the edge
    - software `update files` are not dynamic, they'r static (never changing)
    - EC2 instances aren't serverless
    - **Easy way to make an existing application more scalable and cheaper**