### To check the status of the PostgreSQL service on your Linux  Server
```sh
sudo systemctl status postgresql
```

### Start PostgreSQL (if stopped)
```sh
sudo systemctl start postgresql
```
### Enable PostgreSQL to start on boot
```sh
sudo systemctl enable postgresql
```
## Confirm PostgreSQL is set to listen on localhost
### Open the PostgreSQL config file

```sh 
sudo nano /etc/postgresql/VERSION/main/postgresql.conf
```

### find  Connection Settings -
```sh
listen_addresses = '127.0.0.1'  # Or use '*' to allow all 
port = 5432   # Default port (change only if needed) 
``` 
### UFW firewall (if needed)
```sh
sudo ufw allow 5432/tcp # Replace 5432 with custom port if changed
```

## Configure in pg_hba.conf
```sh
# Database administrative login by Unix domain socket
local   all             postgres                                peer

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            scram-sha-256
host    replication     all             ::1/128                 scram-sha-256

```   

```sh 
sudo nano /etc/postgresql/<VERSION>/main/postgresql.conf # e.g., 15, 14 ,16 etc
```


### restart PostgreSQL
```sh
sudo systemctl restart postgresql
```


###  Check which port PostgreSQL is listening on
```sh
sudo ss -ltnp | grep postgres
```

### sudo -u postgres psql
This command is used to log into the PostgreSQL command-line interface (psql) as the postgres user.

### What is postgres?
postgres is the default superuser account in PostgreSQL.It has full access to all databases and roles.This user is usually created when PostgreSQL is installed.

### What is psql?
psql is the PostgreSQL command-line client.
It lets you interact with the PostgreSQL database server using SQL commands.



# Some Importent Command
### Open PostgreSQL shell 
```sh
sudo -u postgres psql
```
### Create DataBase
```sh
CREATE DATABASE <DATA_BASE_NAME>;
```

### Create user 
```sh
CREATE USER <USERNAME> WITH PASSWORD 'PASSWORD';
```
### GRANT permission 
```sh
GRANT ALL PRIVILEGES ON DATABASE <DATA_BASE_NAME> TO <USERNAME>;
```

# Connect to PostgreSQL
```sh
psql -U your_username -d your_database

psql

\l            -- List all databases
\c dbname     -- Connect to database
\dt           -- Show tables in current database
\d tablename  -- Show table structure
	
ALTER DATABASE oldname RENAME TO newname; # Rename Database
ALTER DATABASE your_database OWNER TO newuser; #  Change Owner of Database
```


# Change Password of an Existing PostgreSQL User
### Step 1: Login to PostgreSQL
```sh
sudo -u postgres psql
```
### step 2 : Change Password
```sh
ALTER USER hospital_db WITH PASSWORD 'your_new_password';
```