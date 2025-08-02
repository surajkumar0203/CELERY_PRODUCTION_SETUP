## Setp 1 Systemd Services (Production Setup)

### - 1. Celery Service
```sh
    nano /etc/systemd/system/PROJECT_NAME.celery.service
```

```sh
    Syntax
    [Unit]
    Description=Celery Service
    After=network.target
    Requires=redis.service

    [Service]
    user=USERNAME
    Group=USERNAME   
    WorkingDirectory=/var/www/ProjectNameFolder
    EnvironmentFile=/var/www/.env
    ExecStart=/var/www/PPROJECT_DIR/venv/bin/celery -A PROJECT_NAME worker --loglevel=info --logfile=/var/log/celery/
    Restart=always
    Type=simple

    [Install]
    WantedBy=multi-user.target

```
### - 2. Celery Beat Service
```sh
    nano /etc/systemd/system/PROJECT_NAME.celerybeat.service
```
```sh
    Syntax
    [Unit]
    Description=Celery Beat Service
    After=network.target
    Requires=PROJECT_NAME.celery.service

    [Service]
    user=USERNAME
    Group=USERNAME   
    WorkingDirectory=/var/www/ProjectNameFolder
    EnvironmentFile=/var/www/.env
    ExecStart=/var/www/ProjectNameFolder/venv/bin/celery -A PROJECT_NAME beat --loglevel=info --scheduler django_celery_beat.schedulers:DatabaseScheduler --logfile=/var/log/celery/beat.log
    Restart=always
    Type=simple

    [Install]
    WantedBy=multi-user.target

```
### find USERNAME 
```sh 
    whoami
```
### Explanation of Each Section
[Unit]

Description: Brief explanation of the service.

After=network.target: Start this service only after network is up.

Requires=redis.service: Ensures Redis (used as Celery broker) starts first.

[Service]

User / Group: System user/group under which Celery will run.

WorkingDirectory: Project directory where Django is located.

EnvironmentFile: Path to .env file containing environment variables (like DJANGO_SETTINGS_MODULE, SECRET_KEY, etc.).

ExecStart: Command to start Celery worker with logs stored in /var/log/celery/worker.log.

Restart=always: Automatically restart the worker if it crashes.

Type=simple: Assumes the service starts immediately (no fork).

[Install]

###  Create Folder for Celery Log
```sh
    sudo mkdir -p /var/log/celery
    sudo chown user:user /var/log/celery

```

### Enable & Start Services
```sh
    sudo systemctl daemon-reload
    sudo systemctl enable PROJECT_NAME.celery.service
    sudo systemctl start PROJECT_NAME.celery.service
    sudo systemctl status PROJECT_NAME.celery.service

    sudo systemctl enable PROJECT_NAME.celerybeat.service
    sudo systemctl start PROJECT_NAME.celerybeat.service
    sudo systemctl status PROJECT_NAME.celerybeat.service
    
    sudo systemctl status PROJECT_NAME.gunicorn.service


```
### Verify 
```sh
    ps aux | grep celery   # To show celery proess
    tail -f /var/log/celery/worker.log # To show worker log
    tail -f /var/log/celery/beat.log # To show beat log
```