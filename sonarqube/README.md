# Sonarqube
## Reference: 
### Dockerfile: https://github.com/SonarSource/docker-sonarqube/blob/master/10/community/Dockerfile
### Sonarqube Config: https://gist.github.com/marek-panek/8b5fec0ad623926b110267e0b7d66559




#### Sonarqube Configuration
1. In PostgreSQL database

  1.1 Create user
      ```create user <user_name> with password '<password>';```
      
  1.2 Create database
      ```create database <db_name> with owner <user_name> encoding 'UTF8';```

2. Edit <sonar_install_dir>/conf/sonar.properties, usually this file is in /opt/sonarqube/conf/
  ```
  sonar.jdbc.username=sonarqubeadmin
  sonar.jdbc.password=<password>
  sonar.jdbc.url=jdbc:postgresql://sonardb/sonarqubedb
  sonar.web.host=0.0.0.0
  ```

  # Web context. When set, it must start with forward slash (for example /sonarqube).
  sonar.web.context=/sonarqube
  # TCP port for incoming HTTP connections. Default value is 9000, you can change this accordingly
  sonar.web.port=9000
