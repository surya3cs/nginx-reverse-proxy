For this example, data base server acts as the backend origin server. Apache Tomcat will handle requests on the following URL:
this dasa base i have already configured on task 1
http://18.204.89.75:30002

i do not want clients to connect to the backend server on port 30002. Instead, i want all requests to first go through the Nginx server on the following URL:

Nginx will act as a reverse proxy, forwarding all requests that it receives with the context root / to the corresponding application running on d.ata base server

To proxy requests, Nginx must add a new location entry to the nginx.conf file that maps the / URI to the backend server through a proxy_pass setting.

Nginx Docker reverse proxy config file

server {
    listen 80;
    
    location / {
        proxy_pass http://18.204.89.75:30002/;
    }
}

Copy the Nginx config file back to Docker using docker file

FROM nginx:latest
RUN rm /etc/nginx/conf.d/*
COPY nginx.conf /etc/nginx/conf.d

To spin up the docker container i used docker-compose yaml
version: "3.2"
services:
  nginx:
    build: 
      context: .
    image: nginx-reverse-proxy
    
    
    container_name: nginx-reverse-proxy-container
    
    ports:
      - "80:80"
      
now try to access the application without port number:http://18.204.89.75

This setup i done throuh again below jenkins declartive pipeline

pipeline {
    agent any
    
tools {
    dockerTool 'Docker'
}

    stages {
        stage('git') {
            steps {
                git branch: 'main', url: 'https://github.com/surya3cs/nginx-reverse-proxy.git'
            }
        }
        
        stage('ngnix-reverse-proxy') {
            steps {
               
                sh 'sudo docker-compose up -d'
            }
        }
        
    }
}


T
