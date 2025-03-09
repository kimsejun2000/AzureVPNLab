# AWS와 VPN 연결 구성
AWS에 VPC를 구성한 후 Azure와 VPN 연결을 하여 가상 머신 간 통신을 확인하고 DNS 확장에 대해 알아본다.

## Azure 가상 네트워크에 VPN Gateway 생성
1. [Microsoft Azure Management Console](https://portal.azure.com)에 로그인한다.

1. 상단 검색창에 `vpn-lab-vnet-hub-xx`를 입력하여 `vpn-lab-vnet-hub-xx`리소스를 찾아 클릭한다.

1. 왼쪽 네비게이션 메뉴에서 **설정** > **서브넷**을 클릭한다.

1. 상단 **+ 서브넷**을 클릭하여 다음 정보를 참고하여 GatewaySubnet을 생성한다.
    - `서브넷 용도`: Virtual Network Gateway
    - `시작 주소`: 10.00.255.0
    - `크기`: /27(32개 주소)

1. 서브넷 추가가 완료되면, 상단 검색창에 **가상 네트워크 게이트웨이**를 입력하여 Marketplace에서 **가상 네트워크 게이트웨이** 를 찾아 클릭한다.

1. 다음 정보를 참고하여 가상 네트워크 게이트웨이를 생성한다.
    - `이름`: `vpn-lab-vgw-xx`
    - `지역`: Korea Central
    - `게이트웨이 유형`: VPN
    - `SKU`: VpnGw1
    - `가상 네트워크`: vpn-lab-vnet-hub-xx
    - `공용 IP 주소 이름`: `vpn-lab-vgw-ip-xx`
    - `active-active 모드를 사용하도록 설정`: 사용 안 함

1. 가상 네트워크 게이트웨이의 생성일 가다리지 않고 다음 랩으로 이동한다.

## AWS에 VPC 만들기
1. [Microsoft MyApplications](http://myapplications.microsoft.com/)에 접속하여 로그인한다.

1. Amazon Web Services (AWS)를 클릭하여 AWS Console에 접속한다.

1. 우측 상단의 지역이 **아시아 태평양 (서울)**인지 확인한다.

1. 상단 검색창에 **VPC**를 입력하고 서비스에서 **VPC**를 찾아 클릭한다.

1. 왼쪽 네비게이션 메뉴에서 **Virtual Private Cloud** > **VPC**를 클릭한다.

1. 우측 상단의 **VPC 생성**을 클릭하여 다음 정보를 참고하여 VPC를 생성한다.
    - `이름 태그`: `vpn-lab-vpc-xx`
    - `IPv4 CIDR`: `10.2xx.0.0/16`, 2xx의 **xx**은 자신의 계정의 마지막 숫자를 입력

1. 왼쪽 네비게이션 메뉴에서 **Virtual Private Cloud** > **서브넷**를 클릭한다.

1. 우측 상단의 **서브넷 생성**을 클릭하여 다음 정보를 참고하여 서브넷을 생성한다.
    - `VPC ID`: vpc-xxxxxxxxxxxx (vpn-lab-vpc-xx)
    - `1/1개 서브넷`:
        - `서브넷 이름`: `vpn-lab-subnet-xx`
        - `가용 영역`: 아시아 태평영 (서울) / ap-northeast-2a
        - `IPv4 서브넷 CIDR 블록`: `10.2xx.0.0/24`

## AWS에 Linux instance 만들기
1. 상단 검색창에 **ec2**를 입력하고 서비스에서 **EC2**를 찾아 클릭한다.

1. 왼쪽 네비게이션 메뉴에서 **인스턴스** > **인스턴스**를 클릭한다.

1. 우측 상단의 **인스턴스 시작**을 클릭하여 다음 정보를 참고하여 EC2 Instance를 생성한다.
    - `이름`: `vpn-lab-amzl-xx`
    - `키 페어 이름 - 필수`: 새 키 페어 생성
        - `키 페어 이름`: `vpn-lab-key-xx`
        - `키 페어 유형`: ED25519
    - `네트워크 설정`: 우측 편집 클릭
        - `VPC - 필수`: vpc-xxxxxxxxxxxx (vpn-lab-vpc-xx)
        - `보안 그룹 이름 - 필수`: `vpn-lab-sg-xx`

## AWS VPC에 VGW 만들기
1. 상단 검색창에 **VPC**를 입력하고 서비스에서 **VPC**를 찾아 클릭한다.

1. 왼쪽 네비게이션 메뉴에서 **가상 사설 네트워크(VPN)** > **가상 프라이빗 게이트웨이**를 클릭한다.

1. 우측 상단의 **가상 프라이빗 게이트웨이 생성**을 클릭하여 다음 정보를 참고하여 가상 프라이빗 게이트웨이를 생성한다.
    - `이름 태그 - 선택 사항`: `vpn-lab-vgw-xx`

1. 우측 상단의 새로고침 아이콘을 클릭하여 `vpn-lab-vgw-xx`의 `상태`가 `분리됨`이 되면 `vpn-lab-vgw-xx`를 선택한 후 우측 상단의 **작업** > **VPC에 연결**을 클릭하여 다음 정보를 참고하여 VPC에 연결한다.
    - `사용 가능한 VPC`: vpc-xxxxxxxxxxxx / vpn-lab-vpc-xx

1. 우측 상단의 새로고침 아이콘을 클릭하여 `vpn-lab-vgw-xx`의 `상태`가 `연결됨`이 되었는지 확인한다.

1. 왼쪽 네비게이션 메뉴에서 **Virtual Private Cloud** > **라우팅 테이블**를 클릭한다.

1. **VPC** 탭을 확장하여 앞서 생성한 `vpc-xxxxxxxxxxxx | vpn-lab-vpc-xx`을 찾아 클릭한다.

1. 하단의 정보창에서 **라우팅**탭을 이동한 후 **라우팅 편집**을 클릭한다.

1. **라우팅 추가**버튼을 클릭한 후 다음을 참고하여 정보를 입력하고 **변경 사항 저장**을 클릭한다.
    - `대상(IP CIDR)`: 10.xx.0.0/16
    - `대상`: 가상 프라이빗 게이트웨이 > vgw-xxxxxxxxxxxx (vpn-lab-vgw-xx)

## Azure VPN Gateway 정보를 이용하여 AWS site-to-site VPN Connect 만들기
1. 상단 검색창에 **VPC**를 입력하고 서비스에서 **VPC**를 찾아 클릭한다.

1. 왼쪽 네비게이션 메뉴에서 **가상 사설 네트워크(VPN)** > **고객 게이트웨이**를 클릭한다.

1. 우측 상단의 **고객 게이트웨이 생성**을 클릭하여 다음 정보를 참고하여 고객 게이트웨이를 생성한다.
    - `이름 태그 - 선택 사항`: `vpn-lab-azure-vgw-xx`
    - `IP 주소`: Azure portal에서 `vpn-lab-vgw-xx` 리소스를 확인하여 **Overview**에서 우측의 **기본 정보** > **공용 IP 주소**를 확인하여 입력한다.

1. 왼쪽 네비게이션 메뉴에서 **가상 사설 네트워크(VPN)** > **Site-to-Site VPN 연결**을 클릭한다.

1. 우측 상단의 **VPN 연결 생성**을 클릭하여 다음 정보를 참고하여 VPN 연결을 생성한다.
    - `이름 태그 - 선택 사항`: `vpn-lab-vpn-xx`
    - `가상 프라이빗 게이트웨이`: vgw-xxxxxxxxxxxx / vpn-lab-vgw-xx
    - `고객 게이트웨이 ID`: cgw-xxxxxxxxxxxx / vpn-lab-azure-vgw-xx
    - `라우팅 옵션`: 정적
    - `정적 IP 접두사`: 10.xx.0.0/16

1. `vpn-lab-vpn-xx`의 `상태`가 `사용 가능`으로 변경되면 우측 상단의 **구성 다운로드**를 클릭한 후 다음 정보를 입력하여 **다운로드**를 클릭한다.
    - `공급업체`: Cisco Systems, Inc.

1. 파일 다운로드가 완료되면 해당 파일을 열어둔다. 다음 랩에서 사용해야 한다.

## AWS site-to-site VPN Connect 정보를 이용하여 Azure VPN Connection 만들기
1. [Microsoft Azure Management Console](https://portal.azure.com)의 상단 검색창에 **로컬 네트워크 게이트웨이**를 입력하여 Marketplace에서 **로컬 네트워크 게이트웨이** 를 찾아 클릭한다.

1. 다음 정보를 참고하여 로컬 네트워크 게이트웨이를 생성한다.
    - `리소스 그룹`: vpn-lab-rg-xx (앞서 생성한 리소스 그룹)
    - `지역`: Korea Central
    - `이름`: `vpn-lab-aws-vpn-xx`
    - `IP 주소`: AWS Console에서 `vpn-lab-azure-vpn-xx`를 클릭한 후 하단 정보창의 **터널 세부 정보**탭으로 이동하여 **Tunnel 1**의 **외부 IP 주소**를 입력한다.
    - `주소 공간`: 10.2xx.0.0/16
    
1. `vpn-lab-aws-vpn-xx` 생성이 완료되면, 상단 검색창에 `vpn-lab-vgw-xx`를 입력하여 `vpn-lab-vgw-xx`리소스를 찾아 클릭한다.

1. 왼쪽 네비게이션 메뉴에서 **설정** > **연결**을 클릭한다.

1. 상단 **+ 추가**을 클릭하여 다음 정보를 참고하여 VPN 연결을 생성한다.
    - `리소스 그룹`: vpn-lab-rg-xx (앞서 생성한 리소스 그룹)
    - `연결 형식`: 사이트 간(IPsec)
    - `이름`: `vpn-lab-aws-vpn-conn-xx`
    - `가상 네트워크 게이트웨이`: vpn-lab-vgw-xx
    - `로컬 네트워크 게이트웨이`: vpn-lab-aws-vpn-xx
    - `공유 키(PSK)`: 앞서 다운로드한 AWS 구성 파일에서 **로컬 네트워크 게이트웨이에 입력한 외부 IP 주소**를 검색한 후 **pre-shared-key** 값을 찾아 입력
    - `IKE 프로토콜`: IKEv2

1. AWS Console의 VPN 연결과 Azure Portal의 VPN 연결 모두 연결된 것을 확인한다.

## VM간 통신 확인 및 DNS resolve 확인
1. 상단 검색창에 이전 스텝에서 생성한 가상 머신 이름인 `vpn-lab-VM01-xx`를 입력하여 `vpn-lab-VM01-xx` 리소스를 찾아 클릭한다.

1. 왼쪽 네비게이션 메뉴에서 **연결** > **베스천**을 클릭한 후 다음과 같은 정보로 SSH 연결한다.
    - `인증 유형`: 로컬 파일의 SSH 프라이빗 키
    - `사용자 이름`: `student`
    - `로컬 파일`: 다운로드 받은 .pem 파일
    
    > [메모]
    >
    > `xxx.bastion.azure.com에서 다음 권한을 요청합니다.` 창이 뜨면 **허용**클릭

1. 명령어 창에 다음과 같은 명령어를 입력하여 결과를 확인한다.
    ```bash
    ssh -i <AWS vpn-lab-key-xx> ec2-user@<AWS vpn-lab-amzl-xx의 사설 IP>
    ```
