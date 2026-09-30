# Phase 4 — Payment Engine + Kafka

## Goal
Payment Engine as separate Tomcat WAR. Learn app lifecycle, thread pools, Kafka integration.

## Tomcat Skills
- #8 App Lifecycle (ServletContextListener)
- #3 Thread pool sizing

## Tomcat Instance
Port: 8091
Instance: /opt/tomcat-payment/
Shutdown Port: 8105

### server.xml — HTTP Connector for Payment Engine
File: /opt/tomcat-payment/conf/server.xml
```xml
<Server port="8105" shutdown="SHUTDOWN">

<Connector port="8091"
           protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443"/>
```

## Flow
```
CBS :8090 → REST POST → Payment Engine :8091
│
Validate (balance, frozen)
│
Write TRANSACTIONS table
│
Publish → Kafka topic: payments
│
Notification Service :8092 (consumer)
│
Write NOTIFICATIONS table
```

## Kafka Topics
| Topic | Producer | Consumer |
|-------|----------|----------|
| payments | Payment Engine | Notification Service |
| notifications | Notification Service | (log/DB write) |

## Key Tomcat Skill: ServletContextListener
```java
public class KafkaConsumerListener implements ServletContextListener {
    private KafkaConsumer consumer;
    private ExecutorService executor;

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        // Start Kafka consumer thread here
        executor = Executors.newSingleThreadExecutor();
        executor.submit(() -> consumer.poll(...));
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        // Clean shutdown — critical Tomcat skill
        consumer.close();
        executor.shutdown();
    }
}
```
## web.xml — Register ServletContextListener
File: payment.war → WEB-INF/web.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         version="4.0">

  <!-- Kafka consumer starts/stops with the app -->
  <listener>
    <listener-class>com.digistack.payment.KafkaConsumerListener</listener-class>
  </listener>

  <servlet>
    <servlet-name>PaymentServlet</servlet-name>
    <servlet-class>com.digistack.payment.PaymentServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>PaymentServlet</servlet-name>
    <url-pattern>/payment/*</url-pattern>
  </servlet-mapping>

</web-app>
```

## Verification
```bash
# Trigger payment via CBS
curl -X POST http://localhost:8090/cbs/transfer/external \
  -H "Content-Type: application/json" \
  -d '{"from":"DSB-SAV-00001","to":"DSB-SAV-00002","amount":1000,"type":"IMPS"}'

# Check Kafka topic
kafka-console-consumer.sh --topic payments --bootstrap-server localhost:9092 --from-beginning

# Check notification
SELECT * FROM notifications ORDER BY created_at DESC;
```


## Phase-End Interview Questions
1. Why use `ServletContextListener` to start a Kafka consumer instead of a background thread in the servlet?
2. What happens to a Kafka consumer thread if `contextDestroyed()` is not implemented — what does `catalina.out` show?
3. What is the difference between Tomcat connector threads (HTTP handlers) and your Kafka consumer thread pool?
4. In DigiStack, CBS calls Payment Engine at `:8091` — is this synchronous or async? What are the trade-offs?
5. What log line in `catalina.out` confirms `KafkaConsumerListener.contextInitialized()` ran successfully?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes