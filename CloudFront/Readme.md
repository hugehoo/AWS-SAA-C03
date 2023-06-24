
# AWS Cloudfront

- CDN
    - Improve read performance, by using caching
    - 216 point globally
    - DDos Protection
    - Integration with Shield.

- CloudFront 와 EdgeLocation 은 다른 개념인가?
    - ㅇㅇ 다르네. CloudFront 가 사용자 request 를 받고, edge location 으로 요청을 보내 캐시를 가져옴
    - cloud front 는 global edge network.
- CloudFront 와 EdgeLocation 을 사용하면 특정 리전에 있는 S3 Bucket 을 전세계의 엣지 로케이션으로 분산시킬 수 있따.


- cdn
    - 전세계에 걸친 컨텐츠 전송 네트워크
    - Caching for a TTL (maybe a day)
    - Great for static content
- s3 cross region replication
    - caching 되지 않고 읽기전용 온리
    - 다른 리전으로의 버킷 복제인 셈.
    - Great for dynamic content that needs to be available at low-latency in few regions.

- OAC : Origin Access Control


### Cloudfront - ALB or EC2 as an origin
- EC2 only 일 때 -> EC2 는 public 이어야 함. 그래야 edge location 이 접근할 수 있다.
- ALB 사용할 땐 -> ALB 는 public, EC2 는private 이어야함. ALB 가 트래픽 보내주기에 EC2 는 Security Group 에 있어도 된다.

### GeoGraphic Restrictions
- Allow list, Block list 를 세팅할 수 있다.
- ex) 컨텐츠 저작권법으로 인한 제한을 둘 때 사용할 수 있는 option.


### Pricing
- CloudFront 는 전세계에 퍼져 있는데, 가격은 엣지 로케이션마다 상이함.
- price class All : all regions - best performance
- price class 200 : most regions, but exclude the most expensive regions
- price class 100 : only the least expensiv regions

### Cache Invalidatoin
- 모든 파일( * ) 에 대해 캐시 무효화 할 수 있고, 특정 경로에 한해서만 무효화 할 수도 있다.
-

### Global Accerlerator
- 유니캐스트 : one server holds one IP address
- 애니캐스트 : 모든 서버가 동일한 ip 를 가지고, 클라는 가까운 서버로 라우팅 된다.
    - 근데 어케 동일한 아이피를 가지지?
- GA 는 애니캐스트 방식을 사용한다.

### Global Accelerator vs CloudFront
- both use the AWS Global network and its edge locations arount the world
- Both integrate with AWS shield for DDoS protection

#### Differences
**CloudFront**
- Dynamic content (such as API acceleration and dynamic site delivery)
- Content is served at the edge
  **Global Accelerator**
- Good fit for non-Http cases(UDP, IOT, MQTT, Voice Over Ip)
- Good for Http use cases that requires static IP addresses
- Good for HTTP use cases that required deterministic, fast regional failover.
### Q. 유니캐스트 얘긴 왜한거야..?

Global Accelerator
- 가까운 지역의 ec2 에 우선 연결
- 가깝더라도 down 되면, 자동으로 다른 곳의 ec2에 연결



#오답노트
> Q. S3 버킷 상에 호스팅된 정적 웹사이트가 있습니다. 요청을 더 잘 처리하고 성능을 향상시키기 위해 S3 버킷을 가리키는 CloudFront 배포를 생성했습니다. 얼마 후, 여러분은 아직도 사용자들이 S3 버킷에서 웹사이트로 직접 액세스할 수 있다는 것을 알게 되었습니다. 여러분은 사용자들이 CloudFront만을 통해서 웹사이트로 액세스하게끔 하려 합니다. 이를 위해서는 어떻게 해야 할까요?
- 클라이언트들에게 이메일을 보내 S3 엔드 포인트를 사용하지 말 것을 요청
- CloudFront 배포를 구성해 원본 액세스 ID를 생성한 후, S3 버킷이 오직 CloudFront 배포 OAI 사용자들이 보내는 요청만을 수락하도록 업데이트 (Answer)
- 클라이언트가 CloudFront로 리다이렉팅되도록 S3 액세스 포인트 사용

#오답노트
>Q. 한 기업에서 웹 애플리케이션을 AWS 클라우드로 이전해 EC2 오토 스케일링 그룹에 있는 EC2 인스턴스 세트를 사용하려고 합니다. 웹 애플리케이션은 여러 개의 컴포넌트로 구성되어 있기 때문에 특정 웹 애플리케이션 컴포넌트로 라우팅하려면 호스트 기반 라우팅 기능이 필요합니다. 이 웹 애플리케이션은 이용객이 아주 많기 때문에 고객들의 방화벽 화이트리스트에 등록될 수 있도록 정적 IP 주소를 사용해야 합니다. 또한 이 애플리케이션을 이용하는 고객들이 전 세계에 흩어져 있으므로 짧은 지연 시간으로 서비스를 제공해야 합니다. 다음 중 정적 IP 주소를 할당하고 전 세계에 짧은 지연 시간으로 서비스를 제공하는 데 도움이 될 만한 AWS 서비스는 무엇입니까?
- AWS Global Accelerator + 애플리케이션 로드 밸런서 (Answer)
- Amazon CloudFront
- 네트워크 로드 밸런서
- 애플리케이션 로드 밸런서