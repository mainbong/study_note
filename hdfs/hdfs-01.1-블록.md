# 블록

HDFS 파일은 지정한 크기의 블록으로 나누어지고, 각 블록은 독립적으로 저장됨.

블록은 128MB와 같이 매우 큰 단위. 블록이 큰 이유는 탐색비용 최소화. 블록이 크면 하드디스크에서 블록의 시작점을 탐색하는 데 걸리는 시간을 줄일 수 있고, 네트워크를 통해 데이터를 전송하늗네 더 많은 시간을 할당할 수 있음. 따라서 여러 개의 블록으로 구성된 대용량 파일을 전송하는 시간은 디스크 IO 속도에 크게 영향을 받게 됨.

## 블록 크기 파일 분할

* 기본 블록 크기를 넘어서는 파일은 블록 크기로 분할하여 저장.
* 블록 크기보다 작은 파일은 단일블록으로 저장
  * 실제 파일 크기의 블록이 됨
* 블록 단위로 나누어 저장하기 때문에 디스크 사이즈보다 더 큰 파일을 보관할 수 있음
  * 블록 단위로 파일을 나누어 저장하기 때문에 700G * 2 = 1.4T 크기의 HDFS에 1T의 파일 저장 가능

## 블록 추상화의 이점

  1. 파일 하나의 크기가 단일 디스크의 용량보다 커질 수 있음.
  2. 파일 단위보다 블록 단위의 추상화를 하면 스토리지의 서브 시스템을 단순하게 만들 수 있다는 것. 파일 탐색 지점이나 메타정보를 저장할 때 사이즈가 고정되어 있으므로 구현이 좀 더 쉬움.
  3. 내고장성을 제공하는데 필요한 복제(replication)을 구현할 때 매우 적합함. 블록 당뉘로 복제를 구현하기 때문에 데이터 복제에도 어려움 없이 처리할 수 있음. 같은 노드에 같은 블록이 존재하지 않도록 하여 노드가 고장일 경우 다른 노드의 블록으로 복구할 수 있음.

## 블록 지역성 (Block locality)

맵리듀스를 이용한 분산 컴퓨팅은 블록의 locality를 이용해 성능을 높임. 맵리듀스가 처리할 때 현재 노드에 저장되어 있는 블록을 이용하는 블록 locality를 통해 성능을 높일 수 있음.

* 네트워크를 이용한 데이터 전송 시간 감소
* 대용량 데이터 확인을 위한 디스크 탐색 시간 감소
* 적절한 단위의 블록 크기를 이용한 CPU 처리시간 증가

### 블록 작업 순서

블록 locality를 위한 작업 우선 순위는 다음과 같음
1. 같은 노드에 있는 데이터
2. 같은 랙(Rack)의 노드에 있는 데이터
3. 다른 랙의 노드에 있는 데이터

## 블록 스캐너 (Block scanner)

데이터 노드는 주기적으로 블록 스캐너를 실행하여 블록의 쳌섬을 확인하고 오류가 발생하면 수정함.

## 블록 캐싱 (Block Cacheing)

데이터 노드에 저장된 데이터 중 자주 읽는 블록은 블록 캐시(block cache)라는 데이터 노드의 메모리에 명시적으로 캐싱할 수 있음. 파일 단위로 캐싱할 수도 있어서 조인에 사용되는 데이터들을 등록하여 읽기 성능을 높일 수 있음.

```bash
$ hdfs cacheadmin
Usage: bin/hdfs cacheadmin [COMMAND]
          [-addDirective -path <path> -pool <pool-name> [-force] [-replication <replication>] [-ttl <time-to-live>]]
          [-modifyDirective -id <id> [-path <path>] [-force] [-replication <replication>] [-pool <pool-name>] [-ttl <time-to-live>]]
          [-listDirectives [-stats] [-path <path>] [-pool <pool>] [-id <id>]
          [-removeDirective <id>]
          [-removeDirectives -path <path>]
          [-addPool <name> [-owner <owner>] [-group <group>] [-mode <mode>] [-limit <limit>] [-maxTtl <maxTtl>]
          [-modifyPool <name> [-owner <owner>] [-group <group>] [-mode <mode>] [-limit <limit>] [-maxTtl <maxTtl>]]
          [-removePool <name>]
          [-listPools [-stats] [<name>]]
          [-help <command-name>]

# pool 등록 
$ hdfs cacheadmin -addPool pool1
Successfully added cache pool pool1.

# path 등록
$ hdfs cacheadmin -addDirective -path /user/hadoop/shs -pool pool1
Added cache directive 1

# 캐쉬 확인 
 hdfs cacheadmin -listDirectives
Found 1 entry
 ID POOL    REPL EXPIRY  PATH             
  1 pool1      1 never   /user/hadoop/shs 
```