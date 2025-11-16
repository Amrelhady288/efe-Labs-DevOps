# Flask Microservice Dockerization

## Install App Dependencies

```
pip install -r requirements.txt
```

(Example output omitted for brevity)

## Run App Locally

```
python app.py
```

App runs on:

* [http://127.0.0.1:5000](http://127.0.0.1:5000)
* [http://192.168.x.x:5000](http://192.168.x.x:5000)

---

## Build Docker Image

```
docker build -t flask-microservice:v1 .
```

(Shows layer pulling + dependency installation)

---

## Run Container

```
docker run -d -p 8080:5000 --name flask-app flask-microservice:v1
```

---

## Test Application

```
curl http://localhost:8080
```

Expected output:

```
Hello from Flask running inside Docker! Welcome, DevOps Engineer!
```

