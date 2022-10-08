
docker pull atlassian/bitbucket-server:6.10.1-ubuntu-jdk8
docker pull mariadb:5.5.56

docker network create --driver bridge --subnet=172.18.0.0/16 myBitbucketNetwork

docker run -v /root/bitbucket_test:/var/atlassian/application-data/bitbucket --name="bitbucket" -d -p 7990:7990 -p 7999:7999 --network=myBitbucketNetwork --ip=172.18.1.1 atlassian/bitbucket-server:6.10.1-ubuntu-jdk8

docker run --detach --network=myBitbucketNetwork --name mariadb -v /root/mariadbtest_data:/var/lib/mysql -v /root/my.cnf:/etc/mysql/my.cnf --env MARIADB_USER=maria --env MARIADB_PASSWORD=Admin123 --env MARIADB_DATABASE=mariadb --env MARIADB_ROOT_PASSWORD=Admin123 --env MYSQL_ROOT_PASSWORD=Admin123 --ip=172.18.1.2 mariadb:5.5.56

CREATE DATABASE bitbucket CHARACTER SET utf8 COLLATE utf8_bin;
CREATE USER 'bitbucket'@'%' IDENTIFIED BY 'bitbucket123';
GRANT ALL PRIVILEGES ON bitbucket.* TO 'bitbucket'@'%';
FLUSH PRIVILEGES;

docker run --name postgres --network=myBitbucketNetwork -e POSTGRES_DB=bitbucket -e POSTGRES_USER=bitbucket -e POSTGRES_PASSWORD=bitbucket -p 5432:5432 -v /home/pramodhm/data:/var/lib/postgresql/data --ip=172.18.1.3 -d postgres:11
