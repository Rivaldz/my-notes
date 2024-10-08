#Check docker service
1. sudo systemctl status docker (check active service)
2. docker ps -a (check all container active)

#Delete Images Container
docker rmi -f <key image> 
docker rm -f <key container> 

#postgres image
1. Create folder for mount 
mkdir -p /path/to/postgres-data

2. Create Dockerfile
# Use image PostgreSQL official as base image
FROM postgres:latest

# Set environment variables for user, password, and database
ENV POSTGRES_USER=myuser
ENV POSTGRES_PASSWORD=mypassword
ENV POSTGRES_DB=mydatabase

# Expose port 5432 for PostgreSQL
EXPOSE 5432

3. Build Docker 
docker build -t my-postgres .

4. Running docker
docker exec -it my-postgres psql -U myuser -d mydatabase

5. Access postgres on other app
$dsn = "pgsql:host=my-postgres(name container);port=5432;dbname=mydatabase;user=myuser;password=mypassword";

    RUN apt-get install -y libpq-dev \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql
