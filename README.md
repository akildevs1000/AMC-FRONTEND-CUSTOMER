nuxt app installation

pre requisite

configure nginx file using below code snippet

server {

	server_name your.subdomain.com;

    location / {
        proxy_pass         http://139.59.69.241:3335; // your ip and port
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme; 

    }
}

install ssl => sudo certbot --nginx -d your.domain.com






git clone repo_name at your desired location
cd repo_name 
npm i
configure env
npm run build
pm2 start npm --name "AMC CUSTOMER" -- start
pm2 start npm --name "AMC TECHNICIAN" -- start
