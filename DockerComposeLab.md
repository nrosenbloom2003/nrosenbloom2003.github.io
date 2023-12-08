# WordPress using Docker Compose
This is a short guide to creating a Docker container and opening WordPress from it.
## Step 1: Install Docker
1. The first step to this process is to install Docker first. This lab used DigitalOcean to create the VM and the Docker container on it.
## Step 2: Docker Compose
1. In the VM, type `mkdir wordpress` to create a directory for the project.
2. Next, change into the new directory with `cd wordpress`.
3. Now we need to create the `docker-compose.yml` file for WordPress. Type in `nano docker-compose.yml` to create and then open the file to edit it.
4. Once you are in the `docker-compose.yml` file, copy this code and paste it into the file by right clicking in the terminal:
   ```services:
  db:
    # We use a mariadb image which supports both amd64 & arm64 architecture
    image: mariadb:10.6.4-focal
    # If you really want to use MySQL, uncomment the following line
    #image: mysql:8.0.27
    command: '--default-authentication-plugin=mysql_native_password'
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=somewordpress
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wordpress
      - MYSQL_PASSWORD=wordpress
    expose:
      - 3306
      - 33060
  wordpress:
    image: wordpress:latest
    volumes:
      - wp_data:/var/www/html
    ports:
      - 80:80
    restart: always
    environment:
      - WORDPRESS_DB_HOST=db
      - WORDPRESS_DB_USER=wordpress
      - WORDPRESS_DB_PASSWORD=wordpress
      - WORDPRESS_DB_NAME=wordpress
volumes:
  db_data:
  wp_data:```
5. Save the changes to the file by pressing CTRL-X, then Y, then ENTER.
6. Next, type `docker compose up -d` to run docker compose.
WordPress example:
