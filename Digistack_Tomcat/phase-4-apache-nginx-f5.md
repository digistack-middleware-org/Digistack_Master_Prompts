# PHASE 4: Apache, Nginx & F5 (Day 71–105)
Goal: Master every traffic layer above Tomcat; end-to-end architecture mastery.
Milestone Day 105: Whiteboard FULL architecture + slow-request diagnosis, 15 min, no notes. 💰 Money phase.

## Module 9: Apache HTTPD Fundamentals (Day 71–80)
| Day | Topic | Lab |
|---|---|---|
| 71 | Apache architecture: MPM concept — process/thread model | Install Apache; verify version & MPM (httpd -V, apachectl -M) |
| 72 | Prefork vs Worker vs Event MPM — deep comparison | Switch MPMs; observe process/thread behavior with ps |
| 73 | Directory structure, httpd.conf, conf.d/, includes logic | Annotate httpd.conf line by line |
| 74 | Key directives: KeepAlive, MaxRequestWorkers, Timeout, ServerLimit | Stress test with ab; observe effects of each |
| 75 | Modules system: static vs shared | Enable/disable modules; break config and fix |
| 76 | mod_proxy + mod_proxy_http — reverse proxy basics | Proxy Apache → Tomcat via HTTP; verify headers & logs |
| 77 | mod_proxy_ajp — AJP protocol, port 8009 | Configure AJP → Tomcat; compare with HTTP |
| 78 | mod_jk (legacy): workers.properties, worker roles | Set up mod_jk with 2 Tomcat workers |
| 79 | ⭐ Comparison: mod_jk vs mod_proxy_ajp vs mod_proxy_http | mod_proxy_balancer with 2 Tomcats + failover; kill one |
| 80 | Logs: access log, error log, %D; mod_status | Enable %D; run load; find slow requests in access log |

## Module 10: Apache SSL & Virtual Hosting (Day 81–88)
| Day | Topic | Lab |
|---|---|---|
| 81 | SSL/TLS handshake refresher; what "termination" means | Draw SSL termination architecture |
| 82 | Why SSL terminates at Apache, not Tomcat | HTTPS on Apache; proxy to Tomcat |
| 83 | Certificate chain: root → intermediate → server; PEM vs PKCS | Create 3-level cert chain; break it; see browser error |
| 84 | Key SSL directives (SSLCertificateFile, SSLCACertificateFile…) | Config review — spot the missing chain directive |
| 85 | Two-way SSL at Apache: SSLVerifyClient, SSLVerifyDepth | Mutual SSL; test failure cases (no/wrong/expired cert) |
| 86 | Virtual hosts: name-based vs IP-based; SNI | Host 2 apps on one Apache; test both URLs |
| 87 | Redirects & headers: HTTP→HTTPS, HSTS, mod_rewrite | Rewrite rule + HSTS; verify with curl -I |
| 88 | 🏦 Banking: B2B mutual-SSL + cert-expired-Saturday story | Simulate expired cert; debug with openssl s_client; write renewal runbook |

## Module 11: Nginx (Day 89–95)
| Day | Topic | Lab |
|---|---|---|
| 89 | Nginx architecture: event-driven, workers, why it scales | Install Nginx; compare worker model with Apache under load |
| 90 | Core config: nginx.conf, http/server/location blocks | Annotate nginx.conf; nginx -t; graceful reload |
| 91 | Upstream blocks + proxy_pass — LB 2 Tomcats | Round robin; kill one; watch failover |
| 92 | Health checks (max_fails, fail_timeout), timeouts | Tune proxy timeouts; simulate slow backend |
| 93 | Rate limiting: limit_req, limit_conn | Flood with ab; observe 503s |
| 94 | Static offload + caching (proxy_cache, expires) | Serve PDFs via Nginx, APIs to Tomcat — statement scenario |
| 95 | 🏦 Banking: Nginx vs Apache deep-dive + "10k connections" | Load test both with ab; record results; write answer |

## Module 12: F5 / BIG-IP (Day 96–101)
| Day | Topic | Lab |
|---|---|---|
| 96 | F5 concepts: Virtual Server, Pool, Member, Node, SNAT | Draw F5 → Tomcat architecture from memory |
| 97 | Health monitors: TCP vs HTTP; monitor vs actual app state | Write health-check endpoint + curl-based monitor script |
| 98 | ⭐ The false-healthy problem: 200 OK but app broken | Reproduce; design /health.jsp checking DB too |
| 99 | Persistence: cookie insert, source-IP affinity | Simulate via Nginx cookie stickiness; test session survival |
| 100 | SSL offload vs passthrough; iRules (concept) | Write pros/cons doc; practice recommendation answer |
| 101 | 🏦 Banking: F5 deployments — drain, node disable, zero impact | Drain one Tomcat → deploy WAR → re-enable; zero failed requests |

## Module 13: End-to-End Traffic Flow 💰 (Day 102–105)
| Day | Topic | Lab |
|---|---|---|
| 102 | Full flow: DNS → CDN → F5 → Nginx/Apache → Tomcat → DB/Mainframe | Draw complete diagram from memory |
| 103 | Headers: X-Forwarded-For, X-Forwarded-Proto, Host at every layer | Configure XFF; verify Tomcat sees real client IP |
| 104 | Layer-by-layer slow-request diagnosis (evidence-based method) | Build diagnosis playbook: curl -w, %D, thread dump, netstat per hop |
| 105 | 🏦 Banking: fraud asks "Which customer IP did transaction X come from?" | Trace request ID across all layers; rehearse slow-Net-Banking answer, timed |

## ✅ Phase 4 Checkpoints
- Day 80: 🏦 /netbanking/* → Tomcat farm, /payments/* → second backend, with failover + "mod_jk vs proxy_ajp — which and why?" aloud
- Day 88: "Partner says SSL handshake fails — walk me through debugging."
- Day 95: "Compare Apache vs Nginx — which handles 10k concurrent connections and why?"
- Day 101: "Take a Tomcat node out of F5 with zero customer impact?"
- Day 105: FINAL — whiteboard FULL architecture + slow-request diagnosis, 15 min, no notes
