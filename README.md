# Oracle Enterprise Edition 11g Release 2

Oracle Enterprise Edition 11g Release 2 on Oracle Linux

This **Dockerfile** is a [trusted build](https://registry.hub.docker.com/u/sath89/oracle-ee-11g/) of [Docker Registry](https://registry.hub.docker.com/).

### Installation

Create a docker image

Run:

    docker pull sath89/oracle-ee-11g

Create a docker container with one of the following commands

Run with 8080 and 1521 ports opened:

    docker run -d -p 8080:8080 -p 1521:1521 sath89/oracle-ee-11g

Run with data on host and reuse it:

    docker run -d -p 8080:8080 -p 1521:1521 -v /my/oracle/data:/u01/app/oracle sath89/oracle-ee-11g

Run with Custom DBCA_TOTAL_MEMORY (in Mb):

    docker run -d -p 8080:8080 -p 1521:1521 -v /my/oracle/data:/u01/app/oracle -e DBCA_TOTAL_MEMORY=1024 sath89/oracle-11g

The above commands will return a docker container id. To know how the execution is going, you must run the following command with the returned id.

Run:

    docker logs -f <docker_container_id>

![Oracle DB creation log](https://github.com/ArmandoAv/docker_oracle_ee_11g/blob/main/images/Container_Log.png)

When in the log you can see:

    Import finished

    Database ready to use. Enjoy!

The following commands must be executed

Run:

    docker exec -it <docker_container_id> /bin/bash
    su -oracle

Connect database with following setting:

    hostname: localhost
    port: 1521
    sid: EE
    service name: EE.oracle.docker
    username: system
    password: oracle

To connect using sqlplus whit system or sysdba user:

<pre>
sqlplus system/oracle@//localhost:1521/EE.oracle.docker
sqlplus / as sysdba
</pre>

Password for SYS & SYSTEM:

    oracle

Once connected to the DB, the following commands are executed to format the data obtained from the queries:

    SET LINESIZE 1800
    SET PAGESIZE 30

![Connection to the Oracle DB](https://github.com/ArmandoAv/docker_oracle_ee_11g/blob/main/images/Container_Execution.png)

To exit the SQL console execute the following command:

    quit

To exit the Oracle user console and the docker container console, run the following command twice:

    exit

Connect to Oracle Enterprise Management console with following settings:

    http://localhost:8080/em
    user: sys
    password: oracle
    connect as sysdba: true

By Default web management console is enabled. To disable add env variable:

    docker run -d -e WEB_CONSOLE=false -p 1521:1521 -v /my/oracle/data:/u01/app/oracle sath89/oracle-11g
    #You can Enable/Disable it on any time

Start with additional init scripts or dumps:

    docker run -d -p 1521:1521 -v /my/oracle/data:/u01/app/oracle -v /my/oracle/init/SCRIPTSorSQL:docker-entrypoint-initdb.d sath89/oracle-11g

By default Import from `docker-entrypoint-initdb.d` enabled only if you are initializing database(1st run). If you need to run import at any case - add `-e IMPORT_FROM_VOLUME=true`
**In case of using DMP imports dump file should be named like ${IMPORT_SCHEME_NAME}.dmp**
**User credentials for imports are ${IMPORT_SCHEME_NAME}/${IMPORT_SCHEME_NAME}**

**TODO LIST**

- Web management console HTTPS port
- Add functionality to run custom scripts on startup, for example User creation
- Add Parameter that would setup processes amount for database (Currently by default processes=300)
- Spike with clustering support
- Spike with DB migration from 11g
