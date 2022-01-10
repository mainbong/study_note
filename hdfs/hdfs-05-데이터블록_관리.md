# 05. HDFS 데이터 블록 관리

 HDFS 운영 중 데이터 노드에 문제가 생기면, 데이터 블록에 문제가 발생할 수 있음. corrupt 상태와 under replicated 상태임.

## 05.1 Corrupt 블록

HDFS는 하트비트를 통해 데이터 블록에 문제가 생기는 것을 감지하고 자동으로 복구를 진행함. 다른 데이터 노드에 복제된 데이터를 가져와서 복구함. 하지만 모든 복제 블록에 문제가 생겨서 복구하지 못하게 되면 corrupt 상태가 됨. corrupt 상태의 파일들은 삭제하고, 원본 파일을 다시 hdfs 에 올려줘야 함.

### 05.1.1. corrupt 상태 확인

HDFS 커맨드의 `fsck`를 이용하여 상태를 체크할 수 있음. 커럽트 상태의 블록과 복제 개수가 부족한 블록의 정보를 확인할 수 있음.

```bash
# 루트의 상태 체크 
$ hdfs fsck /

# /user/hadoop/ 디렉토리의 상태 체크 
$ hdfs fsck /user/hadoop/

Status: HEALTHY
 Total size:    1378743129 B
 Total dirs:    612
 Total files:   2039
 Total symlinks:        0
 Total blocks (validated):  2039 (avg. block size 676185 B)
 Minimally replicated blocks:   2039 (100.0 %)
 Over-replicated blocks:    0 (0.0 %)
 Under-replicated blocks:   2039 (100.0 %)
 Mis-replicated blocks:     0 (0.0 %)
 Default replication factor:    2
 Average block replication: 1.0
 Corrupt blocks:        0
 Missing replicas:      4004 (66.258484 %)
 Number of data-nodes:      1
 Number of racks:       1
FSCK ended at Thu Dec 06 05:31:42 UTC 2018 in 37 milliseconds


The filesystem under path '/user/hadoop' is HEALTHY
```

상태를 확인하고, 커럽트 상태이면 `hdfs fsck -delete` 명령을 이용하여 커럽트 상태의 블록을 삭제함. 커럽트 상태의 방지를 위해서 중요한 파일은 복제 개수를 늘려주면 좋음. `hdfs fs -setrep`명령을 이용하여 처리함.

```bash
The filesystem under path '/user/hadoop' is CORRUPT

# 커럽트 상태의 파일 삭제 
$ hdfs fsck -delete

# /user/hadoop/ 의 복제 개수를 5로 조정 
$ hadoop fs -setrep 5 /user/hadoop/
# /user/hadoop/ 하위의 모든 파일의 복제 개수를 조정 
$ hadoop fs -setrep 5 -R /user/hadoop/
```

## 05.2 Replicated block 부족 상태

복제개수 부족상태는 파일에 지정된 복제 개수 만큼 데이터 블록에 복제되지 않았을 때 발생함. 데이터 노드의 개수보다 복제 개수를 많이 지정했을 때 발생할 수 있음. `fsck` 명령으로 복제 개수가 부족한 파일을 확인할 수 있음. 복제 개수가 부족한 파일은 복제 개수를 늘려주면 됨. `hadoop fs -setrep` 명령을 이용하여 처리함

```bash
# /user/hadoop/ 의 복제 개수를 5로 조정 
$ hadoop fs -setrep 5 /user/hadoop/

# /user/hadoop/ 하위의 모든 파일의 복제 개수를 조정 
$ hadoop fs -setrep 5 -R /user/hadoop/
```