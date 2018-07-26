## Hadoop分布式集群搭建
    
    Version: hadoop-3.0.3
    
### 节点角色
    
    10.28.3.31  hadoop-node1 | namenode nodemanagers resourcemanager
    10.28.3.32  hadoop-node2 | datanode
    10.28.3.33  hadoop-node3 | datanode
    10.28.3.34  hadoop-node4 | datanode

### Hadoop User
    
    groupadd hadoop
    useradd -g hadoop hadoop
    echo "hadoop" | passwd --stdin hadoop
    
    runuser -l hadoop -c "ssh-keygen -t rsa -b 2048"
    su - hadoop
    for i in `seq 1 4`;do ssh-copy-id -i .ssh/id_rsa.pub hadoop@hadoop-node${i};done
    
### Install
    
    mkdir /data/bigdata/hadoop/fdfs/{nn,snn,dn} -p
    chown -R hadoop:hadoop /data/bigdata/hadoop
     
    mkdir /opt/bdapps
    tar -zxf hadoop-3.0.3.tar.gz -C /opt/bdapps/
    cd /opt/bdapps/
    ln -s hadoop-3.0.3 hadoop
    chown -R hadoop:hadoop hadoop
    chown -R hadoop:hadoop hadoop-3.0.3
    cd hadoop
    mkdir logs
    chmod g+w logs
    chown -R hadoop:hadoop logs

    scp -r /opt/bdapps/hadoop/etc/hadoop/* hadoop-node1:/opt/bdapps/hadoop/etc/hadoop/
    scp -r /opt/bdapps/hadoop/etc/hadoop/* hadoop-node2:/opt/bdapps/hadoop/etc/hadoop/
    scp -r /opt/bdapps/hadoop/etc/hadoop/* hadoop-node3:/opt/bdapps/hadoop/etc/hadoop/
    scp -r /opt/bdapps/hadoop/etc/hadoop/* hadoop-node4:/opt/bdapps/hadoop/etc/hadoop/
    
### NameNode
    
    su - hadoop
    hadoop checknative -a
    
    hdfs namenode -format
    start-dfs.sh
    start-yarn.sh
    
### DataNode
    
    su - hadoop
    start-dfs.sh
    
### yarn命令
    
    yarn application -list
    yarn application -list -appStates=all
    yarn node -list
    yarn node -status hadoop-node2:37564
    
### Web管理
    
    http://10.28.3.31:9870/
    http://10.28.3.31:8088/

### Test
    
    hdfs dfs -mkdir /test
    hdfs dfs -put /etc/hosts /test/1.txt
    hdfs dfs -ls /test
    hdfs dfs -ls -R /test
    hdfs dfs -get /test/1.txt
    ls /data/bigdata/hadoop/fdfs/dn/current/
    
