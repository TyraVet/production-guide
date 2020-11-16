<p align="center">
    <span class="badge-license">
        <img
        src="https://img.shields.io/github/license/Andrsrz/tyra-web?style=for-the-badge"
        alt="License" />
    </span>
    <span class="badge-buymeacoffee">
        <a href="https://www.buymeacoffee.com/andrsrz"
        title="Donate using Buy Me A Coffee">
        <img
        src="https://img.shields.io/static/v1?label=Buy%20Me%20a%20Beer&message=donate&color=FF813F&style=for-the-badge&logo=buy-me-a-coffee"
        alt="Buy Me A Coffee donate button" />
        </a>
    </span>
</p>

# TyraWeb Production Guide

## VueJS app and NodeJS/Express API in same machine.

We're goint to use [Nginx](https://www.nginx.com/) to serve our software into
the world.
We decided to go with Nginx because it can be used as a reverse proxy. In this
way we have our static website and also the API on another port in the same
machine. So before anything install Nginx in your system.

### 1. Set your static website files
1.1 Setup and Build [TyraWeb VueJS Application](https://github.com/tyra-web/tyra-web).
1.2 Create directory ```/var/www/yourdomain.com```.
1.3 Move the static website files from the build into that folder.

### 2. Set API
2.1 Setup and Run [TyraWeb NodeJS/Express API](https://github.com/tyra-web/tyra-web-api).

### 3. Create SSL Certificate
You can follow up this [tutorial](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04).

### 4. Create ```/etc/nginx/sties-available/yourdoamin.com```
``` sh
server {
    listen 443 default_server;
    listen [::]:443 default_server;
    
    ssl on;
    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
    
    root /var/www/yourdomain.com;
    index index.html;
    
    location / {
        try_files $uri $uri/ = 404;
    }
    
    location /api {
        proxy_http_version 1.1;
        proxy_cache_bypass $http_upgrade;
        
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $stream_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        proxy_pass http://localhost:3000;
    }
}

server {
    listen 80;
    server_name yourdomain.com;
    rewrite ^ https://$host$request_uri? permanent;
}
```

## Contributing
If you find this project usefull for your professional life, consider
making a donation.

## License
[MIT](https://mit-license.org/)
