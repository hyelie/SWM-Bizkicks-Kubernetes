# SWM-Bizkicks-Kubernetes
Bizkicks 서비스의 쿠버네티스 배포를 위한 리포지토리입니다.

## 환경 구성 (Environment Setup)
### Clone
```
git clone https://github.com/hyelie/SWM-Bizkicks-Kubernetes.git
```


### 배포
#### MySQL 배포 및 secret 설정
```
kubectl apply -f backend-mysql-pvc.yml # DB 저장공간 배포

# 이후 kubernetes secret을 설정
# 예시
# mysql-root-pw : rootpw
# kubectl create secret generic mysql-root-pw --from-literal=rootpw=root

# userinfo: username, userpw
# kubectl create secret generic userinfo --from-literal=username='depuser' --from-literal=userpw='depuserpw'

# dbinfo : dbname, url
# kubectl create secret generic dbinfo --from-literal=dbname=bizschema --from-literal=url='jdbc:mysql://35.184.171.248:3306/bizschema?autoReconnect=true&useSSL=false&useUnicode=true&characterEncoding=UTF-8&autoReconnectForPools=true&serverTimezone=UTC'

kubectl apply -f backend-mysql-deploy.yml # secret 설정 후 DB 배포
```

#### Redis 배포
```
kubectl apply -f redis.yml # redis 배포
```

#### 서버 배포
배포 전, docker image가 만들어진 상태여야 하며 backend-spring.yml 파일에 image를 설정해 주어야 합니다.
```
# 예시
containers:
 - name: echo
   image: gcr.io/temporal-fx-321902/bizkicks-backend:0.0.5
   ports:  
   ...
```

이후 백엔드 서버를 배포합니다.
```
# 초기 배포 시
kubectl apply -f bizkicks-kubernetes/backend-spring.yml

# 배포 중단 시
gcloud container clusters resize hardcoded-api --num-nodes=0 --zone=us-central1-c

# 배포 재시작 시
gcloud container clusters resize hardcoded-api --num-nodes=3 --zone=us-central1-c
```
