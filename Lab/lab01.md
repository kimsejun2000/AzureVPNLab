# Azure Network 구성
Microsoft Azure에 가상 네트워크를 구성하고 관련된 기능들에 대해 간략히 살펴본다.

이 랩은 Windows 11에 최적화 되어있습니다. Windows 10, MacOS, Linux 머신으로 실습하는 분들은 알맞게 조절하세요.

## 가상 네트워크 배포
1. [Microsoft Azure Management Console](https://portal.azure.com)에 로그인한다.

1. 상단 검색창에 **가상 네트워크**를 입력하여 **가상 네트워크** 서비스를 찾아 클릭한다.

1. 좌측 상단에 **+ 만들기**를 클릭한다.

1. 다음 정보를 참고하여 가상 네트워크를 생성한다. 정의하지 않은 옵션은 전부 그대로 둔다.
    - `리소스 그룹`: **새로 만들기**를 클릭하여 `vpn-lab-rg-xx`을 입력하고 **확인**버튼 클릭. **xx**은 자신의 계정의 마지막 숫자를 입력
    - `가상 네트워크 이름`: `vpn-lab-vnet-hub-xx`
    - `지역`: (Asia Pacific) Korea Central
    - `IP 주소`:
        - `IP CIDR`: `10.0.0.0`을 `10.xx.0.0`으로 변경, **xx**은 자신의 계정의 마지막 숫자를 입력

1. 가상 네트워크의 생성이 완료될 때 까지 기다린다.

## Linux VM 배포
1. 상단 검색창에 **가상 머신**를 입력하여 **가상 머신** 서비스를 찾아 클릭한다.

1. 좌측 상단에 **+ 만들기**를 클릭한 후 **Azure 가상 머신**을 선택한다.

1. 다음 정보를 참고하여 가상 머신를 생성한다.
    - `리소스 그룹`: vpn-lab-rg-xx (앞서 생성한 리소스 그룹)
    - `가상 머신 이름`: `vpn-lab-VM01-xx`
    - `지역`: (Asia Pacific) Korea Central
    - `보안 유형`: `표준`
    - `이미지`: Ubuntu Server 22.04 LTS - x64 Gen2
    - `크기`: Standard_B2ats_v2
    - `사용자 이름`: `student`
    - `SSH 키 유형`: Ed25519 SSH 형식
    - `키 쌍 이름`: `vpn-lab-key-xx`
    - `인바운트 포트 선택`: HTTP (80), SSH (22)
    - `OS 디스크 유형`: 표준 SSD(로컬 중복 스토리지)
    - `가상 네트워크`: vpn-lab-vnet-hub-xx
    - `공용 IP`: 없음
    - `자동 종료 사용`: 체크박스 체크 해제

    > [메모]
    >
    > VM 생성 시 **새 키 쌍 생성** 창이 뜨면 `프라이빗 키 다운로드 및 리소스 만들기` 버튼을 클릭하여 키 쌍을 다운로드 하고 리소스 생성

1. 가상 머신의 생성이 완료될 때 까지 기다립니다.

## Azure-provided name resolution와 Azure Private DNS Zone 구성 및 확인
1. 상단 검색창에 **프라이빗 DNS 영역**를 입력하여 **프라이빗 DNS 영역** 서비스를 찾아 클릭한다.

1. 좌측 상단에 **+ 만들기**를 클릭한다.

1. 다음 정보를 참고하여 가상 네트워크를 생성한다.
    - `리소스 그룹`: vpn-lab-rg-xx (앞서 생성한 리소스 그룹)
    - `이름`: `vpn-lab.local`

1. 프라이빗 DNS 영역 배포가 완료되면 **리소스로 이동** 버튼을 클릭하여 배포된 프라이빗 DNS 영역 리소스로 이동한다.

1. 좌측 네비게이션 메뉴에서 **DNS 관리** > **레코드 집합**을 선택한 후 상단에 **+ 추가**를 클릭한다.

1. 오른쪽 블레이드창이 뜨면 다음을 참고하여 정보를 입력고 레코드를 **추가**한다.
    - `이름`: `test`
    - `IP 주소`: `192.168.0.100`

1. 좌측 네비게이션 메뉴에서 **DNS 관리** > **Virtual Network 링크**을 선택한 후 상단에 **+ 추가**를 클릭한다.

1. 다음을 참고하여 Virtual Network 링크를 만든다.
    - `링크 이름`: `vpn-lab-vnet-hub-connect-xx`
    - `Virtual Network`: vpn-lab-vnet-hub-xx

    > [메모]
    >
    > Virtual Network 링크가 다 만들어지지 않아도 다음 스텝을 진행할 수 있다.

1. 상단 검색창에 **Bastions**를 입력하여 **Bastions** 서비스를 찾아 클릭한다.

1. 좌측 상단에 **+ 만들기**를 클릭한다.

1. 다음 정보를 참고하여 Bastion service를 생성한다.
    - `리소스 그룹`: vpn-lab-rg-xx (앞서 생성한 리소스 그룹)
    - `이름`: `vpn-lab-bastion-xx`
    - `지역`: (Asia Pacific) Korea Central
    - `계층`: 기본
    - `가상 네트워크`: vpn-lab-vnet-hub-xx
    - `서브넷`: AzureBastionSubnet(10.xx.1.0/26) (**서브넷 구성 관리** 버튼을 클릭하여 다음 정보를 참고하여 `AzureBastionSubnet`을 생성한다.)
        - `서브넷 용도`: Azure Bastion
        - 서브넷 생성 후 상단의 **Bastion 만들기**를 클릭하여 이전 단계로 돌아온다.
    - `공용 IP 주소 이름`: `vpn-lab-bastion-ip-xx`

1. Bastion 배포가 완료될 때 까지 기다린다.

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
    nslookup vpn-lab-VM01-xx
    nslookup test.vpn-lab.local 168.63.129.16
    ```
