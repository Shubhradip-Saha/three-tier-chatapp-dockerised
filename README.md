# three-tier-chatapp-dockerised
Containerised the Django-based three-tier chat application. The repository contains the necessary Docker files and he related configurations to start the application.
The file structure is as follows ---->
.
├── backend/
│   ├── Dockerfile
│   ├── .env
├── frontend/
│   ├── Dockerfile
│   ├── default.conf
├── database/
│   ├── Dockerfile
│   ├── .env
│   ├── init.sql
│   └── database.cnf

1. Database Setup (MySQL)
1.1 Create .env file inside /database/
MYSQL_ROOT_PASSWORD=val
MYSQL_DATABASE=val
MYSQL_USER=val
MYSQL_PASSWORD=val

1.2 Build Docker Image
docker build -t chatapp-database:latest -f database/Dockerfile_database ./database

1.3 Create Docker Volume
docker volume create chatapp_db_data (only for creating persistent pace for database files)

1.4 Create Docker Network
docker network create chatapp-network

1.5 Run Database Container
docker run -d \
  --name chatapp-db \
  --network chatapp-network \
  --env-file database/.env \
  -v chatapp_db_data:/var/lib/mysql \
  -v $(pwd)/database/init.sql:/docker-entrypoint-initdb.d/init.sql \
  -v $(pwd)/database/database.cnf:/etc/mysql/conf.d/database.cnf \
  chatapp-database:latest



2. Backend Setup (Django-server)
   
2.1 Create .env file inside /backend/
DB_NAME= Name of the database created above 
DB_USER= Database user which th backend will user to log in to the specified db 
DB_PASSWORD= use the same secret
DB_HOST= database container name 
DB_PORT=3306

2.2 Build Docker Image
docker build -t chatapp-backend:latest -f backend/Dockerfile_backend ./backend

2.3 Run Backend Container
docker run -d \
  --name chatapp-backend \
  --network chatapp-network \
  --env-file backend/.env \
  -p 8000:8000 \
  chatapp-backend:latest
  
3. Frontend Setup (Nginx)
   
3.1 Build Docker Image
docker build -t chatapp-frontend:latest -f frontend/Dockerfile_frontend ./frontend

3.2 Run Frontend Container
docker run -d \
  --name chatapp-frontend \
  --network chatapp-network \
  -p 80:80 \
  chatapp-frontend:latest


Additional notes:
Create separate .env files for backend and database.
Use the same chatapp-network or custom bridge network of choice, but all containers should be in the same network to be able to communicate.
Expose the Frontend such that it could be port-mapped to port 80 of localhost/hostvm. This enables the traffic coming to the host port 80 to be redirected to the nginx container.
May expose the backend ports if there are more number of backend containers in the same or different host machines and need to use nginx as a load balancer to distribute the traffic between the containers evenly.
May not expose the 3306 mysql port.

Once the application is live, it could be accessed using the docker-host vm IP.



