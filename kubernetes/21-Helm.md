# 21. Helm

## 21.1. 헬름 소개

쿠버네티스를 사용하다 보면 결국 수많은 템플릿을 관리해야 함. helm은 이런 템플릿 파일들의 집합(차트라고 함)을 관리하는 쿠버네티스 패키지 매니저 도구임.
쿠버네티스의 하위 프로젝트로 시작되었다가 2018년 6월 CNCF 재단의 정식 프로젝트로 승격됨.

헬름은 차트와 차트 압축파일(gz)을 만들 수 있음. 그리고 차트가 저장된 차트 저장소(chart repository)와 연결해 쿠버네티스 클러스터에 차트를 설치하거나 삭제할 수 있음. 헬름 차트들의 배포 주기를 관리할 수도 있음.

헬름을 이용하면 잘 정리된 차트들로 필요한 애플리케이션들을 빠르게 설치할 수 있음. [Helm Charts](https://github.com/helm/charts)라는 깃허브 저장소에는 incubator 와 stable 디렉터리 아래 수많은 차트가 준비되어 있음. MySQL, Redis, Jenkins, Hadoop, ElasticSearch 등 많은 애플리케이션을 쉽게 설치해서 사용할 수 있음.

헬름을 본격적으로 알아보기 전 다음 세 가지 주요 개념을 기억하기 바람.

* chart: 쿠버네티스에서 실행할 애플리케이션을 만드는 데 필요한 정보 묶음임.
* config: 패키지한 chart에 넣어서 배포 가능한 오브젝트를 만들 때 사용할 수 있는 설정이 있음.
* release: 특정 config를 이용해 실행 중인 chart의 인스턴스임.

## 21.2. 헬름 클라이언트와 틸러 서버

헬름은 커맨드라인 인터페이스인 helm client 와 쿠버네티스 클러스터 안에서 helm client의 명령을 받아 쿠버네티스 API와 통신하는 Tiller Server 로 구성되어 있음. 헬름 클라이언트는 Go프로그래밍 언어로 개발했으며 틸러 서버와는 gRPC를 이용해 통신함.

helm client 를 사용하면 로컬 서버에서 chart를 만들고 chart repository 들을 관리할 수 있음. tiller server와 통신하면 쿠버네티스 클러스터에 설치하려는 chart를 보낼 수 있음. 또한 현재 클러스터에 실행 중인 애플리케이션(helm chart를 이용해 실행) 릴리즈 정보를 요청하거나 실행 중인 릴리즈를 업그레이드 하거나 삭제하는 요청 등을 tiller server로 보냄.

tiller server는 주로 helm client 요청을 처리하려고 대기하다가 요청이 오면 클러스터에 실행할 릴리즈를 만들려고 chart와 config를 조합하거나, 클러스터에 char 를 설치하거나, release 를 관리함. 클러스터에서 실제로 chart를 업그레이드하거나 삭제할 수도 있음.

## 21.3. helm 설치하고 사용하기

 다음 명령을 실행해 helm client 를 설치할 수 있음.
 
 ```bash
#macOS
$ brew install kubernetes-helm
 ```

설치가 잘 안된다면 헬름의 (깃허브 저장소 Release 페이지)[https://github.com/helm/releases]에서 helm client 파일을 직접 다운로드해 사용함.

helm client 를 설치했으면 쿠버네티스 클러스터에 tiller server 를 설치해야 함. 간단하게 `helm init` 명령을 실행함. 이때 쿠버네티스에 접근하는 kubeconfig 설정이 로컬 서버에 미리 되었어야 함.