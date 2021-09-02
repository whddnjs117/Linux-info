# Local yum repository

CentOS ,RHEL에 ISO 파일로 로컬 yum 저장소(Local yum repository) 만들기

## 과정
* 외부 인터넷이 차단된 서버(off-line server) 에 설치 ISO Image 파일을 업로드해서 yum 저장소를 만들고 패키지 설치 및 업데이트하는 방법을 설명합니다.
* Local repository 생성
* Local repository 공유

---
### Local repository 생성

1. 설치 dvd 다운로드 합니다. 예로 CentOS(7버전)는 [http://isoredirect.centos.org/centos/7/isos/x86_64/](http://isoredirect.centos.org/centos/7/isos/x86_64/) 에서 다운로드가 가능합니다.
2. mount 할 폴더를 생성합니다.
```shell
mkdir -p /var/www/repos/centos
```
3. 다운받은 iso 파일을 서버에 올린 후에 mount 합니다. (sftp 활용)
```Linux
mount -o loop CentOS-6.10x86_64bin-DVD1.iso /var/www/repos/centos
```
4. repository 정보를 설정하기 위해 /etc/yum.repos.d/local.repo를 에디터로 열고 다음 repository 설정을 넣습니다.
```shell
[local-repo]
name=Local Repository
baseurl=file:///var/www/repos/centos
enabled=1
gpgcheck=0
```
5. 다음 명령어를 실행 한 후 local-repo가 보이면 정상 설정된 것입니다.
```shell
yum repolist | grep local-repo

repo id                              repo name                                                               status

epel                                 EPEL repository                                                         12,581
local -repo                          Local Repository                                                      6,713
nginx                                Nginx Repository x86_64 - Archive  
```

---
### Local repository 공유
위 방법은 서버마다 설정해야 하므로 관리하는 서버가 많을 경우 반복 작업을 해야합니다.

이럴 때 Web Server에 내부 네트워크용 저장소를 설정하면 다른 서버에서는 저장소 주소만 등록하면 되므로 편리하게 사용할 수 있습니다.

#### 서버 설정

1. 저장소롤 사용할 서버(Ex: 192.168.10.10)에 ssh로 연결합니다.
2. CentOS 나 RHEL의 설치 dvd(CentOS-6.10x86_64bin-DVD1.is)를 저장소 서버에 업로드합니다.
3. mount 할 폴더를 만드는데 Web Server 로 제공할 것이므로 /var/www 하단에 생섷합니다.
```shell
mkdir -p /var/www/repos/centos/iso
```
4. 업로드할 설치 iso를 mount 합니다.
```shell
mount -o loop /var/iso-file/CentOS-6.10-x86_64-bin-DVD1.iso /var/www/repos/centos/iso/
```
5. 부팅시 자동으로 마운트 되도록 /etc/fstab 에 다음 내용을 추가합니다.
```shell
/var/iso-file/CentOS-6.10-x86_64-bin-DVD1.iso /var/www/repos/centos/iso/ iso9660 loop 0
```
6. yum repository 를 생성할 수 있는 패키지인 createrepo를 설치합니다.
```shell
yum install createrepo -y
```
#### client 설정
1. repository를 사용할 서버에 연결합니다.

2. repository 정보를 설정하기 위해 <b>/etc/yum.repos.d/centos-internal.repo</b> 를 에디터로 열고 다음 내용을 추가합니다.
```shell
[internal-repo]
name=Internal Repository
baseurl=http://192.168.10.10/repos/centos/iso
enabled=1
gpgcheck=0
```

3. 다음명령을 실행해서 저장소가 보이는지 확인합니다.
```shell
yum repolist | grep internal-repo
```