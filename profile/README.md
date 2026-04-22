The following are the links that we will refer to in the training 

| __  | Link | Comments |
| ------------- | ------------- | ------------- |
| Google DOcument | https://docs.google.com/document/d/1tOV10GOfFnuqOcF7ZMjbqZECaTSCvUuFgg-IyTt9RkQ/edit?usp=sharing | |
| Project Setup | https://github.com/raghudevopsb88/wealth-project/blob/main/docs/00-overview.md | |
| WMP App Repos | https://github.com/orgs/wmp-v1/repositories | Fork these |



## Disk extention commands.

```
growpart /dev/nvme0n1 4 
lvextend -r -L +5GB /dev/mapper/RootVG-homeVol
```

## Load Test Commands.
```
docker rm -f wmp-load
docker pull docker.io/rkalluru/wmp-load:latest
docker run -d -p 5000:5000 --name wmp-load docker.io/rkalluru/wmp-load:latest
```

## Logstash Config.
```
input {
  beats {
    port => 5044
  }
}

filter {
  if [kubernetes][container][name] == "frontend" {
  grok {
    match => { "message" => "%{IP:ip}%{SPACE}%{HTTPDATE:date}%{SPACE}%{WORD:http_method}%{SPACE}%{PATH:http_path}%{SPACE}%{WORD}/%{NUMBER}%{SPACE}%{NUMBER:http_code}%{SPACE}%{NUMBER:response_time}%{SPACE}%{NUMBER:response_size}%{SPACE}%{QUOTEDSTRING:http_referer}%{SPACE}%{QUOTEDSTRING:http_user_agent}%{SPACE}%{QUOTEDSTRING:http_x_forwarded_for}" }
  }
 } else {
  drop { }
 }
}

output {
  elasticsearch {
    hosts => ["https://localhost:9200"]
    ssl_verification_mode => "none"
    user => "elastic"
    password => "eD9mWinX-tBjgyIacNJe"
    index => "%{[kubernetes][container][name]}"
  }
}
```
