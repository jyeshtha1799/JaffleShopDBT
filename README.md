
# Containerised VirtuMart DBT Project
# This repository hosts a containerised version of the popular VirtuMart DBT project,
# originally known as Jaffle Shop by dbt Labs. Use this project to populate and run 
# VirtuMart in a Postgres instance through Docker.

# Running the Project with Docker
# To get started, build and run the services defined in your docker-compose.yml:
echo "Building and running Docker containers..."
docker-compose build
docker-compose up

# This setup runs a Postgres instance and builds the dbt resources of VirtuMart as outlined 
# in the repository. These containers will remain active, allowing you to:
# - Query the Postgres database and interact with tables created from dbt models.
# - Execute additional dbt commands via the dbt CLI.

# Building Additional or Modified Data Models
# After the containers are operational, any modifications to the dbt project can be applied by re-running commands. 

# 1. Access the dbt Container:
# Identify the running dbt container:
echo "Accessing the dbt container..."
container_id=$(docker ps | grep dbt | awk '{print $1}')
docker exec -it $container_id /bin/bash

# 2. Run dbt Commands:
# Execute the following in the container to update or rebuild models:
echo "Running dbt commands inside the container..."
docker exec -it $container_id /bin/bash -c "dbt deps; dbt seeds --profiles-dir profiles; dbt run --profiles-dir profiles; dbt snapshot --profiles-dir profiles; dbt test --profiles-dir profiles"

# Alternatively, combine these steps:
echo "Building all dbt models and tests..."
docker exec -it $container_id /bin/bash -c "dbt build --profiles-dir profiles"

# Querying VirtuMart Data Models on Postgres
# To query and verify the models, seeds, and snapshots created, follow these steps:

# 1. Access the Postgres Service:
# Locate the container ID:
echo "Accessing the Postgres container..."
postgres_container_id=$(docker ps | grep postgres | awk '{print $1}')
docker exec -it $postgres_container_id /bin/bash

# 2. Query Using psql:
# Start the PostgreSQL interface:
echo "Starting psql..."
docker exec -it $postgres_container_id /bin/bash -c "psql -U postgres"

# List tables and views:
echo "Listing tables and views in the database..."
docker exec -it $postgres_container_id /bin/bash -c "psql -U postgres -c '\dt; \dv'"

# 3. Perform SQL Queries:
# Execute SQL queries to interact with the dbt models:
echo "Querying tables..."
table_or_view_name="customers"  # Change to the desired table or view
docker exec -it $postgres_container_id /bin/bash -c "psql -U postgres -c 'SELECT * FROM $table_or_view_name;'"

# Conclusion
# This containerised approach to the VirtuMart DBT project simplifies the process of running 
# and managing dbt tasks in a local development environment, using Docker to streamline operations
# and interactions with the PostgreSQL database.
