# Project Name: Java Application Deployment on Kubernetes using Jenkins and Argo CD

 Project Description: Efficiently Deploying Java-based Full-Stack Web Application. ensuring security, scalability, and high availability using DevOps tools like Jenkins, Argo CD, Terraform, Ansible, Docker, Helm, Kubernetes, Packer, Prometheus, and Grafana.

 ![Sitemap Whiteboard in Green Purple Basic Style](https://github.com/user-attachments/assets/e55b89d3-30ef-4cf5-b94a-620b8db5c0bd)

## Application Technologies

- Java
- Spring Boot
- JDBC
- H2 Database
- Thymeleaf
- HTML5
- Maven

## Application Features

- CRUD operations
- JDBC for database control
- Spring Boot framework
- Mapping HTTP requests to appropriate HTML pages on the controller
- Sharing model attributes between the controller and HTML files using Thymeleaf
- Customizing schema using schema.sql file
- Inserting initial data using data.sql file

## How to Run Application via Docker

 build the docker image
   ```
   docker build -t shamshuddin03/spymission:2.1.2 .
   ```
Run the docker container
```
  docker run -d --name spy -p 8080:8080 spymission:2.1.2
```
