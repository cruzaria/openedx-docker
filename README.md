# [WIP] Dockerizing edX and build virtual private network

## Preambule - create 9 virtual machines that have installed [docker-compose](https://docs.docker.com/compose/) app1,(app2),...,(app9) 

How does this look on schema:

![Schema](oie_1NUVmqAXcQqW.png)

## Step1 - build app1 (app2)(app3)

Installation for all other components please search on [Cruzaria](https://github.com/cruzaria/)

## Lauch a production stack
The production stack is sufficient for a minimal production deployment of Open edX.

Prepare build:

    make directories

Build and run:

    docker-compose build # go get a coffee
    docker-compose up

The LMS will be reachable at http://localhost:8000.

The CMS will be reachable at http://localhost:8080.

For local development, you should point to http://localhost:8800.

On the first run you will need to migrate the database and collect static assets:

    make migrate
    make assets

To import the Open edX demo course, cloning from https://github.com/cruzaria/courses to upload dir and run:

    make import-demo-course

To daemonize:

    docker-compose up -d

## Development

Open a bash in the lms:

    docker-compose run lms bash

Open a python shell in the lms or the cms:

    make lms-shell
    make cms-shell

## Step2 - build App4-6 (MariaDB)

Just install galera for this

    docker rm -f galera && docker run -d --name galera -e SERVICE_4444_IGNORE=true -e SERVICE_4567_IGNORE=true -e SERVICE_4568_IGNORE=true -e SERVICE_3306_NAME=db -e SERVICE_3306_TAGS=1 -e SERVICE_3306_CHECK_TCP=true -e WSREP_NODE_ADDRESS=10.0.0.6 -e WSREP_CLUSTER_ADDRESS=gcomm://10.0.0.6:4567,10.0.0.10:4567,10.0.0.11:4567 -e MYSQL_ROOT_PASSWORD=password -p 3306:3306 -p 4567:4567/udp -p 4567-4568:4567-4568 -p 4444:4444 -v /mnt/data/galera.service/mysql:/var/lib/mysql:Z panubo/mariadb-galera mysqld

end enroll

    echo "select sc.id, sc.course_id, sc.created, au.username, au.first_name, au.last_name, au.email from student_courseenrollment as sc inner join auth_user as au on sc.user_id = au.id;" | mysql openedx -u root -ppassword

## Step3 - build App7-9 (mongo)

    docker rm -f mongo && docker run --name mongo -e SERVICE_27017_NAME=mongo -e SERVICE_27017_TAGS=3 -e SERVICE_27017_CHECK_TCP=true -v /mongofiles/data:/data/db -v /mongokey:/opt/keyfile --hostname="mongo3" --add-host mongo1:10.0.0.7 --add-host mongo2:10.0.0.12 --add-host mongo3:10.0.0.13 -p 27017:27017 -d mongo --smallfiles --keyFile /opt/keyfile/mongodb-keyfile --replSet "rs0"


## Last Step4 - smtp

we use docker smtp from namshi (he like renamed here but you can find it)

configure relay with this params  

  "EMAIL_HOST": "smtp",
  "EMAIL_PORT": 9025,

## Need Russian hosting for edX?

[hosting edX 100$/m](http://code.eletsjob.ru)

for sellers:
[email](mailto:vvvvvvvvvv@inbox.ru)
[fb](https://www.facebook.com/andropovvv)
