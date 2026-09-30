# Phase 10 — Docker / Kubernetes / CI-CD

## Goal
Containerize everything. Jenkins pipeline with blue-green deploy.

## Tomcat Skills
- #10 Docker + Tomcat
- #2 Deployment strategies (blue-green, rolling)
- #9 Capacity planning

## Dockerfile (CBS Service)
```dockerfile
FROM tomcat:9-jdk17

# Remove default webapps
RUN rm -rf /usr/local/tomcat/webapps/*

# Copy WAR
COPY cbs.war /usr/local/tomcat/webapps/cbs.war

# Copy custom server.xml
COPY server.xml /usr/local/tomcat/conf/server.xml

# Copy context.xml (JNDI DataSource)
COPY context.xml /usr/local/tomcat/conf/context.xml

# Copy HikariCP + ojdbc8 jars
COPY lib/hikaricp.jar /usr/local/tomcat/lib/
COPY lib/ojdbc8.jar /usr/local/tomcat/lib/

EXPOSE 8080

CMD ["catalina.sh", "run"]
```

## Docker Compose (docker-compose.yml)
```yaml
version: '3.8'
services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/digistack.conf:/etc/nginx/conf.d/default.conf

  cbs:
    build: ./cbs
    ports:
      - "8090:8080"
    environment:
      - DB_URL=jdbc:oracle:thin:@oracle:1521:XE
    depends_on:
      - oracle
      - kafka

  payment:
    build: ./payment
    ports:
      - "8091:8080"
    depends_on:
      - kafka
      - cbs

  notification:
    build: ./notification
    ports:
      - "8092:8080"
    depends_on:
      - kafka

  customer-portal-1:
    build: ./customer-portal
    ports:
      - "8082:8080"

  customer-portal-2:
    build: ./customer-portal
    ports:
      - "8083:8080"

  kafka:
    image: confluentinc/cp-kafka:latest
    environment:
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092

  zookeeper:
    image: confluentinc/cp-zookeeper:latest

  oracle:
    image: gvenzl/oracle-xe:21-slim
    environment:
      ORACLE_PASSWORD: oracle123
    ports:
      - "1521:1521"
```

## Jenkins Pipeline (Jenkinsfile)
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Deploy to Node B') {
            steps {
                sh '''
                  curl -u admin:admin123 \
                    "http://localhost:8083/manager/text/deploy?path=/cbs&update=true" \
                    --upload-file target/cbs.war
                '''
            }
        }
        stage('Smoke Test') {
            steps {
                sh 'curl -f http://localhost:8083/cbs/accounts/DSB-SAV-00001'
            }
        }
        stage('Switch Nginx Upstream') {
            steps {
                sh './scripts/switch-upstream.sh node-b'
            }
        }
    }
}
```

## Passing JAVA_OPTS to Dockerized Tomcat
```bash
# Option 1 — In docker-compose.yml under the cbs service:
#   environment:
#     - JAVA_OPTS=-Xms512m -Xmx512m -XX:+HeapDumpOnOutOfMemoryError
#
# Option 2 — In Dockerfile:
#   ENV JAVA_OPTS="-Xms512m -Xmx512m -XX:+HeapDumpOnOutOfMemoryError \
#     -XX:HeapDumpPath=/usr/local/tomcat/logs/heapdump.hprof"
#
# catalina.sh reads $JAVA_OPTS automatically — no setenv.sh needed inside container
```

## Verification
```bash
# Start everything
docker-compose up -d

# Check all containers
docker-compose ps

# Test through Nginx
curl http://localhost/cbs/accounts/DSB-SAV-00001
```

## Phase-End Interview Questions
1. In the DigiStack Dockerfile, why is `RUN rm -rf /usr/local/tomcat/webapps/*` done before `COPY cbs.war`?
2. How do you pass `JAVA_OPTS` to a Dockerized Tomcat — which file reads it at startup inside the container?
3. In the Jenkins blue-green pipeline, CBS is deployed to Node B first — at what stage does Nginx switch traffic?
4. What is the Tomcat Manager text API endpoint used in the Jenkinsfile — what HTTP method and credentials?
5. In `docker-compose.yml`, `cbs` depends on `oracle` — does `depends_on` guarantee Oracle is ready to accept connections? What is the correct fix?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes