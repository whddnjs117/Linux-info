# tomcat 

## 개요
tomcat 설치 과정부터 활용방법을 작성하는 페이지


## 과정
* tomcat 설치 and 설정
* tomcat 인스턴스 분리


### tomcat 설치 and 설정
* 설치할 tomcat을 linux os 버전을 가져온다.
    * 필자는 tomcat 8.5.47을 사용하였습니다.
    *  https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.47/bin/
* 설치 파일을 이동시켜준다.
    * sftp로 접속하여 파일을 전송
        * 파일을 전송할 디렉토리를 맞추고 시작합니다.
          ```shell
          put apache-tomcat-8.5.47.tar.gz
          ```
* tar파일 해제 하기  
    ```shell
    tar -zxvf apache-tomcat-8.5.47.tar.gz
    ```
* tomcat path 잡기
    * 필자는 /etc/profile 로 시스템 path를 잡아주었습니다.
    ```shell
    vi /etc/profile # vi 편집기로 실행

    # vi 편집기 시작
    CATALINA_HOME=/app/tomcat8/apache-tomcat-8.5.47 # 실제 tomcat 설치된 디렉토리 위치

    PATH=$PAHT:$CATALINA_HOME/bin
    export CATALINA_HOME
    # vi 편집기 종료 wq로 저장 q!는 저장안함
    ```
    
### tomcat 인스턴스 분리
* tomcat으로 여러개의 프로젝트를 실행시키고 싶을때 인스턴스를 분리해서 해야한다.
* 예를 들어 ins1, ins2, ins3 라는 프로젝트를 3개를 별로도 나눌경우
    * 인스턴스를 구성할 폴더를 생성
        ```shell
        mkdir ins1 #1번 인스턴스 폴더 생성
        mkdir ins2 #2번 인스턴스 폴더 생성
        mkdir ins3 #3번 인스턴스 폴더 생성
        ```
    * 각 인스턴스폴더별로 설정(톰캣 구동을 위한)
        * 현재 디렉토리 위치는 tomcat 설치 위치
        ```shell
        cp -rf conf webapps logs ./../ins1 # ins1 폴더안에 톰캣 설정을 위한 파일을 복사
        cp -rf conf webapps logs ./../ins2 # ins2 폴더안에 톰캣 설정을 위한 파일을 복사
        cp -rf conf webapps logs ./../ins3 # ins3 폴더안에 톰캣 설정을 위한 파일을 복사
        ```
        * server.xml 이나 web.xml 수정 할 사항 있을시 수정
        * 프로젝트 배포위치나 별도 서버 등등..
    * 각 인스턴스폴도별로 설정이 끝난 후 각각의 톰캣구동 실행 설정
        * 필자는 path자체를 profile에서 bin으로 잡았기 때문에 /bin안에 쉘 스크립트문을 생성함
        * ins1 쉘 스크립트
        ```shell
        vi startins1.sh
        # vi 편집기 열림
        
        #! /bin/sh 
        export CATAKINA_BASE=/tomcat8/ins1    # 해당 폴더의 인스턴스를 실행 시켜주는의미

        cd $CATALINA_HOME/bin
        ./startup.sh
        # vi 편집기 닫힘
        ```
        * ins2 ,3 도 1이랑 동일 
* path 가 잡혀있어 startins1.sh을 실행시킬시 톰캣 구동이 된다.
* 별도로 로그를 확인하고 싶으면 각 인스턴스별 파일별로 logs/catalina.out 을 확인한다.
    ```shell
    tail -f catalina.out
    ```
* 이상