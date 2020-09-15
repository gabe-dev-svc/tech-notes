# Service Authentication and Authorization

## Password Grant Flow
[OAuth 2.0 Password Grant Type](https://oauth.net/2/grant-types/password/)
[Super useful Okta article](https://www.oauth.com/oauth2-servers/access-tokens/password-grant/)

- Application exchanges user's credentials for an access token. Access token request contains the following parameters:
  - `grant_type` must be set to `password`
  - `username` 
  - `password`
  - `scope` (optional)
  - Client Authentication (can be a `client_secret` or `client_id`)
 
## Using nginx to add auth to any application

[Use nginx to Add Authentication to Any Application \| Okta Developer](https://developer.okta.com/blog/2018/08/28/nginx-auth-request)

- `auth_request` module forwards requests to a separate service to check user authentication (an auth proxy)

> Plain Jane nginx config
```nginx
server {
  listen 443 ssl http2;
  server_name stats.avocado.lol;

  ssl_certificate /etc/letsencrypt/live/avocado.lol/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/avocado.lol/privkey.pem;

  root /web/sites/stats.avocado.lol;

  index index.html;
}
```

>adding `auth_request`
```nginx
server {
  listen 443 ssl http2;
  server_name stats.avocado.lol;

  ssl_certificate /etc/letsencrypt/live/avocado.lol/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/avocado.lol/privkey.pem;

  root /web/sites/stats.avocado.lol;

  index index.html;
  
  # Any request to this server will first be sent to this URL
  auth_request /vouch-validate;

  location = /vouch-validate {
    # This address is where Vouch will be listening on
    proxy_pass http://127.0.0.1:9090/validate;
    proxy_pass_request_body off; # no need to send the POST body

    proxy_set_header Content-Length "";
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;

    # these return values are passed to the @error401 call
    auth_request_set $auth_resp_jwt $upstream_http_x_vouch_jwt;
    auth_request_set $auth_resp_err $upstream_http_x_vouch_err;
    auth_request_set $auth_resp_failcount $upstream_http_x_vouch_failcount;
  }

  error_page 401 = @error401;

  # If the user is not logged in, redirect them to Vouch's login URL
  location @error401 {
    return 302 https://login.avocado.lol/login?url=https://$http_host$request_uri&vouch-failcount=$auth_resp_failcount&X-Vouch-Token=$auth_resp_jwt&error=$auth_resp_err;
  }
}
```

- Two server configurations are required. One for the web server and one for the authentication server. 
- The snippet below sets up a reverse proxy for what would be a Vouch server
```nginx
server {
  listen 443 ssl;
  server_name login.avocado.lol;

  ssl_certificate /etc/letsencrypt/live/login.avocado.lol/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/login.avocado.lol/privkey.pem;

  # Proxy to your Vouch instance
  location / {
    proxy_set_header  Host  login.avocado.lol;
    proxy_set_header  X-Forwarded-Proto https;
    proxy_pass        http://127.0.0.1:9090;
  }
}
```