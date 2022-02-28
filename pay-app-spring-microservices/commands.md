docker network create distribuidos
docker run -p 5432:5432 --name postgres --network distribuidos -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=db_invoice -d icesiops/postgres:0.1.0

docker run -p 3306:3306 --name mysql --network distribuidos -e MYSQL_ROOT_PASSWORD=mysql -e MYSQL_DATABASE=db_operation -d icesiops/mysql:0.1.0

docker run -p 27017:27017 --network distribuidos --name mongodb -d mongo

docker run -p 2181:2181 -d -p 9092:9092 --name servicekafka --network distribuidos -e ADVERTISED_HOST=servicekafka -e NUM_PARTITIONS=3 johnnypark/kafka-zookeeper

docker run -d -p 8888:8888 --network distribuidos --name app-config icesiops/appconfig:0.1.0

docker run -d -p 8006:8006 --network distribuidos --name app-invoice icesiops/appinvoice:0.1.0

docker run -d -p 8010:8010 --network distribuidos --name app-pay icesiops/apppay:0.1.0

docker run -d -p 8082:8082 --network distribuidos --name app-transaction icesiops/apptransaction:0.1.0

psql -h localhost -d db_invoice -U postgres -f data.sql

#### consul

Modify application.properties file according to consul server information.
Add the line  implementation 'org.springframework.cloud:spring-cloud-starter-consul-discovery' into build.gradle depedencies
Install dnsmasq
Create a config file for dnsmasq below the path /etc/dnsmasq.d
Add the next line server=/consul/127.0.0.1#8600


docker run -d -p 8500:8500 -p 8600:8600/udp --network distribuidos --name consul consul:latest agent -server -bootstrap-expect 1 -ui -data-dir /tmp -client=0.0.0.0