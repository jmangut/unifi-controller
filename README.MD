[![unifi-network-application](https://raw.githubusercontent.com/linuxserver/docker-templates/master/linuxserver.io/img/unifi-banner.png)](https://ui.com/)


## Application Setup

### Setting Up Your External Database

Starting with version 8.1 of Unifi Network Application, mongodb 3.6 through 7.0 are supported.

**Make sure you pin your database image version and do not use `latest`, as mongodb does not support automatic upgrades between major versions.**

**MongoDB >4.4 on X86_64 Hardware needs a CPU with AVX support. Some lower end Intel CPU models like Celeron and Pentium (before Tiger-Lake) more Details: [Advanced Vector Extensions - Wikipedia](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX) don't support AVX, but you can still use MongoDB 4.4.**

If you are using the [official mongodb container](https://hub.docker.com/_/mongo/), you can create your user using an `init-mongo.sh` file with the following contents (do not modify; copy/paste as is):

```sh
#!/bin/bash

if which mongosh > /dev/null 2>&1; then
  mongo_init_bin='mongosh'
else
  mongo_init_bin='mongo'
fi
"${mongo_init_bin}" <<EOF
use ${MONGO_AUTHSOURCE}
db.auth("${MONGO_INITDB_ROOT_USERNAME}", "${MONGO_INITDB_ROOT_PASSWORD}")
db.createUser({
  user: "${MONGO_USER}",
  pwd: "${MONGO_PASS}",
  roles: [
    { db: "${MONGO_DBNAME}", role: "dbOwner" },
    { db: "${MONGO_DBNAME}_stat", role: "dbOwner" }
  ]
})
EOF
```