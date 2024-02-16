# GitLab docker

## env
- linux
- docker

## usage
```bash
docker compose up

docker compose down -v --remove-orphans

# check init pass
docker compose exex {container name} cat /etc/gitlab/initial_root_password
```
