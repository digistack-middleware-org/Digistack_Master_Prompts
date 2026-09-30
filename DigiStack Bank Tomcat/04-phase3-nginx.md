# Phase 3 — Nginx Reverse Proxy

## Goal
Put Nginx in front of CBS. Learn HTTP proxy_pass vs AJP, compression.

## Tomcat Skills
- #2 Reverse proxy deployment
- #3 Compression tuning

## Exercises
1. proxy_pass HTTP → CBS :8090
2. Switch to AJP connector — compare behavior
3. Configure proxy_send_timeout + upstream keepalive
4. Static assets from Nginx, dynamic from Tomcat
5. gzip at Nginx vs gzip at Tomcat connector — compare

## Nginx Config
File: /etc/nginx/conf.d/digistack.conf

```nginx
upstream cbs_backend {
    server localhost:8090;
    keepalive 32;
}

server {
    listen 80;
    server_name digistack.local;

    location /cbs/ {
        proxy_pass http://cbs_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_send_timeout 30s;
        proxy_read_timeout 30s;
    }

    location /static/ {
        root /opt/digistack/static;
    }
}
```

## AJP Nginx Config (when using AJP connector)
File: /etc/nginx/conf.d/digistack.conf — AJP variant
```nginx
# AJP requires mod_proxy_ajp — use HTTP connector instead for Nginx
# Nginx does NOT support AJP natively.
# To use AJP: put Apache httpd (mod_proxy_ajp) in front, OR keep Nginx with HTTP connector.
# Exercise: compare latency — HTTP proxy_pass vs AJP through Apache httpd
```

## AJP Connector (server.xml on CBS Tomcat)
```xml
<Connector protocol="AJP/1.3"
           address="::1"
           port="8009"
           redirectPort="8443"
           secretRequired="false" />
```

## Verification
```bash
curl http://digistack.local/cbs/accounts/DSB-SAV-00001
# Must return same response as direct :8090 call
```

## Phase-End Interview Questions
1. What is the difference between Nginx `proxy_pass` (HTTP) and AJP connector — which is faster and why?
2. Why was AJP disabled by default starting in Tomcat 9.0.31 (CVE-2020-1938 — Ghostcat)?
3. What does `proxy_set_header X-Real-IP $remote_addr` do — why does CBS need it?
4. What is the risk of enabling gzip at both Nginx and Tomcat simultaneously?
5. In the DigiStack setup, a request hits `digistack.local/cbs/accounts/DSB-SAV-00001` — trace it hop by hop.

## Status
- [ ] Not started
- [ ] In progress  
- [ ] Completed

## Notes