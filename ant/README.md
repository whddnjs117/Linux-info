# ant 

## 개요
ant bulid를 사용 방법 정리 또는 사용 과정을 작성하는 페이지

## 과정
* ant 설치 과정
* linux에서 ant 환경변수 설정

### ant 설치 과정
1. ant 설치 파일 다운로드[https://archive.apache.org/dist/ant/binaries/](https://archive.apache.org/dist/ant/binaries/) 에서 알맞은 ant 다운로드
2. sftp 커맨드를 활용하여 다운로드한 파일을 linux 서버로 옮겨준다.
```shell
sftp root@ip #접속
put 다운로드한 파일명 #다운로드 파일 보내기(경로는 별도로 맞추기)
```
3. linux서버에서 가져온 파일을 압축해제
```shell
tar -zxvf 다운로드파일명
```

### ant 환경변수 설정
```shell
vi /etc/profile

#ant path
ANT_HOME = /app/ant/apache-ant1.10.11 #본인은 ant설치위치를 /app/ant/하위에 다운로드
export ANT_HOME
```

### ant bulid 설정
* 현재 ant 설치가 완료 되었으며 ant 실행
```shell
ant 빌드명 # build.xml을 설정해야합니다.
```
#### build.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="HUNEL_EHR_WEB_STAND" default="build_project" basedir=".">

  <description>AJP_EHRS_WEB build</description>

  <property name="hunelapp.dir" value="."/>
  <property name="src.dir" value="${hunelapp.dir}/src"/>
  <property name="classes.dir" value="${hunelapp.dir}/WebRoot/WEB-INF/classes"/>
  <property name="lib.app.dir" value="${hunelapp.dir}/WebRoot/WEB-INF/lib"/>
  <property name="lib.was.dir" value="/app/tomcat8/apache-tomcat-8.5.70/lib"/>
  <property name="build.profile" value=""/>

  <path id="classpath">
    <fileset dir="${lib.app.dir}" includes="**/*.jar"/>
    <fileset dir="${lib.was.dir}" includes="**/*.jar"/>
  </path>

  <!-- Clean classes directory -->
  <target name="clean">
    <delete dir="${classes.dir}" />
  </target>

  <!-- Compile -->
  <target name="compile">
    <mkdir dir="${classes.dir}" />
    <javac srcdir="${src.dir}" destdir="${classes.dir}" debug="true" encoding="utf-8" classpathref="classpath" includeantruntime="false" >
      <exclude name="webservice/interfaces/sample/client/java5/**"/>
    </javac>
  </target>
  <!-- Copy Properties -->
  <target name="copy_prop">
    <copy todir="${classes.dir}" overwrite="true" >
      <fileset dir="${src.dir}">
        <include name="**/*.properties" />
      </fileset>
    </copy>
    <copy todir="${classes.dir}" overwrite="true" >
      <fileset dir="${src.dir}">
        <include name="**/${build.profile}system.properties" />
      </fileset>
      <!--globmapper from="*.properties.sample" to="*.properties"/-->
    </copy>
    <copy todir="${classes.dir}" overwrite="true" >
      <fileset dir="${src.dir}">
        <include name="**/*.properties" />
        <include name="**/*.p12" />
        <include name="**/*.xml" />
      </fileset>
    </copy>
  </target>
  <target name="build_project" depends="clean, compile, copy_prop" />
</project>

```

Ant를 빌드하는 목적으로 사용을 하려고 설정해 놓은 모습이다.
현재 Ant는 빌드하는 목적으로 사용을 하려고 build.xml을 설정을 했다.

<b>위에서부터 하나씩 살펴보자.</b>

#### project 및 property
```xml
<project name="HUNEL_EHR_WEB_STAND" default="build_project" basedir=".">

  <description>AJP_EHRS_WEB build</description>

  <property name="hunelapp.dir" value="."/>
  <property name="src.dir" value="${hunelapp.dir}/src"/>
  <property name="classes.dir" value="${hunelapp.dir}/WebRoot/WEB-INF/classes"/>
  <property name="lib.app.dir" value="${hunelapp.dir}/WebRoot/WEB-INF/lib"/>
  <property name="lib.was.dir" value="/app/tomcat8/apache-tomcat-8.5.70/lib"/>
  <property name="build.profile" value=""/>
```
<br>
project name으로 프로젝트의 이름을 설정할 수 있고 default build를 설정할 수 있다. 
default build 라고 하는것은 Ant Script의 구성 요소 중 Job의 단위인 target이 있는데 Ant Script를 그냥 실행시켰을때
어떤 target을 수행할것인지 정해줄 수 있다.

아래는 property 정의를 하낟. pom.xml이나 다른 곳에서 property 정의를 하는것과 용도는 같다. 실수를 줄여주고
중복을 방지하기 위해 필요한 경로들에 대해 정의를 해놓는 곳이다.
<br>

#### classpath

```xml
  <path id="classpath">
    <fileset dir="${lib.app.dir}" includes="**/*.jar"/>
    <fileset dir="${lib.was.dir}" includes="**/*.jar"/>
  </path>
```
<br>
클래스패스를 정의하는 역할을 한다. 다른것과 달리 이건 path 태그를 사용한다. 빌드를 하는데 필요한 라이브러리를 가져오는데 그에 따른 폴더위치를
지정해주는 것이다. 여러개의 라이브러리가 존재한다면 모두 모아서 정의를 해줘야한다.
<br>

#### clean

```xml
  <!-- Clean classes directory -->
  <target name="clean">
    <delete dir="${classes.dir}" />
  </target>
```
<br>
clean은 전에 빌드한 결과물에 대해서 삭제를 해주는 역할을 한다. 기존 빌드를 지우지 않으면 빌드 결과물이 꼬이는 경우가 있어 삭제해야한다.
보통 clean을 하고 다시 빌드를 하는 경우를 권한다.
<br>

#### compile

```xml
  <!-- Compile -->
  <target name="compile">
    <mkdir dir="${classes.dir}" />
    <javac srcdir="${src.dir}" destdir="${classes.dir}" debug="true" encoding="utf-8" classpathref="classpath" includeantruntime="false" >
      <exclude name="webservice/interfaces/sample/client/java5/**"/>
    </javac>
  </target>
```
<br>
Java Source와 위에서 정의한 Classpath를 토대로 소스 빌드를 수행하는 역할을 한다. 
빌드의 결과물은 class 파일로 생성되며, 생성된 파일은 destdir에 정의한 곳으로 보낸다. 
javac에서는 위와 같이 다양한 속성들을 설정할 수 있다.           
<br>

|속성|설명|
|:---|:---|
|scrdir|compile할 java파일이 위치한 경로 <b>(필수)</b>|
|destdir|compile한 class파일들이 위치할 경로|
|debug|compile시 debug정보를 출력할지 여부 <b>off</b>|
|encoding|소스의 encoding 설정|
|classpathref|참조할 classpath 설정|
|includeantruntime|정의한 classpath 외에 ant의 runtime libaray를 추가여부|
|verbose|compile시 진행상황을 보여줄 것인지 여부 <b>no</b>|
|failonerror|build 실패 시 에러를 표시할 것인지 여부 <b>true</b>|

<br>

#### Copy
```xml
  <!-- Copy Properties -->
  <target name="copy_prop">
    <copy todir="${classes.dir}" overwrite="true" >
      <fileset dir="${src.dir}">
        <include name="**/*.properties" />
      </fileset>
    </copy>
    <copy todir="${classes.dir}" overwrite="true" >
      <fileset dir="${src.dir}">
        <include name="**/${build.profile}system.properties" />
      </fileset>
      <!--globmapper from="*.properties.sample" to="*.properties"/-->
    </copy>
    <copy todir="${classes.dir}" overwrite="true" >
      <fileset dir="${src.dir}">
        <include name="**/*.properties" />
        <include name="**/*.p12" />
        <include name="**/*.xml" />
      </fileset>
    </copy>
  </target>
``` 
<br>
copy는 복사를 해주는 역할을 한다.  
fileset으로 지정되어 있는 파일을 todir 디렉토리 위치로 파일을 복사해주는 역할을 한다.
예를 들어 하나의 프로젝트에 외부 환경설정 파일들이 있을 시 프로젝트 실행시키는데 필요한 역할이 있다면 그역시도 was에 존재해야 
정상적으로 프로젝트가 실행될 것이다. 그래서 외부 환경설정 파일들도 복사하여 올리기위해 존재한다.