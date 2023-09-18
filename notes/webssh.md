Tag: #tool 

---

## WebSSH
https://github.com/huashengdun/webssh

### Deployment:

#### docker-compose
- Download project from Github:
```shell
git clone https://github.com/huashengdun/webssh.git
```
- Run with docker-compose
```shell
# Start service
docker-compose up -d

# Stop service
docker-compose down
```

#### command-line

```shell
wssh --address='127.0.0.1' --port=8888 --policy=reject
```

### URL Arguments

```
http://webssh-server:<port>?hostname=<target host>&username=<username>&password=<base64_encoded_str>
```
