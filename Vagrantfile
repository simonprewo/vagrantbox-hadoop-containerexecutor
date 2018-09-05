# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"

  # Forward all Hadoop relevant ports
  config.vm.network "forwarded_port", guest: 50090, host: 50090
  config.vm.network "forwarded_port", guest: 50070, host: 50070
  config.vm.network "forwarded_port", guest: 50010, host: 50010
  config.vm.network "forwarded_port", guest: 50075, host: 50075
  config.vm.network "forwarded_port", guest: 8088, host: 8088
  config.vm.network "forwarded_port", guest: 8030, host: 8030
  config.vm.network "forwarded_port", guest: 8025, host: 8025
  config.vm.network "forwarded_port", guest: 8141, host: 8141
  config.vm.network "forwarded_port", guest: 8142, host: 8142
  config.vm.network "forwarded_port", guest: 8032, host: 8032


  config.vm.provider "virtualbox" do |vb|
     # Customize the amount of memory on the VM in our case 2048MB
     vb.memory = "2048"
  end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL
     apt-get update
     apt-get install -y default-jdk

     # Setup Passwordless SSH to localhost
     ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
     cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
     chmod 0600 ~/.ssh/authorized_keys
     ssh -oStrictHostKeyChecking=no localhost ls
     ssh -oStrictHostKeyChecking=no 0.0.0.0 ls
     
     
     # Configure Environment Variable of Hadoop
     echo "export HADOOP_HOME=\"/usr/local/hadoop\"" > /etc/profile.d/hadoop.sh
     echo "export PATH=\$PATH:/usr/local/hadoop/bin" >> /etc/profile.d/hadoop.sh
     source /etc/profile.d/hadoop.sh
 
     wget ftp://ftp.fu-berlin.de/unix/www/apache/hadoop/common/hadoop-2.9.1/hadoop-2.9.1.tar.gz || wget ftp://ftp-stud.hs-esslingen.de/pub/Mirrors/ftp.apache.org/dist/hadoop/common/hadoop-2.9.1/hadoop-2.9.1.tar.gz
     tar -xzvf hadoop-2.9.1.tar.gz
     sudo mv hadoop-2.9.1 /usr/local/hadoop
     echo "export JAVA_HOME=\$(readlink -f /usr/bin/java | sed \"s:bin/java::\")" >>  ${HADOOP_HOME}/etc/hadoop/hadoop-env.sh

     # Configure HDFS
     echo "<configuration>" > ${HADOOP_HOME}/etc/hadoop/core-site.xml
     echo "  <property>" >> ${HADOOP_HOME}/etc/hadoop/core-site.xml
     echo "    <name>fs.defaultFS</name>" >> ${HADOOP_HOME}/etc/hadoop/core-site.xml
     echo "    <value>hdfs://localhost:9000</value>" >> ${HADOOP_HOME}/etc/hadoop/core-site.xml
     echo "  </property>" >> ${HADOOP_HOME}/etc/hadoop/core-site.xml
     echo "</configuration>" >> ${HADOOP_HOME}/etc/hadoop/core-site.xml
     echo "<configuration>" > ${HADOOP_HOME}/etc/hadoop/hdfs-site.xml
     echo "  <property>" >> ${HADOOP_HOME}/etc/hadoop/hdfs-site.xml
     echo "    <name>dfs.replication</name>" >> ${HADOOP_HOME}/etc/hadoop/hdfs-site.xml
     echo "    <value>1</value>" >> ${HADOOP_HOME}/etc/hadoop/hdfs-site.xml
     echo "  </property>" >> ${HADOOP_HOME}/etc/hadoop/hdfs-site.xml
     echo "</configuration>" >> ${HADOOP_HOME}/etc/hadoop/hdfs-site.xml

     # Format HDFS
     hdfs namenode -format

     # Start HDFS     
     ${HADOOP_HOME}/sbin/start-dfs.sh

     # Configure Yarn
     echo "<configuration>" > ${HADOOP_HOME}/etc/hadoop/mapred-site.xml
     echo "  <property>" >> ${HADOOP_HOME}/etc/hadoop/mapred-site.xml
     echo "    <name>mapreduce.framework.name</name>" >> ${HADOOP_HOME}/etc/hadoop/mapred-site.xml
     echo "    <value>yarn</value>" >> ${HADOOP_HOME}/etc/hadoop/mapred-site.xml
     echo "  </property>" >> ${HADOOP_HOME}/etc/hadoop/mapred-site.xml
     echo "</configuration>" >> ${HADOOP_HOME}/etc/hadoop/mapred-site.xml
     echo "<configuration>" > ${HADOOP_HOME}/etc/hadoop/yarn-site.xml
     echo "  <property>" >> ${HADOOP_HOME}/etc/hadoop/yarn-site.xml
     echo "    <name>yarn.nodemanager.aux-services</name>" >> ${HADOOP_HOME}/etc/hadoop/yarn-site.xml
     echo "    <value>mapreduce_shuffle</value>" >> ${HADOOP_HOME}/etc/hadoop/yarn-site.xml
     echo "  </property>" >> ${HADOOP_HOME}/etc/hadoop/yarn-site.xml
     echo "</configuration>" >> ${HADOOP_HOME}/etc/hadoop/yarn-site.xml

     # Start Yarn
     ${HADOOP_HOME}/sbin/start-yarn.sh
     
   SHELL
end
