# 1 镜像
	镜像是多个层叠式的只读文件；

# 2 容器
	容器 = 层叠只读文件 + 读写文件 （copy on write）;

# 3 常用docker命令

	service docker start -- 启动docker服务进程；
	docker pull xxx -- 拉取xxx镜像；
	docker run xxx -- 创建xxx容器；
	docker stop xxx -- 停止xxx容器；
	docker rm xxx -- 移除xxx容器；
	docker rmi xxx -- 移除xxx镜像；
	docker inspect xxx -- 查看xxx镜像信息
	docker ps -- 查看正在运行的docker容器
	docker cp xxx:yyy zzz -- 拷贝xxx容器yyy目录或者文件到宿主机的zzz	例如，docker cp nginx:/etc/nginx/nginx.conf /root/Docker/nginx/conf/nginx.conf（拷贝名称为nginx的容器的/etc/nginx/nginx.conf到宿主机的/root/Docker/nginx/conf/nginx.conf）
	docker exec -it xxx /bin/bash  -- 进入xxx容器
	docker logs -f xxx -- 查看xxx容器的日志

# 4 常用docker容器启动示例
## 4.1 启动postgresql容器(使用集成了postgis的镜像)
+ 启动命令：docker run --name postgres -d -p 5432:5432 -e POSTGRES_USER=sonar -e POSTGRES_PASSWORD=sonar -e POSTGRES_DB=sonar -v /mnt/docker/container/postgres/data/:/var/lib/postgresql/data postgres
+ 启动参数释义：

	--name 指定容器名称

	-p 端口映射，冒号前为宿主机端口，冒号后为容器端口

	-v 挂载容器目录到宿主机目录，冒号前为宿主机目录，冒号后为容器目录
	
	-e 设置容器环境变量

	-d 容器在后台运行

+ 启动完容器后如果需要使用postgis，还需要执行以下命令：

	CREATE EXTENSION postgis;

	CREATE EXTENSION postgis_topology;

## 4.2 启动nginx容器
+ 启动命令：docker run --name nginx -d -p 80:80 -v /root/Docker/nginx/conf:/etc/nginx/conf -v /root/Docker/nginx/html:/etc/nginx/html -v /mnt/data/nginx_data:/mnt/data/nginx_data nginx
+ 注意：

	如果 -v 挂载的为配置文件，则需要在启动之前手动在宿主机上创建对应文件；

	在nginx.conf 中配置的路径为【nginx容器中的路径】；

## 4.3 启动tomcat容器
+ 启动命令：docker run --name tomcat -d -p 8080:8080 -v /root/Docker/tomcat/conf:/usr/local/tomcat/conf -v /root/Docker/tomcat/webapps:/usr/local/tomcat/webapps tomcat
+ 注意：

	如果 -v 挂载的为配置文件，则需要在启动之前手动在宿主机上创建对应文件(/root/Docker/tomcat/conf目录下放置server.xml文件)；

## 4.4 启动部署TDS服务的tomcat容器
+ 启动命令：docker run --name tomcat -d -p 8080:8080 -v /root/Docker/tomcat/conf:/usr/local/tomcat/conf -v /root/Docker/tomcat/webapps:/usr/local/tomcat/webapps -v /root/Docker/tomcat/bin/setenv.sh:/usr/local/tomcat/bin/setenv.sh -v /root/Docker/tomcat/content:/usr/local/tomcat/content -v /mnt/data/nc/LAPS3KM:/mnt/data/nc/LAPS3KM tomcat
+ 注意：

	如果 -v 挂载的为配置文件，则需要在启动之前手动在宿主机上创建对应文件(/root/Docker/tomcat/bin目录下放置setenv.sh)；

## 4.5 启动elasticsearch容器
+ 启动命令：docker run -d --name es -p 9200:9200 -p 9300:9300 -v /root/Docker/elasticsearch/config:/usr/share/elasticsearch/config -v /root/Docker/elasticsearch/data:/usr/share/elasticsearch/data -v /root/Docker/elasticsearch/logs:/usr/share/elasticsearch/logs elasticsearch

## 4.6 启动elasticsearch插件kibana
+ 启动命令：docker run --name kibana --link es:elasticsearch -p 5601:5601 -d kibana
+ 注意：

	--link es:elasticsearch 中的es为已启动的elasticsearch容器的名称

## 4.7 启动elasticsearch插件elasticsearch-head
+ 启动命令：docker run -d --name es-head -p 9100:9100 -v /root/Docker/elasticsearch-head/Gruntfile.js:/usr/src/app/Gruntfile.js -v /root/Docker/elasticsearch-head/app.js:/usr/src/app/_site/app.js mobz/elasticsearch-head:5

## 4.8 启动sonarqube容器
+ 启动命令：docker run --name sonarqube -d -p 9000:9000 --link postgres -e sonar.jdbc.username=sonar -e sonar.jdbc.password=sonar -e sonar.jdbc.url=jdbc:postgresql://postgres:5432/sonar -v /mnt/docker/container/sonarqube/conf/:/opt/sonarqube/conf -v /mnt/docker/container/sonarqube/data:/opt/sonarqube/data -v /mnt/docker/container/sonarqube/logs:/opt/sonarqube/logs -v /mnt/docker/container/sonarqube/temp/:/opt/sonarqube/temp -v /mnt/docker/container/sonarqube/extensions/:/opt/sonarqube/extensions  sonarqube
+ 注意：

	--link es:elasticsearch 中的es为已启动的elasticsearch容器的名称

# 5 进入docker容器内部
+ 命令：docker exec -it 775c7c9ee1e1 /bin/bash
+ 命令参数释义：

	-it 申请控制台

	775c7c9ee1e1为容器id，也可以使用容器别名替代

# 6 查看docker容器日志
+ 命令：docker logs -f 775c7c9ee1e1
+ 命令参数释义：

	-f 实时监控

	775c7c9ee1e1为容器id，也可以使用容器别名替代