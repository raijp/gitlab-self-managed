# 1. Run.
```
git clone https://github.com/raijp/gitlab-self-managed.git && cd gitlab-self-managed


docker rm -f nginx-main
docker rm -f gitlab-ee-1
docker network rm gitlab


docker network create --subnet=172.43.1.0/24 --gateway=172.43.1.1 gitlab

docker build --network host -t nginx:bullseye nginx/.

docker run -d \
  -p 80:80 \
  --restart unless-stopped \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  --mount type=bind,source="$(pwd)"/nginx/etc/nginx/conf.d,target=/etc/nginx/conf.d \
  --name nginx-main nginx:bullseye

docker run -d \
  --network gitlab \
  --hostname gitlab.example.com \
  --log-driver none \
  -p 10080:80 \
  -p 10022:22 \
  --restart unless-stopped \
  --mount type=bind,source=$(pwd)/gitlab/storage/config,dst=/etc/gitlab \
  --mount type=bind,source=$(pwd)/gitlab/storage/logs,dst=/var/log/gitlab \
  --mount type=bind,source=$(pwd)/gitlab/storage/data,dst=/var/opt/gitlab \
  --shm-size 256m \
  --name gitlab-ee-1 gitlab/gitlab-ee:16.1.1-ee.0


# docker exec -it nginx-main service nginx reload
```

# 2. Take a break a lil bit maybe about 5mins then get a root password.
```
docker exec -it gitlab-ee-1 grep 'Password:' /etc/gitlab/initial_root_password
```

# 3. HOSTS file should be set the following ip and host name (x.x.x.x is hosting server ip).
```
x.x.x.x gitlab.example.com
```

# 4. Navigate to http://gitlab.example.com then set username(root) and password.
