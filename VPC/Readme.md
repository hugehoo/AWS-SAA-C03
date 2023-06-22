
### VPC

### CIDR
- Class Inter-Domain Routing
    - a method for allocatoin IP addresses
    - IP 범위를 정의하는데 도움을 준다.
    - /32 -> IP 하나만 나타낸다.
    - /0 -> 모든 IP 를 나타냄.
- Base IP
    - Represents an IP contained in the range (XX.XX.XX.XX)
    - Example : 10.0.0.0, 192.168.0.0
- Subnet Mask
    - Defines how many bits can change in the IP
    - IP 에서 변경 가능한 비트의 개수를 정의
    - /8 -> 255.0.0.0
    - /16 -> 255.255.0.0
    - /24 -> 255.255.255.0
    - /32 -> 255.255.255.255
- 동일한 IP 에서 여러형태의 subnet mask가 존재
    - 192.168.0.0/32 -> allows for 1 IP (2^0)
      ![[Pasted image 20230622223615.png]]

https://www.ipaddressguide.com/cidr

공유 IP
사설 IP : 특정 값만 허용함
그러니까, EC2 인스턴스 하나 띄웠을 때 외부랑 통신이 가능했던 이유는, 기본 VPC 가 하나 생성되어 있고, Route Table 에서 0.0.0.0/0 트래픽을 gateway 로 매핑하여 통신을 시켜주는 것.

- VPC 마다 할당된 CIDR 은 5개
- 각 CIDR 최소 크기는 /28 => 2^4 = IP 주소는 최소 16개
	-  최대 크기는 /16 => 2^16 = IP 주소는 최대 65536개
- VPC 는 사설 리소스이기에 사설 아이피(IPv4)만 할당된다.


### Subnet
- VPC 내부에 있는 IPv4 주소의 부분 범위
- 이 범위 내에서 AWS 가 IP 주소 다섯개를 예약함 -> EC2에 할당할 수 없는 IP (5개)
- Example
    - CIDR block 10.0.0.0/24, then reserved IP addresses are:
    - `10.0.0.0` - Network Address 로 예약
    - `10.0.0.1` - reserved by AWS for the VPC router 로 예약
    - `10.0.0.2` - reserved by AWS for mapping to Amazon-provided DNS
    - `10.0.0.3` - reserved by AWS for future use
    - `10.0.0.255` - Network Broadcast Address. AWS Does not support Broadcast in a VPC, there for the address is reserved.
      Exam TIP
- if you need 29 IP addresses for EC2 instances:
    - can't choose a subnet of size /27 (32 IP addresses, 32 - 5 = 27 < 29)
    - need to choose a subnet of size /26 (64 IP addresses, 64 - 5 = 59 > 29)

Q. public subnet 이 여러개일 이유가 뭐가 있지?

### Internet Gateway (IGW)
- Allows Resources (e.g EC2) in a VPC connect to the Internet.
- 수평 확장 용이하고 HA, 중복성 높다.
- One VPC : One IGW
- Internet GW 는 인터넷 접속을 허용하지 않는다? what's this means?
>	Q. Internet Gateways on their own do not allow Internet access. what's this mean? I thought IGW should be connected to Internet so it can be accessed from all traffic.
A. An Internet Gateway (IGW) itself does not provide direct access to the internet. Instead, it serves as a connection point between your virtual private cloud (VPC) and the internet. The IGW allows traffic from your VPC to flow to the internet and vice versa. To enable access to the internet for instances within your VPC, you need to configure proper routing rules, and security settings like security groups, network ACLs, and assign public IPs or use NAT Gateway to route traffic from private subnets. So, while the IGW is a necessary component in providing internet access to your VPC, it alone does not guarantee full internet connectivity.


### Bastion
얘도 걍 EC2 인스턴스임, 이름이 배스쳔 일뿐. 근데 퍼블릭 서브넷에 있고, 배스쳔 호스트 그룹이라는 Security Group 내부에 존재함

- 배스천 EC2 는 Private Subnet 의 Ec2 로 접근할 수 있다.

