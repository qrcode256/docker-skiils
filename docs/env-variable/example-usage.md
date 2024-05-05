```sh
sudo docker run -d -t -i -e REDIS_NAMESPACE='staging' \ 
-e POSTGRES_ENV_POSTGRES_PASSWORD='foo' \
-e POSTGRES_ENV_POSTGRES_USER='bar' \
-e POSTGRES_ENV_DB_NAME='mysite_staging' \
-e POSTGRES_PORT_5432_TCP_ADDR='docker-db-1.hidden.us-east-1.rds.amazonaws.com' \
-e SITE_URL='staging.mysite.com' \
-p 80:80 \
--link redis:redis \  
--name container_name dockerhub_id/image_name
```

```sh
# first create a file env.file with data in flat yaml format

$ docker run --env-file ./env.list ubuntu bash
```