Launch Instances

Name/Tag them

cd Downloads

chmod 400 key no ...pem

ssh -i key no ....pem ubuntu @ Public IP (connect to remote server)

sudo apt-get update

windows : (open cmd) 
pscp -i a.ppk prac.pem ubuntu@52.35.96.240:/home/ubuntu/.ssh	(copy the key to remote server thru local machine)
 
linux : (terminal)
scp -i key no ....pem key no ....pem ubuntu@52.35.183.191:/home/ubuntu/.ssh         (copy the key to remote server (nn)thru local machine)

On remote server go to .ssh directory (cd .ssh)
check if the key is come

-------**adding hosts**----

sudo nano /etc/hosts (nn)
eg :172.31.23.8  ip-172-31-23-8.eu-west-1.compute.internal  nn
172.31.23.7  ip-172-31-23-7.eu-west-1.compute.internal  snn
172.31.23.10  ip-172-31-23-10.eu-west-1.compute.internal  dn1
172.31.23.9  ip-172-31-23-9.eu-west-1.compute.internal  dn2

-------**configuring .profile**-----------
nano ~/.profile
eval `ssh-agent` ssh-add /home/ubuntu/.ssh/key no ....pem

source ~/.profile
------------------------------------

ssh  snn 
sudo nano /etc/hosts
eg :  172.31.23.8  ip-172-31-23-8.eu-west-1.compute.internal  nn
      172.31.23.7  ip-172-31-23-7.eu-west-1.compute.internal  snn
      172.31.23.10  ip-172-31-23-10.eu-west-1.compute.internal  dn1
      172.31.23.9  ip-172-31-23-9.eu-west-1.compute.internal  dn2
exit

ssh dn1 
sudo nano /etc/hosts
eg:   172.31.23.8  ip-172-31-23-8.eu-west-1.compute.internal  nn
      172.31.23.7  ip-172-31-23-7.eu-west-1.compute.internal  snn
      172.31.23.10  ip-172-31-23-10.eu-west-1.compute.internal  dn1
      172.31.23.9  ip-172-31-23-9.eu-west-1.compute.internal  dn2
exit

ssh  dn2 
sudo nano /etc/hosts
eg:   172.31.23.8  ip-172-31-23-8.eu-west-1.compute.internal  nn
      172.31.23.7  ip-172-31-23-7.eu-west-1.compute.internal  snn
      172.31.23.10  ip-172-31-23-10.eu-west-1.compute.internal  dn1
      172.31.23.9  ip-172-31-23-9.eu-west-1.compute.internal  dn2
exit

  ----------- **send the key to all nodes **-----------

scp .ssh/key no ....pem ubuntu@snn:/home/ubuntu/.ssh
scp .ssh/key no ....pem ubuntu@1dn:/home/ubuntu/.ssh
scp .ssh/key no ....pem ubuntu@2dn:/home/ubuntu/.ssh
scp .ssh/key no ....pem ubuntu@3dn:/home/ubuntu/.ssh

scp .profile  ubuntu@snn:/home/ubuntu
scp .profile  ubuntu@1dn:/home/ubuntu
scp .profile  ubuntu@2dn:/home/ubuntu
scp .profile  ubuntu@3dn:/home/ubuntu
 
--------**installing and configuring dsh (make sure u r in nn)**--------------

sudo apt-get install dsh

cd /etc/dsh

sudo nano machines.list

#localhost
nn
snn 
dn1
dn2


---------------------------**configuring bashrc linux env setup**---------------
nano ~/.bashrc
export HADOOP_PREFIX=/usr/local/hadoop/
export PATH=$PATH:$HADOOP_PREFIX/bin
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
export PATH=$PATH:$JAVA_HOME

scp .bashrc ubuntu@snn:/home/ubuntu
scp .bashrc ubuntu@dn1:/home/ubuntu
scp .bashrc ubuntu@dn2:/home/ubuntu
scp .bashrc ubuntu@dn2:/home/ubuntu

dsh -a bash

dsh -a source ~/.profile


-----------------------------**downloading hadoop**------------------------
dsh -a sudo apt-get update

dsh -a uptime

dsh -a wget http://apache.mirror.gtcomm.net/hadoop/common/hadoop-1.2.1/hadoop-1.2.1.tar.gz

dsh -a tar -xzvf hadoop-1.2.1.tar.gz

dsh -a sudo mv hadoop-1.2.1 /usr/local/hadoop

----------**downloading and installing java (Hadoop requires a working Java 1.6+)**--------

dsh -a sudo apt-get install openjdk-7-jdk  -y

------**setting hadoop env**------
nano /usr/local/hadoop/conf/hadoop-env.sh
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
export HADOOP_OPTS=-Djava.net.preferIPV4Stack=true

-------**configuring xml's**------
nano /usr/local/hadoop/conf/core-site.xml
<property>
<name>fs.default.name</name>
<value>hdfs://nn:9000</value>
</property>
<property>
<name>hadoop.tmp.dir</name>
<value>/usr/local/hadoop/tmp</value>
</property>

nano /usr/local/hadoop/conf/hdfs-site.xml
<property>
<name>dfs.replication</name>
<value>3</value>
</property>
<property> 
<name>dfs.permissions</name> 
<value>false</value> 
</property>

nano /usr/local/hadoop/conf/mapred-site.xml
<property>
<name>mapred.job.tracker</name>
<value>hdfs://nn:9001</value>
</property>

--------------** scp xml’s to all hosts**----------

cd /usr/local/hadoop/conf/

scp  hadoop-env.sh core-site.xml hdfs-site.xml mapred-site.xml ubuntu@snn:/usr/local/hadoop/conf/

scp  hadoop-env.sh core-site.xml hdfs-site.xml mapred-site.xml ubuntu@dn1:/usr/local/hadoop/conf/

scp  hadoop-env.sh core-site.xml hdfs-site.xml mapred-site.xml ubuntu@dn2:/usr/local/hadoop/conf/

---------------**configuring masters and slaves**-------------
nano /usr/local/hadoop/conf/masters
snn

nano /usr/local/hadoop/conf/slaves
dn1 
dn2

----------**making tmp dir**---------

dsh -a mkdir /usr/local/hadoop/tmp

-------------**exec bash**-------
dsh -a exec bash
bash

----**formatting hadoop namenode**--------------
hadoop namenode -format

---------------**starting hadoop services(daemons)**-------------------
start-all.sh 

jps
