# 12. 하둡2 소개

## 12.7 쇼트 서킷 조회
> 모든 분산파일 시스템은 당연히 데이터노드를 통해서만 액세스가 가능하지만, 생각해보면 맵리듀스나 별도의 타스크가 실행되는 경우에도 항상 데이터노드를 통하기 때문에 불필요한 접근이 지속적으로 발생한다. 이러한 단점을 해소하기 위해 나온 것이 **쇼트 서킷 조회**인데, DFSClient가 DataTransferProtocol을 통해 데이터 노드와 통신하는 경우 HDFS의 어느 블록에 접근하더라도 TCP 소켓 비용이 발생하는데, 이 말은 블록 접근이 많으면 많을수록 소켓통신의 빈도도 같이 높아진다는 의미입니다. 원격지 블록은 어쩔 수 없지만, 로컬블록인 경우에는 성능저하의 원인 중 하나로 알려져 있습니다.

### [What is short circuit read?](http://www.openkb.info/2014/06/what-is-short-circuit-local-reads.html)
* 기존의 파일읽기는 항상 DataTransferProtocol을 통해 데이터노드의 TCP 통신이 발생한다
![short-circuit-read-1.png](images/short-circuit-read-1.png)
* 최초 접근 시에는 GetBlockLocalPathInfo를 통해 블록의 위치를 조회하고 이후부터는 로컬파일을 직접 읽고, 쓸 수 있게 된다
![short-circuit-read-2.png](images/short-circuit-read-2.png)
```hdfs-site.xml
    dfs.block.local-path-access.user:           gpadmin,hdfs,mapred,yarn,hbase,hive
    dfs.client.read.shortcircuit:               true
    dfs.client.read.shortcircuit.skip.checksum: false
    dfs.domain.socket.path
```
