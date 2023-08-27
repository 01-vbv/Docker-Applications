In this we will deploy a PostgreSQL database using secrets, configurations, and volumes on an IaC file. This file will contain all the application's requirements and will be used to deploy the application as a Docker Stack. We are using swarm cluster node 2 for creating Volume and scheduling the service.


Docker Stack file description

We defined the postgres:alpine image for the database service.

The database service will only be scheduled on worker nodes with a tier label key and a value of database. In this case, it will run tasks only on node2.

The postgres image can use Docker Swarm secret files as environment variables, and in this case, it will use postgres_password mounted on /run/secrets/postgres_password. The secret is declared externally because it was previously created outside of this file.

We also added a config file to create an initial database called docker. The config file is external as well because we added it outside the postgres-stack.yaml file.

We also added an external volume named PGDATA. We will use this volume for the database but it will only exist on node2. It is defined as external because we manually create the PGDATA volume locally on node2.

We published the PostgreSQL application's port 5432 on the host's port; that is, 15432. We changed the published port to recognize that they are not the same because 5432 will be an internal port on the defined network named net.

Finally, we defined the net network as attachable to be able to test our database with a simple container running a postgres client. We added two aliases to the database service inside this network: postgres and mydatabase.

Notice that all the objects that were created for the stack will use the stack's name as a prefix. This will not happen on externally defined objects. They will be used, but we create them manually, outside of the stack's life cycle.