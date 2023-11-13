# OdooNginxReverse
how to set nginx as reverse proxy for odoo16 instance


for my domain for reverse proxy i just add to /etc/nginx/conf.d/  - odoo.conf file that forward adres. other setting no need.


here is odoo.conf that work for me

vi odoo.conf


      # Odoo Upstreams
      upstream odooserver {
       server 127.0.0.1:8069;
      }
      
      # http to https redirection
      server {
          listen 80;
          server_name dehkadeh38.ir;
          return 301 https://dehkadeh38.ir$request_uri;
      }
      
      server {
          listen 443 ssl;
          server_name dehkadeh38.ir;
          access_log /var/log/nginx/odoo_access.log;
          error_log /var/log/nginx/odoo_error.log;
      
         # SSL
          ssl_certificate /etc/letsencrypt/live/dehkadeh38.ir/fullchain.pem;
          ssl_certificate_key /etc/letsencrypt/live/dehkadeh38.ir/privkey.pem;
          ssl_trusted_certificate /etc/letsencrypt/live/dehkadeh38.ir/chain.pem;
      
      
          # Proxy settings
          proxy_read_timeout 720s;
          proxy_connect_timeout 720s;
          proxy_send_timeout 720s;
          proxy_set_header X-Forwarded-Host $host;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
          proxy_set_header X-Real-IP $remote_addr;
      
          # Request for root domain
          location / {
             proxy_redirect off;
             proxy_pass http://odooserver;
          }
      
          # Cache static files
          location ~* /web/static/ {
              proxy_cache_valid 200 90m;
              proxy_buffering on;
              expires 864000;
              proxy_pass http://odooserver;
          }
      
          # Gzip Compression
          gzip_types text/css text/less text/plain text/xml application/xml application/json application/javascript;
          gzip on;
      }