- 요약하면 퍼블릭 서브넷의 배스쳔 호스트를 통해 프라이빗 서브넷의 인스턴스에 접근할 수 있다.

- Bastion Security Group 은 인터넷접속이 가능하게 설정되어야 한다.
- 프라이빗 서브넷의 보안그룹은 꼭 SSh 접근을 허용해주어야 배스쳔에서 접속이 가능해진다.

### Nat Instance vs Nat Gateway
- Nat instance 는 이제 곧 사라질 서비스라고 한다.
- 그래서 Nat instance 와 nat gateway 중 선택하는 정도의 내용만 인지하고 있자.

- NAT 인스턴스를 사용할 때는 소스/목적지 확인을 중단해야 한다. NAT 인스턴스 잔체가 소스/목적지가 중요한게 아니라면, 트래픽을 송수신할 수 있어햐 한다.

###  NAT Gateway
- high bandwidth, 가용성이 높고 관리할 필요도 없다.
- 특정 AZ에 생성되고, elastic iP 를 할당받음
- Can't be used by EC2 instance in the same Subnet (only from other subnets)
- Private subnet -> NAT GW -> IGW
- 대역폭은 자동스케일링 된다.(초당 5Gb)

![[Pasted image 20230623205020.png]]
- Public Subnet 은 Router 와 연결된 상태이므로 인터넷 연결이 가능함
- 즉 NAT GW 도 인터넷 연결이 된 상태
- Private EC2 는 인터넷이 연결된 NAT GW와 통신하여 인터넷 커넥션을 이룰 수 있다.


![[Pasted image 20230623204853.png]]

- 하나의 가용영역이 다운되더라도, NAT GW 는 각 AZ에 있기 때문에 괜춘

- NAT GW 는 HA 를 가지는 반면에, NAT Instance 는 인스턴스간 장애 조치 스크립트를 만들어 전체적 관리를 해야한다.
- NAT GW 대역폭 : 45GB
- Security Groups / Bastion host 를 사용하지 않는다. (nat instance 는 사용함)


### 보안그룹 및 네트워크 ACL (NACL)
- NACL 은 무상태(Stateless), 보안 그룹은 상태 유지(stateful)
- NACL 은 서브넷으로 들어오는 트래픽을 제어하는 방화벽 역할을 한다
- 1 NACL / subnet, 새로운 서브넷이 생성되면 Default NACL 이 할당된다.
- NACL Rules : 1~32766 의 숫자로 규칙이 부여되는데, 숫자가 낮을 수록 규칙의 우선권을 갖는다. 즉 1번 룰에서 특정 포트로 트래픽을 허용하는데, 2번 룰에서 해당 포트의 트래픽을 거부한다 하더라도, 1번이 더 우선권을 갖기에 해당 포트의 트래픽은 허용된다.

- 서브넷 수준에서 특정 IP 주소를 차단하는데 아주 적합하다.

### Default NACL (시험에서 중요함)
- 연결된 서브넷을 가지고, 인바운드와 아웃바운드의 모든 요청을 허용하는 특수성 가진다.
- 만약 Default NACL 이 서브넷과 연결되어 있다면, 모든 트래픽이 드나들도록 허용된다는 의미

### Ephemeral Ports
- 연결 수명을 위해서만 할당되는 무작위 포트

![[Pasted image 20230623212329.png]]
- 이 그림은 잘 이해가 되지 않는게, 아까 서브넷 하나당 하나의 NACL 을 가진다고 하지 않았나?
- 아 일대일 매핑이 아니라,,,, 일단 1:N 이더라도 하나의 NACL 은 할당 받는 거구나! 굿!
- Security Group 은 인스턴스 수준, NACL 은 subnet 수준에서 작동한다.
- NACL 은 허용/거부를 모두 지원하기 때문에 특정 IP 주소를 거부할 수 있다. (Security Group 은 허용만 가능)
-


<hr>

AWS 에 국부 유출을 줄이겠다는 마음으로 제작된 레포
- SAA-C03 한번에 합격 기원