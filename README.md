# Hasura Downgrade Cron Issue

A helper repository for an issue on [hasura/graphql-engine](https://github.com/hasura/graphql-engine).

# Reproduction steps

```bash
$ hasura update-cli --version 1.3.3
$ git checkout 71288c41da232a13380a5af6b6bccb8947068472 # 1.3.3 server
$ docker-compose up -d
$ hasura migrate apply
$ hasura metadata apply

$ docker-compose down
$ git checkout c05415170267250ffa6541fb86fbe896f69ed258
$ hasura update-cli --version 2.0.1
$ docker-compose up -d
$ docker-compose down
$ docker-compose up -d postgres

# https://hasura.io/docs/latest/graphql/core/deployment/downgrading.html#step-2-downgrade-hasura-catalogue-version
$ docker run -e HASURA_GRAPHQL_DATABASE_URL="postgres://postgres:postgrespassword@172.17.0.1:5432/postgres" hasura/graphql-engine:v2.0.1 graphql-engine downgrade --to-v1.3.3 > error_message.txt
```

# Why this fails
I've added a cron trigger that is included in the metadata. I've called it foo in this example.
Running the docker downgrade command returns this error (it is in error_message.txt in the root
of this repository):
```
...
"description":"Key (trigger_name)=(foo) is not present in table \"hdb_cron_triggers\"."},"arguments":[]},"path":"$","error":"database query error","code":"unexpected"}
```
