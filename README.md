# Caddy accesslog: custom decoders for Wazuh SIEM
This simple decoders allow Wazuh to process Caddy access logs using built-in web rules, which is not possible with JSON decoder


## Requirements:
— You should enable access logging to the default Caddy logger
https://caddyserver.com/docs/caddyfile/directives/log#examples

```
example.com {
    log {
        output file /var/log/access.log
        format json
    }
}
```

— You should add an application tag to the logs, e.g. for rsyslog:
```
Tag="Caddy:"
```

## Examples:
Full event:
```
Jul 22 22:22:22 Caddy Caddy: {"level":"info","ts":1721733720.947426,"logger":"http.log.access.log0","msg":"handled request","request":{"remote_ip":"88.66.66.66","remote_port":"66666","client_ip":"11.123.33.45","proto":"HTTP/2.0","method":"GET","host":"caddy.site.com","uri":"/favicon.ico","headers":{"Sec-Fetch-Dest":["image"],"Referer":["https://caddy.site.com/"],"Accept-Encoding":["gzip, deflate, br"],"Accept-Language":["ru,en;q=0.9,en-GB;q=0.8,en-US;q=0.7"],"Priority":["u=1, i"],"Sec-Ch-Ua":["\"Not/A)Brand\";v=\"8\", \"Chromium\";v=\"126\", \"Microsoft Edge\";v=\"126\""],"User-Agent":["Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36 Edg/126.0.0.0"],"Accept":["image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8"],"Sec-Fetch-Mode":["no-cors"],"Sec-Ch-Ua-Mobile":["?0"],"Sec-Ch-Ua-Platform":["\"Windows\""],"Sec-Fetch-Site":["same-origin"]},"tls":{"resumed":false,"version":123,"cipher_suite":4444,"proto":"h2","server_name":"caddy.site.com"}},"bytes_read":0,"user_id":"","duration":0.000017,"size":0,"status":200,"resp_headers":{"Server":["Caddy"],"Alt-Svc":["h3=\":443\"; ma=2592000"]}}
```
wazuh-logtest results:
```
**Phase 1: Completed pre-decoding.
        timestamp: 'Jul 22 22:22:22'
        hostname: 'Caddy'
        program_name: 'Caddy'

**Phase 2: Completed decoding.
        name: 'Caddy'
        client_ip: '11.123.33.45'
        dsthost: 'caddy.site.com'
        duration: '0.000017'
        id: '200'
        method: 'GET'
        protocol: 'HTTP/2.0'
        referer: 'https://caddy.site.com/'
        size: '0'
        srcip: '88.66.66.66'
        srcport: '66666'
        srcuser: ''
        url: '/favicon.ico'
        user_agent: 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36 Edg/126.0.0.0'

**Phase 3: Completed filtering (rules).
        id: '31108'
        level: '0'
        description: 'Ignored URLs (simple queries).'
        groups: '['web', 'accesslog']'
        firedtimes: '1'
        mail: 'False'
```
