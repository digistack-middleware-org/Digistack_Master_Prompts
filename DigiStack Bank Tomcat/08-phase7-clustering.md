# Phase 7 — Clustering & High Availability

## Goal
Run 2 Customer Portal instances behind Nginx. Practice session replication and failover.

## Tomcat Skills
- #4 Clustering (DeltaManager)
- #4 Sticky sessions
- #4 Failover testing

## Setup
```
Nginx :80
│
├── Customer Portal 1 :8082
└── Customer Portal 2 :8083
```

## Step 1 — Sticky Sessions (Nginx)
```nginx
upstream customer_portal {
    hash $cookie_JSESSIONID consistent;
    server localhost:8082;
    server localhost:8083;
}
```

## Step 1B — Enable Distributable Sessions (web.xml — Customer Portal)
File: customer-portal.war → WEB-INF/web.xml
```xml
<web-app ...>
  <!-- REQUIRED for DeltaManager session replication -->
  <distributable/>
  ...
</web-app>
```
Without `<distributable/>`, Tomcat logs a warning and does NOT replicate sessions even with clustering configured.

## Step 2 — DeltaManager Clustering (server.xml on BOTH instances)



```xml
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
         channelSendOptions="8">

  <Manager className="org.apache.catalina.ha.session.DeltaManager"
           expireSessionsOnShutdown="false"
           notifyListenersOnReplication="true"/>

  <Channel className="org.apache.catalina.tribes.group.GroupChannel">
    <Membership className="org.apache.catalina.tribes.membership.McastService"
                address="228.0.0.4"
                port="45564"
                frequency="500"
                dropTime="3000"/>
    <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
              address="auto"
              port="4001"
              autoBind="100"
              selectorTimeout="5000"
              maxThreads="6"/>
    <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
      <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
    </Sender>
  </Channel>
  <Valve className="org.apache.catalina.ha.tcp.ReplicationValve" filter=""/>
  <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/>
</Cluster>
```

## Step 2B — jvmRoute (server.xml — required for sticky sessions)
File: /opt/tomcat-customer1/conf/server.xml
```xml
<Engine name="Catalina" defaultHost="localhost" jvmRoute="node1">
```

File: /opt/tomcat-customer2/conf/server.xml
```xml
<Engine name="Catalina" defaultHost="localhost" jvmRoute="node2">
```
After login, JSESSIONID will look like: `ABC123.node1`
Nginx uses the suffix to route the same user back to node1.

## Step 3 — Failover Test
```bash
# Login to customer portal
curl -c cookies.txt http://localhost/customer/login

# Kill node 1
systemctl stop tomcat-customer1

# Verify session still alive on node 2
curl -b cookies.txt http://localhost/customer/dashboard
# Must still work — session replicated
```
## Phase-End Interview Questions
1. What is the difference between DeltaManager and BackupManager in Tomcat clustering?
2. In the DigiStack setup, a user logs into Customer Portal Node 1 (:8082) — what is the format of their JSESSIONID cookie after jvmRoute is set?
3. You kill tomcat-customer1 — what happens if <distributable/> is missing from customer-portal.war?
4. Why is multicast (address 228.0.0.4) sometimes blocked in cloud environments — what is the alternative for AWS/GCP?
5. How does hash $cookie_JSESSIONID consistent in Nginx differ from ip_hash — which is better for banking portals and why?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes