# 07. HDFS 명령어
---

HDFS 커맨드는 사용자 커맨드, 운영자 커맨드, 디버그 커맨드로 구분됨. 각 모드마다 다양한 커맨드가 있지만 여기서는 사용 및 운영에 필수적인 몇가지 커맨드만 확인하겠음. 전체 커맨드의 목록은 [HDFS Commands Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html)를 참고하십쇼.

## 07.1 사용자 커맨드
사용자 커맨드는 `hdfs`, `hadoop` 쉘을 이용할 수 있음. 일부 커맨드는 `hdfs` 쉘을 이용해야 함. 둘 다 이용할 수 있는 경우 각 쉘의 결과는 동일하며, 사용법은 다음과 같음.

```bash
# dfs 커맨드는 둘다 동일한 결과를 출력 
$ hdfs dfs -ls
$ hadoop fs -ls 

# fsck 커맨드도 동일한 결과 출력 
$ hdfs fsck /
$ hadoop fsck /

# fs.defaultFS 설정값 확인 
$ hdfs getconf -confKey fs.defaultFS
hdfs://127.0.0.1:8020

# 명령어를 인식하지 못함 
$ hadoop getconf
Error: Could not find or load main class getconf
```

|커맨드|비고|
|---|---|
|`classpath`|Hadoop jar 파일에 필요한 클래스패스 출력|
|`dfs`|파일 시스템 쉘 명령어|
|`fetchdt`|네임노드의 델리게이션 토큰 확인|
|`fsck`|파일 시스템 상태 체크|
|`getconf`|설정된 config 정보 확인|
|`groups`|사용자에 설정된 그룹 정보 확인|
|`IsSnapshottableDir`|스냅샷이 가능한 디렉토리 목록 확인|
|`jmxget`|JMX 인포메이션 확인|
|`oev`|Office Edits Viewer, Edits 파일의 상태 확인|
|`oiv`|Offline Image Viewer, 이미지 파일의 상태 확인(2.4 이상)|
|`oiv_legacy`|oiv 2.4 미만의 상태 확인|
|`snapshotDiff`|HDFS 스냅샷의 상태 확인|
|`version`|버전 확인|

이 명령어 중에서 필수적인 `dfs` 와 `fsck` 명령어에 대해 확인해보겠음.

### dfs 커맨드

`dfs` 커맨드는 파일시스템 쉘을 실행하는 명령어입니다. dfs는 `hdfs dfs`, `hadoop fs`, `hadoop dfs` 세가지 형태로 실행이 가능함. 전체 명령어는 (파일시스템쉘 가이드)[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html]를 확인하시면 됨. 명령어의 사용법은 다음과 같음.

```bash
$ hdfs dfs -ls
Found 17 items
drwxr-xr-x   - hadoop hadoop          0 2018-11-30 06:15 datas

$ hadoop fs -ls
Found 17 items
drwxr-xr-x   - hadoop hadoop          0 2018-11-30 06:15 datas
```

|명령어|비고|
|---|---|
|cat|파일의 내용을 확인|
|text|텍스트 형식으로 파일을 읽음, 압축 파일도 텍스트 형식으로 확인|
|appendToFile|지정한 파일에 내용을 추가(append)|
|checksum|파일의 체크섬 확인|
|chgrp|파일의 그룹 변경|
|chmod|파일의 모드 변경|
|count|지정한 경로의 디렉토리 개수, 파일개수, 파일 사이즈 확인|
|df|파일시스템의 용량 확인(복제본을 포함한 용량)|
|du|지정한 경로의 용량 확인(단일 파일의 용량)|
|dus|지정한 경로의 하위 폴더를 포함한 용량 확인|
|expunge|휴지통을 비움. 파일을 영구히 삭제.|
|getfacl|디렉토리, 파일의 ACL(Access Control List) 확인|
|setfacl|디렉토리, 파일의 ACL(Access Control List) 설정|
|getfattr|디렉토리, 파일의 추가적인 속성 확인|
|setfattr|디렉토리, 파일의 추가적인 속성 설정|
|getmerge|주어진 경로의 파일들을 로컬의 파일로 머지|
|find|주어진 표현에 맞는 파일을 조회|
|ls|디렉토리의 파일 조회|
|lsr|디렉토리 하위의 파일을 포함하여 조회|
|mkdir|디렉토리 생성|
|copyFromLocal|로컬의 파일을 HDFS로 복사|
|copyToLocal|HDFS의 파일을 로컬로 복사|
|cp|주어진 경로로 파일을 복사|
|moveFromLocal|로컬의 파일을 HDFS로 이동|
|moveToLocal|HDFS의 파일을 로컬로 이동|
|mv|주어진 경로의 파일을 이동, 파일이름 변경에도 이용|
|get|copyToLocal 명령과 비슷|
|put|copyFromLocal 명령과 비슷|
|createSnapshot|스냅샷 생성|
|deleteSnapshot|스냅샷 삭제|
|renameSnapshot|스냅샷 이름 변경|
|rm|파일 삭제|
|rmdir|디렉토리 삭제|
|rmr|주어진 경로의 하위 경로까지 삭제|
|setrep|레플리케이션 팩터 설정|
|stat|주어진 옵션에 따라 파일의 정보를 확인|
|tail|파일의 마지막 1kbyte를 출력|
|test|주어진 옵션에 따라 파일, 디렉토리의 존재 여부 확인|
|touchz|0byte 파일 생성|
|truncate|주어진 패턴에 따라 파일 삭제|
|usage|주어진 명령어 사용법 확인|
|help|명령어 사용법 확인|

* df는 복제개수를 포함한 전체의 용량을 표현하고, du 단일 파일 또는 경로의 용량임. 1mb 파일의 용량을 du로 확인하면 1mb으로 나오지만, df로 파일 시스템의 용량을 확인하면 복제개수가 3일때  3mb으로 표현됨.

이중에서 주요 명령어의 사용법을 확인해 보겠음.

### cat

지정한 파일을 기본 입력으로 읽어서 출력합니다.
```bash
$ hadoop fs -cat /user/file.txt
```

### text

지정한 파일을 텍스트 형식으로 읽음. gzip, snappy 등의 형식으로 압축된 파일을 자동으로 텍스트 형식으로 출력해 줌.
```bash
$ hadoop fs -text /user/file.txt
```

### ls

주어진 경로의 파일 목록을 조회함.
* 주요옵션 : `-h`, `-R`, `-u`

```bash
$ hadoop fs -ls /user/
# 사람이 읽을 수 있는 형태로 파일사이즈를 메가, 기가로 변경하여 출력 
$ hadoop fs -ls -h /user/
# 하위 폴더까지 조회 
$ hadoop fs -ls -R /user/
# 액세스 시간을 조회. 기본설정은 생성 시간. 마지막 접근 시간을 확인하여 파일 정리 가능 
$ hadoop fs -ls -u /user/
```

[이하는 웹사이트에서 확인](https://wikidocs.net/264960)