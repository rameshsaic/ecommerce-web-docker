# ecommerce-web-docker


# eCommerce Project Setup

This repository provides instructions and Dockerfiles for setting up a simple eCommerce project using Docker containers.

## Step 1: Install Docker

### Redhat or Centos:

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce
sudo systemctl start docker
sudo systemctl enable docker
```

### Ubuntu:

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
sudo systemctl start docker
sudo systemctl enable docker
```

Verify Docker installation:

```bash
sudo docker --version
```

## Step 2: Setup Backend DATABASE Container

Pull the MariaDB image:

```bash
docker pull mariadb:10.6
```

Run a MariaDB container with the desired configuration:

```bash
docker run -d -p 3306:3306 --name backend-container --hostname my-db-container \
  -e MYSQL_ROOT_PASSWORD=root-password -e MYSQL_DATABASE=ecomdb \
  -e MYSQL_USER=ecomuser -e MYSQL_PASSWORD=ecompassword \
  -e DB_HOST=my-db-container mariadb:10.6
```

Check if the container is running:

```bash
docker ps
```

Access the MariaDB container's shell:

```bash
docker exec -it <container-ID> bash
```

Login to MariaDB and create a database and table:

```sql
mysql -h my-db-container -u ecomuser -p
Enter password: ecompassword

MariaDB [(none)]> use ecomdb;
MariaDB [ecomdb]> CREATE TABLE products (
  id mediumint(8) unsigned NOT NULL auto_increment,
  Name varchar(255) default NULL,
  Price varchar(255) default NULL,
  ImageUrl varchar(255) default NULL,
  PRIMARY KEY (id)
) AUTO_INCREMENT=1;
MariaDB [ecomdb]> INSERT INTO products (Name, Price, ImageUrl) VALUES
  ("Laptop", "100", "c-1.png"),
  ("Drone", "200", "c-2.png"),
  ("VR", "300", "c-3.png"),
  ("Tablet", "50", "c-5.png"),
  ("Watch", "90", "c-6.png"),
  ("Phone Covers", "20", "c-7.png"),
  ("Phone", "80", "c-8.png"),
  ("Laptop", "150", "c-4.png");
MariaDB [ecomdb]> exit
```

## Step 3: Setup Front-end WEB Container

Clone the front-end project and build the Docker image:

```bash
mkdir /tmp/ecommerce-project
git clone https://github.com/rameshsaic/ecommerce-web-docker.git/web-dockerfile/ /tmp/ecommerce-project/
cd /tmp/ecommerce-project/
chmod +x dockerfile
docker build --no-cache -t front-end-ecommerce-image .
```

Check the Docker images:

```bash
docker images
```

Run the front-end container and link it to the backend container:

```bash
docker run -d -p 8080:80 --name frontend-container --link backend-container:backend-container front-end-ecommerce-image
```

Check if the front-end container is running:

```bash
docker ps
```

## Step 4: Verify the Setup

Access the application using your server's public IP or local IP (within the network) with port 8080:

[http://localhost:8080](http://localhost:8080)
```

