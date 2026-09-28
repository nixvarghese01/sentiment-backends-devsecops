# devsecops-coit-backend: Sentiment Analysis Backends (Hardened)

The two backend services of the COIT sentiment-analysis app, with extra Dockerfiles for DevSecOps work (dev, multi-stage and hardened images).

| Service | Folder | Stack | Port |
|---|---|---|---|
| Web API | `devsecops-coit-backend1/` | Java 8, Spring Boot, Maven | 8080 |
| Logic service | `devsecops-coit-backend2/` | Python, Flask, TextBlob | 5000 |

The app is a three-tier sentiment-analysis demo: you type a sentence in the web UI and it returns a polarity score.

```
React frontend  --POST /sentiment-->  Spring Boot web API  --POST /analyse/sentiment-->  Python (Flask + TextBlob)
   (nginx :80)                            (:8080)                                         (:5000)
```

## Build
```bash
# Web API (hardened image)
cd devsecops-coit-backend1
docker build -f Dockerfile-secure -t <dockerhub-user>/coit-backend1 .

# Logic service
cd devsecops-coit-backend2
docker build -t <dockerhub-user>/coit-backend2 .
```

## Run
```bash
docker network create sa
docker run -d --name sa-logic --network sa -p 5000:5000 <dockerhub-user>/coit-backend2
docker run -d --name sa-web --network sa -p 8080:8080 -e SA_LOGIC_API_URL=http://sa-logic:5000 <dockerhub-user>/coit-backend1

curl -X POST localhost:8080/sentiment -H 'Content-Type: application/json' -d '{"sentence":"I love this"}'
```

## Branches
`main` (default)
