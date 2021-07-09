---
title: "\U0001F4BB Deploying code-server"
---

some six months ago, GitHub recommended me the repository [code-server](https://github.com/cdr/code-server). it described itself as:

> VS Code in the browser

it seemed, in every aspect, worth a deploy; so I tried, following the documentatio, on my ubuntu 20 vps. the procedures below are also tested and evidently worked on a CentOS 7 machine. as for other operating systems, please go to [the official documentation](https://coder.com/docs/code-server/v3.10.2/guide)
## Getting Started

As a front-end developer, I can hardly say I know anything about servers. Therefore I dry-runned the install script lest slight mishaps:

```
curl -fsSL https://code-server.dev/install.sh | sh -s -- --dry-run
```

and everything seemed, absolutely fine to me, so I ran the install script:

```
curl -fsSL https://code-server.dev/install.sh | sh
```

the other way to do this is:

```
tar xzvf code-server-3.4.1-linux-x86_64.tar.gz && mv code-server-3.4.1-linux-x86_64 code-server
```

run, after one of the two aforementioned methods are done:

```
vim ~/.config/code-server/config.yaml
```

entering editing mode by hitting the `i` key, change the content of the file to below:

```
bind-addr: 0.0.0.0:{desired_port}
auth: password
password: {desired_password}
cert: false
```

now hit `esc` and enter `:wq` which will be shown in the bottom of your command line.

by now, everything is set up and you can simply run the programme by `~/code-server/bin/code-server`, and visiting it by entering `{your_ip}:{desired_port}` in your browser.

## Advanced

### Background process

create a background process file using:

```
vim /etc/systemd/system/code-server.service
```

whereafter pasting the content below:

```
[Unit]
Description=code servier
After=network.target

[Service]
Type=simple
User=root
Restart=on-failure
RestartSec=20s
Environment=
ExecStart=/root/code-server/bin/code-server
ExecReload=/root/code-server/bin/code-server

[Install]
WantedBy=multi-user.target
```

into the file. now set this service to run on boot via the command:

```
systemctl enable code-server
```

more about the `systemctl` command can be found [here](https://man7.org/linux/man-pages/man1/systemctl.1.html).

### Reverse Proxy

this is the Nginx file for your reverse proxy configuration:

```
#PROXY-START/

  location / { # Or / if hosting at the root.
      proxy_pass http://localhost:8080/;
      proxy_set_header Host $host;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection upgrade;
      proxy_set_header Accept-Encoding gzip;
  }

#PROXY-END/
```

and this your site configuration:

```
server
{
    listen 80;
    server_name {domain};
    index index.php index.html index.htm default.php default.htm default.html;
    root /www/wwwroot/{domain};
    
    #SSL-START
    #error_page 404/404.html;
    #SSL-END
    
    #ERROR-PAGE-START 
    #error_page 404 /404.html;
    #error_page 502 /502.html;
    #ERROR-PAGE-END
    
    #PHP-INFO-START
    #CLEAR-CACHE

    location ~ /purge(/.*) {
        proxy_cache_purge cache_one $host$1$is_args$args;
        #access_log  /www/wwwlogs/{domain}_purge_cache.log;
    }
	#REVERSE-PROXY
	include /www/server/panel/vhost/nginx/proxy/{domain}/*.conf;

	include enable-php-00.conf;
    #PHP-INFO-END
    
    #REWRITE-START
    include /www/server/panel/vhost/rewrite/{domain}.conf;
    #REWRITE-END
    
    location ~ ^/(\.user.ini|\.htaccess|\.git|\.svn|\.project|LICENSE|README.md)
    {
        return 404;
    }
        
    access_log  /www/wwwlogs/{domain}.log;
    error_log  /www/wwwlogs/{domain}.error.log;
}
```

### Force HTTPS

[Cloudflare](https://www.cloudflare.com/) is always a good choice.
