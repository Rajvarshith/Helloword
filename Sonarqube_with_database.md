Ref:https://github.com/ravdy/DevOps/blob/master/sonarqube/Sonarqube_with_database.md (view in Edit mode for get clarity)
Need an EC2 instance (min t2.small)
Install Java-11
 apt-get update   
 apt  list | grep openjdk-11  
 apt-get install openjdk-11-jdk -y   
 Install & Setup Postgres Database for SonarQube
 Source: https://www.postgresql.org/download/linux/ubuntu/

Install Postgres database
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'  
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql
Set a password and connect to database (setting password as "admin" password)
sudo passwd postgres
su - postgres
Create a database user and database for sonarque
createuser sonar
psql
ALTER USER sonar WITH ENCRYPTED PASSWORD 'admin';
CREATE DATABASE sonarqube OWNER sonar;
GRANT ALL PRIVILEGES ON DATABASE sonarqube to sonar;
Restart postgres database to take latest changes effect
systemctl restart postgresql 
systemctl status postgresql
check point: You should see postgres is running on 5432

apt install net-tools

Source: https://docs.sonarqube.org/latest/requirements/requirements/

Added below entries in /etc/sysctl.conf
vm.max_map_count=524288
fs.file-max=131072
ulimit -n 131072
ulimit -u 8192
Add below entries in /etc/security/limits.conf
sonarqube   -   nofile   131072
sonarqube   -   nproc    8192
reboot the server
init 6
==========================================

SonarQube Setup
Download soarnqube and extract it.
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.2.46101.zip
unzip sonarqube-8.9.2.46101.zip
Update sonar.properties with below information
sonar.jdbc.username=<sonar_database_username>
sonar.jdbc.password=<sonar_database_password>

#sonar.jdbc.username=sonar
#sonar.jdbc.password=admin
#sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
#sonar.search.javaOpts=-Xmx512m -Xms512m -XX:MaxDirectMemorySize=256m -XX:+HeapDumpOnOutOfMemoryError
``

1. Create a `/etc/systemd/system/sonarqube.service` file start sonarqube service at the boot time 
```sh   
cat >> /etc/systemd/system/sonarqube.service <<EOL
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking
User=sonar
Group=sonar
PermissionsStartOnly=true
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start 
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
StandardOutput=syslog
LimitNOFILE=65536
LimitNPROC=4096
TimeoutStartSec=5
Restart=always

[Install]
WantedBy=multi-user.target
EOL
Add sonar user and grant ownership to /opt/sonarqube directory
useradd -d /opt/sonarqube sonar
chown -R sonar:sonar
Reload the demon and start sonarqube service
systemctl daemon-reload 
systemctl start sonarqube.service 

HIstory 
root@ip-172-31-27-10:/opt# history
    1  clear
    2  java --version
    3  clear
    4  pwd
    5  sudo su-
    6  clear
    7  sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    8  wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
    9  sudo apt-get update
   10  sudo apt-get -y install postgresql
   11  clear
   12  systemctl status postgresql
   13  cat /etc/postgressql
   14  sudo passwd postgres
   15  su - postgres
   16  clear
   17  systemctl restart postgresql
   18  systemctl status postgresql
   19  apt  install net - tools
   20  apt  install net -tools
   21  clear
   22  sudo apt  install net - tools
   23  sudo apt  install net -tools
   24  sudo apt  install net - tools
   25  check point
   26  apt install gitlab-shell
   27  check point
   28  apt install net-tools
   29  netstat -tulpn
   30  vi /etc/sysctl.conf
   31  vi /etc/security/limits.conf
   32  init 6
   33  cd /opt/
   34  ls
   35  wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.3.48735.zip
   36  ll
   37  apt install unzip
   38  unzip sonarqube-8.9.3.48735.zip
   39  cd sonarqube-8.9.3.4873/
   40  cd sonarqube-8.9.3.48735
   41  clear
   42  ll
   43  cd conf/
   44  ls
   45  vi sonar.properties
   46  cat >> /etc/systemd/system/sonarqube.service <<EOL
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking
User=sonar
Group=sonar
PermissionsStartOnly=true
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
StandardOutput=syslog
LimitNOFILE=65536
LimitNPROC=4096
TimeoutStartSec=5
Restart=always

[Install]
WantedBy=multi-user.target
EOL

   47  cat /etc/systemd/system/sonarqube.service
   48  cd ../
   49  cd  /bin/
   50  cd..
   51  cd ../
   52  cd opt/
   53  ls
   54  cd sonarqube-8.9.3.48735/bin/
   55  ls
   56  cd linux-x86-64/
   57  pwd
   58  cd /opt
   59  mv sonarqube-8.9.3.48735/ sonarqube
   60  useradd -d /opt/sonarqube sonar
   61  ls
   62  ls -l
   63  chown -R sonar:sonar /opt/sonarqube
   64  ls -l
   65  systemctl daemon-reload
   66  systemctl start sonarqube.service
   67  ls -l
   68  ps -ef |grep sonar
   69  netstat -tulpn
   70  history
