# Hub-Spoke 네트워크 구성
Microsoft Azure에 Hub-Spoke Network 구성을 설정하여 네트워크간 통신 확인

## Spoke 가상 네트워크와 가상 머신 만들기
1. [Microsoft Azure Management Console](https://portal.azure.com)에 로그인한다.

1. 다음 정보를 참고하여 **2개의 Spoke 가상 네트워크를 생성**한다.
    - `리소스 그룹`: vpn-lab-rg-xx (lab01에서 생성한 리소스 그룹)
    - `가상 네트워크 이름`: `vpn-lab-vnet-spoke1-xx`
    - `지역`: (Asia Pacific) Korea Central
    - `IP 주소`:
        - `IP CIDR`: `10.0.0.0/16`을 `10.1xx.0.0/17`으로 변경

    - `리소스 그룹`: vpn-lab-rg-xx (lab01에서서 생성한 리소스 그룹)
    - `가상 네트워크 이름`: `vpn-lab-vnet-spoke2-xx`
    - `지역`: (Asia Pacific) Korea Central
    - `IP 주소`:
        - `IP CIDR`: `10.0.0.0/16`을 `10.1xx.128.0/17`으로 변경

1. 다음 정보를 참고하여 **2개의 Spoke 가상 네트워크에 가상 머신를 각각 생성한다.**
    - `리소스 그룹`: vpn-lab-rg-xx (앞서 생성한 리소스 그룹)
    - `가상 머신 이름`: `vpn-lab-VM-spoke1-xx`
    - `지역`: (Asia Pacific) Korea Central
    - `보안 유형`: `표준`
    - `이미지`: Ubuntu Server 22.04 LTS - x64 Gen2
    - `크기`: Standard_B2ats_v2
    - `사용자 이름`: `student`
    - `SSH 공개 키 원본`: Azure에 저장된 기존 키 사용
    - `저장된 키`: vpn-lab-key-xx
    - `OS 디스크 유형`: 표준 SSD(로컬 중복 스토리지)
    - `가상 네트워크`: vpn-lab-vnet-spoke1-xx
    - `공용 IP`: 없음
    - `NIC 네트워크 보안 그룹`: 고급
    - `네트워크 보안 그룹 구성`: training-VM01-xx-nsg
    - `자동 종료 사용`: 체크박스 체크 해제

    - `리소스 그룹`: vpn-lab-rg-xx (앞서 생성한 리소스 그룹)
    - `가상 머신 이름`: `vpn-lab-VM-spoke2-xx`
    - `지역`: (Asia Pacific) Korea Central
    - `보안 유형`: `표준`
    - `이미지`: Ubuntu Server 22.04 LTS - x64 Gen2
    - `크기`: Standard_B2ats_v2
    - `사용자 이름`: `student`
    - `SSH 공개 키 원본`: Azure에 저장된 기존 키 사용
    - `저장된 키`: vpn-lab-key-xx
    - `OS 디스크 유형`: 표준 SSD(로컬 중복 스토리지)
    - `가상 네트워크`: vpn-lab-vnet-spoke2-xx
    - `공용 IP`: 없음
    - `NIC 네트워크 보안 그룹`: 고급
    - `네트워크 보안 그룹 구성`: training-VM01-xx-nsg
    - `자동 종료 사용`: 체크박스 체크 해제

## Hub-Spoke 네트워크 토폴로지 구성
1. 상단 검색창에 lab01에서 생성한 가상 네트워크 이름인 `vpn-lab-vnet-hub-xx`를 입력하여 `vpn-lab-vnet-hub-xx` 리소스를 찾아 클릭한다.

1. 좌측 네비게이션 메뉴에서 **설정** > **피어링**을 선택한 후 상단에 **+ 추가**를 클릭한다.

1. 다음 정보를 활용하여 Spoke network와 peering을 연결한다.
    - `원격 가상 네트워크 요약`: 
        - `피어링 링크 이름`: `vpn-lab-vnet-hub-to-vpn-lab-vnet-spoke1`
        - `가상 네트워크`: vpn-lab-vnet-spoke1-xx
        - `피어링된 가상 네트워크에서 'vpn-lab-vnet-hub-xx'이(가) 전달한 트래픽 수신 허용`: 체크
        - `피어링된 가상 네트워크이(가) 'vpn-lab-vnet-hub-xx'의 원격 게이트웨이 또는 경로 서버를 사용하도록 설정`: 체크
    - `로컬 가상 네트워크 요약`: 
        - `피어링 링크 이름`: `vpn-lab-vnet-spoke1-to-vpn-lab-vnet-hub`
        - `vpn-lab-vnet-hub-xx'에서 'vpn-lab-vnet-spoke1-xx'이(가) 전달한 트래픽 수신 허용`: 체크
        - `'vpn-lab-vnet-hub-xx' 게이트웨이 또는 경로 서버가 트래픽을 'vpn-lab-vnet-spoke1-xx'에게 전달하도록 허용`: 체크
    
    - `원격 가상 네트워크 요약`: 
        - `피어링 링크 이름`: `vpn-lab-vnet-hub-to-vpn-lab-vnet-spoke2`
        - `가상 네트워크`: vpn-lab-vnet-spoke2-xx
        - `피어링된 가상 네트워크에서 'vpn-lab-vnet-hub-xx'이(가) 전달한 트래픽 수신 허용`: 체크
        - `피어링된 가상 네트워크이(가) 'vpn-lab-vnet-hub-xx'의 원격 게이트웨이 또는 경로 서버를 사용하도록 설정`: 체크
    - `로컬 가상 네트워크 요약`: 
        - `피어링 링크 이름`: `vpn-lab-vnet-spoke2-to-vpn-lab-vnet-hub`
        - `vpn-lab-vnet-hub-xx'에서 'vpn-lab-vnet-spoke2-xx'이(가) 전달한 트래픽 수신 허용`: 체크
        - `'vpn-lab-vnet-hub-xx' 게이트웨이 또는 경로 서버가 트래픽을 'vpn-lab-vnet-spoke2-xx'에게 전달하도록 허용`: 체크

1. 상단 검색창에 이전 스텝에서 생성한 가상 머신 이름인 `vpn-lab-VM-spoke1-xx`를 입력하여 `vpn-lab-VM-spoke1-xx` 리소스를 찾아 클릭한다.

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
    nslookup vpn-lab-VM01.xxx.syx.internal.cloudapp.net # lab01에서 nslookup vpn-lab-VM01 명령어 실행 결과에 대한 도메인 이름
    nslookup test.vpn-lab.local 168.63.129.16

    ping 10.xx.0.4

    nslookup vpn-lab-VM-spoke2-xx

    ping 10.1xx.128.4
    ```

1. 상단 검색창에 이전 스텝에서 생성한 가상 머신 이름인 `vpn-lab-VM-spoke2`를 입력하여 `vpn-lab-VM-spoke2` 리소스를 찾아 클릭한 후 Bastion을 이용하여 접속한다.

1. 명령어 창에 다음과 같은 명령어를 입력하여 결과를 확인한다.
    ```bash
    nslookup vpn-lab-VM01-xx
    nslookup vpn-lab-VM01.xxx.syx.internal.cloudapp.net # lab01에서 nslookup vpn-lab-VM01 명령어 실행 결과에 대한 도메인 이름
    nslookup test.vpn-lab.local 168.63.129.16

    ping 10.xx.0.4

    nslookup vpn-lab-VM-spoke2-xx

    ping 10.1xx.0.4
    ```
