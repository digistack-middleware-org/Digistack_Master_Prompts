# Phase 8 — SSL/TLS & Hardening

## Goal
Full Tomcat hardening. SSL termination at Nginx + direct Tomcat HTTPS. Lock down everything.

## Tomcat Skills
- #5 SSL/TLS (keytool, HTTPS connector)
- #5 Hardening (remove default apps, disable shutdown port)
- #5 RemoteAddrValve

## Step 1 — Generate Self-Signed Cert
```bash
keytool -genkeypair \
  -alias digistack \
  -keyalg RSA \
  -keysize 2048 \
  -validity 365 \
  -keystore /opt/tomcat-cbs/conf/digistack.jks \
  -storepass changeit \
  -dname "CN=digistack.local, OU=IT, O=DigiStack Bank, L=Mumbai, ST=MH, C=IN"
```

## Step 2 — HTTPS Connector (server.xml)
```xml
<Connector port="8443"
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           maxThreads="150"
           SSLEnabled="true">
  <SSLHostConfig>
    <Certificate certificateKeystoreFile="conf/digistack.jks"
                 certificateKeystorePassword="changeit"
                 type="RSA"/>
  </SSLHostConfig>
</Connector>
```

## Step 3 — Restrict TLS (SSLHostConfig)
```xml
<SSLHostConfig protocols="TLSv1.2+TLSv1.3"
               ciphers="TLS_AES_256_GCM_SHA384:TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384">
```

## Step 4 — Hardening Checklist
```bash
# Remove default apps
rm -rf /opt/tomcat-cbs/webapps/docs
rm -rf /opt/tomcat-cbs/webapps/examples
rm -rf /opt/tomcat-cbs/webapps/manager
rm -rf /opt/tomcat-cbs/webapps/host-manager
rm -rf /opt/tomcat-cbs/webapps/ROOT

# Disable shutdown port — server.xml
# Change: <Server port="8005" shutdown="SHUTDOWN">
# To:     <Server port="-1" shutdown="DISABLED">
```

## Step 4B — Security Response Headers (web.xml Filter)
Add to each WAR's `WEB-INF/web.xml`:
```xml
<!-- Security Headers Filter -->
<filter>
  <filter-name>SecurityHeadersFilter</filter-name>
  <filter-class>org.apache.catalina.filters.HttpHeaderSecurityFilter</filter-class>
  <init-param>
    <param-name>hstsEnabled</param-name>
    <param-value>true</param-value>
  </init-param>
  <init-param>
    <param-name>hstsMaxAgeSeconds</param-name>
    <param-value>31536000</param-value>
  </init-param>
  <init-param>
    <param-name>antiClickJackingEnabled</param-name>
    <param-value>true</param-value>
  </init-param>
  <init-param>
    <param-name>antiClickJackingOption</param-name>
    <param-value>DENY</param-value>
  </init-param>
  <init-param>
    <param-name>xContentTypeOptionsEnabled</param-name>
    <param-value>true</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>SecurityHeadersFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

```bash
# Verify headers
curl -I https://localhost:8443/cbs/accounts/DSB-SAV-00001 -k
# Expect: Strict-Transport-Security, X-Frame-Options: DENY, X-Content-Type-Options: nosniff
```

## Step 5 — Protect Manager App (if kept)
```xml
<!-- conf/Catalina/localhost/manager.xml -->
<Context antiResourceLocking="false" privileged="true">
  <CookieProcessor className="org.apache.tomcat.util.http.Rfc6265CookieProcessor"
                   sameSiteCookies="strict"/>
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.0\.0\.1|192\.168\.1\.\d+"/>
</Context>
```

## Verification
```bash
# Test HTTPS directly
curl -k https://localhost:8443/cbs/accounts/DSB-SAV-00001

# Verify shutdown port disabled
telnet localhost 8005
# Should refuse connection

# Verify weak TLS rejected
openssl s_client -connect localhost:8443 -tls1
# Should fail — TLS 1.0 disabled
```

## Phase-End Interview Questions
1. What is the DigiStack Tomcat shutdown port for `tomcat-cbs` — how do you disable it, and why is it a security risk?
2. What is `RemoteAddrValve` — in DigiStack, where exactly is it configured (which file, which instance)?
3. What is the difference between SSL termination at Nginx vs at Tomcat directly — which does DigiStack use and why?
4. After running `keytool -genkeypair`, what file does it produce and where is it stored in the DigiStack setup?
5. A penetration tester hits `https://digistack.local/examples/jsp/index.html` — what should happen and why?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes