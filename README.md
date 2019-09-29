events {
  worker_connections  4096;
}

http {
  server {
    ## Listen on port 443, using HTTP2
    listen                  443 ssl http2;
    ## For testing purposes, change your /etc/hosts file to include
    ## 0.0.0.0 example.test www.example.test
    server_name             example.test;
    root                    /usr/share/nginx/html;
    ## SSL Certificates for HTTPS traffic to web server
    ssl_certificate         /etc/nginx/example.test.crt;
    ssl_certificate_key     /etc/nginx/example.test.key;
    ## Store and catch most index files
    index                   index.php index.html index.htm;
    access_log              /var/log/nginx/access.log;
    error_log               /var/log/nginx/error.log crit;

    ## Redirect any request to /{root} to /uj47G directory
    location / {
      return 301 $scheme://$server_name/uj47G;
    }
    ## Basic Authentication Protection for secure area
    location /uj47G {
      auth_basic           "Secret area";
      auth_basic_user_file /etc/nginx/.htpasswd; 
    }
  }
}


docker-compose.yaml
version: '3'
services:
  # Define proxy server, uses ./proxy/Dockerfile
  proxy:
    build:
      context: ./proxy
      dockerfile: Dockerfile
    # Mount/Attach local files to contianer files/locations
    volumes:
      - ./proxy/proxy-nginx.conf:/etc/nginx/nginx.conf
      - ./data/example.test.crt:/etc/nginx/example.test.crt
      - ./data/example.test.key:/etc/nginx/example.test.key
      - ./data/logs/access.log:/var/log/nginx/example.test/example_test_access_log
      - ./data/logs/error.log:/var/log/nginx/example.test/example.test_error_log error;
      - ./web/.htpasswd:/etc/nginx/.htpasswd
    ports:
      # Handle both HTTP/HTTPS requests
      # http://example.test => https://example.test
      # https://example.test => https://web
      - "443:443"
      - "80:80"
  # Define web server, uses ./web/Dockerfile
  web:
    build:
      context: ./web
      dockerfile: Dockerfile
    # Mount/Attach local files to contianer files/locations
    volumes:
      - ./web/web-nginx.conf:/etc/nginx/nginx.conf
      - ./site:/usr/share/nginx/html
      - ./data/example.test.crt:/etc/nginx/example.test.crt
      - ./data/example.test.key:/etc/nginx/example.test.key
      - ./web/.htpasswd:/etc/nginx/.htpasswd
      - ./data/logs/access.log:/var/log/nginx/example.test/example_test_access_log
      - ./data/logs/error.log:/var/log/nginx/example.test/example.test_error_log error;
    ports:
    ## HTTPS for Web Server, using 8443, since 443 is being used by proxy server
      - "8443:443"
    # Flow: (=> == redirect, in these notes)
    ## http://example.test => https://example.test => https://web => https://web/uj47G
    ## resulting in response to browser https://example.test/uj47G or https://example.test/uj47G/index.html