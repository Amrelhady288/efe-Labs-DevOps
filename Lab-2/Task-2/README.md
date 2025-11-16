# Docker Compose Lab – Nginx & MySQL

## Run Containers

```
docker-compose up -d
```

> Note: The `version` attribute in docker-compose.yml is obsolete.

**Output Example:**

* nginx Pulled
* mysql_db Pulled
* Network Created
* Volume Created
* Containers Started

---

## Test Application

```
curl -s http://localhost:8080
```

Expected output (default Nginx page):

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>
```

---

## Test Communication Between Containers

```
docker exec nginx-server ping -c 4 mysql_db
```

Expected output:

```
4 packets transmitted, 4 received, 0% packet loss
```

---

## Check Docker Volume

```
sudo ranger /var/lib/docker
```

---

## Remove Containers

```
docker compose down
```

(Removes containers + network)

---

## Run Containers Again (Test Data Persistence)

```
docker compose up -d
```

Expected: Containers start again without losing data.

Test again:

```
curl -s http://localhost:8080
```

Nginx page should appear.

