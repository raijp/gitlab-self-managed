# Run
```
git clone https://github.com/raijp/gitlab-self-managed.git && cd gitlab-self-managed

docker rm -f gitlab-ce-1
docker network rm gitlab

docker network create --subnet=172.43.1.0/24 --gateway=172.43.1.1 gitlab

docker run -d \
  --network gitlab \
  --hostname gitlab.example.com \
  -p 10080:80 \
  -p 10022:22 \
  --name gitlab-ce-1 \
  --restart always \
  --mount type=bind,source=$(pwd)/storage/gitlab/config,dst=/etc/gitlab \
  --mount type=bind,source=$(pwd)/storage/gitlab/logs,dst=/var/log/gitlab \
  --mount type=bind,source=$(pwd)/storage/gitlab/data,dst=/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ce:15.2.2-ce.0

docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password

```
