# GitLab docker

## env
- linux
- docker

## usage
```bash
echo "127.0.0.1 gitlab.local" | sudo tee -a /etc/hosts
sudo mkdir -p volumes/ssl
cd volumes/ssl
sudo openssl req -x509 -nodes -days 3650 -newkey rsa:4096 \
  -keyout gitlab.local.key \
  -out gitlab.local.crt \
  -subj "/C=JP/ST=Tokyo/L=Tokyo/O=MyOrg/OU=Dev/CN=gitlab.local"

docker compose up -d
# check init pass
docker compose exec gitlab cat /etc/gitlab/initial_root_password
```

## http版
```yml
services:
  web:
    image: gitlab/gitlab-ce:latest
    container_name: 'gitlab'
    restart: always
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://localhost:8929'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
        nginx['listen_port'] = 80
    ports:
      - '8929:80'
      - '2224:22'
    volumes:
      - './gitlab/config:/etc/gitlab'
      - './gitlab/logs:/var/log/gitlab'
      - './gitlab/data:/var/opt/gitlab'
```

```bash
docker compose up -d

# check init pass
docker compose exec gitlab cat /etc/gitlab/initial_root_password
```

## 信頼済み証明書に追加
```bash
sudo cp gitlab.local.crt /usr/local/share/ca-certificates/gitlab.local.crt
sudo update-ca-certificates

# 証明書をDockerに信頼させる
sudo mkdir -p /etc/docker/certs.d/gitlab.local:5050
sudo cp ./volumes/ssl/gitlab.local.crt /etc/docker/certs.d/gitlab.local:5050/ca.crt
sudo systemctl restart docker
```
