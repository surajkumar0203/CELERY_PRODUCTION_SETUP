### Some Important Command/Step
```sh
sudo visudo
```

## Add this line

```sh
# Add this line to allow the user to run the command without entering the sudo password.

USERNAME ALL=NOPASSWD: /usr/bin/systemctl restart PROJECTNAME.celery.service, /usr/bin/systemctl restart PROJECTNAME.celerybeat.service
```
## Replace 
-- USERNAME → your actual Linux user
-- PROJECTNAME → your actual project name
## Why /bin/systemctl instead of /usr/bin/systemctl
```sh
# Run this to check correct path:
which systemctl
```


# Change accouding to the project

## .github/workflows/NAME.yml
```sh
name: Django_deploy

# Trigger the workflow on push and
# pull request events on the master branch
on:
  push:
    branches: ["master"]
  pull_request:
    branches: ["master"]

# Authenticate to the the server via ssh
# and run our deployment script
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy Django project to Server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          port: ${{ secrets.PORT }}
          key: ${{ secrets.SSHKEY }}
          script: "cd /var/www/HMSB && ./.scripts/django_deploy.sh"
```

## path ./scripts/Name.sh
```sh
#!/bin/bash
set -e

echo "Deployment started ..."

# Pull the latest version of the app
echo "Copying New changes...."
GIT_SSH_COMMAND="ssh -i ~/.ssh/<PRIVATE KEY>" git pull

echo "New changes copied to server !"

# Activate Virtual Env
source venv/bin/activate

echo "Installing Dependencies..."
pip install -r requirements.txt --no-input


echo "Go To HospitalManagementSystem"
cd HospitalManagementSystem
echo "Clearing Cache..."

python manage.py clean_pyc
python manage.py clear_cache

echo "Serving Static Files..."
python manage.py collectstatic --noinput

echo "Running Database migration..."
python manage.py makemigrations
python manage.py migrate

# Deactivate Virtual Env
deactivate
echo "Virtual env 'venv' Deactivated !"

echo "Reloading App..."
echo "Reload celery"
sudo systemctl restart api.hospitalmidicare.online.celery.service
echo "Reload celerybeat"
sudo systemctl restart api.hospitalmidicare.online.celerybeat.service
echo "Reload gunicorn"
ps aux |grep gunicorn |grep HMSB | awk '{print $2}' | xargs kill -HUP

echo "Deployment Finished !"

 
```