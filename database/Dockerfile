FROM mysql:8.0.42


COPY database.cnf /etc/mysql/conf.d/database.cnf

COPY init.sql /docker-entrypoint-initdb.d/init.sql

# Expose MySQL default port
EXPOSE 3306

