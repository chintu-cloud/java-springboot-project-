
# 🚀 Java Spring Boot + Streamlit Frontend Deployment with Docker & AWS ECR

This guide walks you through setting up Docker, cloning the project, running backend & frontend containers, and finally pushing them to **AWS Elastic Container Registry (ECR)**.  
Every command is shown with the exact EC2 prompt for reproducibility.

---
## 🐳 Step : lunch server  
        name: Docker
        instance type: c7i-flex.large   #  for light weight, low latency 
        networking: default
        security group: (All traffic 0.0.0.0/0)
<img width="893" height="180" alt="Screenshot 2025-12-04 140307" src="https://github.com/user-attachments/assets/10b2a9d8-f102-4564-afa5-ca652c69a249" />

 

## 🐳 Step : connect server
<img width="554" height="282" alt="Screenshot 2025-12-04 221114" src="https://github.com/user-attachments/assets/78e9de95-4a55-497d-ac38-016bbedacdc7" />

## 🐳 Step 1: Switch to Root & Install Docker
```bash
[ec2-user@ip-172-31-15-19 ~]$ sudo su -
[root@ip-172-31-15-19 ~]# yum install docker -y
[root@ip-172-31-15-19 ~]# systemctl start docker
[root@ip-172-31-15-19 ~]# systemctl status docker
```
👉 **Note:** Docker must be installed and running before building images.

---

## 📦 Step 2: Install Git & Clone Repository
```bash
[root@ip-172-31-15-19 ~]# yum install git -y
[root@ip-172-31-15-19 ~]# git clone https://github.com/chintu-cloud/java-springboot-project-.git
[root@ip-172-31-15-19 ~]# cd java-springboot-project-
[root@ip-172-31-15-19 java-springboot-project-]# ls
backend  compose  frontend  jenkins
```
👉 **Note:** The repo contains **backend (Spring Boot)** and **frontend (Streamlit)** modules.

---

## ⚙️ Step 3: Backend Setup
```bash
[root@ip-172-31-15-19 java-springboot-project-]# cd backend
[root@ip-172-31-15-19 backend]# ls
Dockerfile  Dockerfile1  logs  pom.xml  process  src  target
[root@ip-172-31-15-19 backend]# vi Dockerfile
[root@ip-172-31-15-19 backend]# docker build -t backend .
[root@ip-172-31-15-19 backend]# docker run -dt -p 8082:8084 backend
191dc7c4308a2229eca305c1e3125d2e597cc95be4b93f0f95afd95675e48289
[root@ip-172-31-15-19 backend]# docker ps
CONTAINER ID   IMAGE     COMMAND               CREATED          STATUS         PORTS                                       NAMES
191dc7c4308a   backend   "java -jar app.jar"   10 seconds ago   Up 9 seconds   0.0.0.0:8082->8084/tcp, :::8082->8084/tcp   stoic_khayyam
[root@ip-172-31-15-19 backend]# cd ..
```
## vi Dockerfile inside
<img width="1041" height="395" alt="Screenshot (455)" src="https://github.com/user-attachments/assets/d20d7a1f-0270-4cbf-98e8-683356ea57eb" />
   # change database url
   # change database password

👉 **Note:** Backend runs on **port 8084**, mapped to **8082** externally.

---

## 🎨 Step 4: Frontend Setup
```bash
[root@ip-172-31-15-19 java-springboot-project-]# ls
backend  compose  frontend  jenkins
[root@ip-172-31-15-19 java-springboot-project-]# cd frontend
[root@ip-172-31-15-19 frontend]# ls
Dockerfile  app.py  process  requirements.txt
[root@ip-172-31-15-19 frontend]# vi Dockerfile
[root@ip-172-31-15-19 frontend]# vi requirements.txt
[root@ip-172-31-15-19 frontend]# docker build -t frontend .                                                                                                                                                                                                    
[root@ip-172-31-15-19 frontend]# docker ps
CONTAINER ID   IMAGE     COMMAND               CREATED         STATUS         PORTS                                       NAMES
191dc7c4308a   backend   "java -jar app.jar"   5 minutes ago   Up 5 minutes   0.0.0.0:8082->8084/tcp, :::8082->8084/tcp   stoic_khayyam                                                                                                                                                                                    
[root@ip-172-31-15-19 frontend]# docker run -dt -p 8502:8501 frontend
358b7f738724d84c032febd855bdd8e1136b0e57f7672639d327cda39a0be510
[root@ip-172-31-15-19 frontend]# docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED          STATUS          PORTS                                       NAMES
358b7f738724   frontend   "streamlit run app.p…"   7 seconds ago    Up 6 seconds    0.0.0.0:8502->8501/tcp, :::8502->8501/tcp   crazy_hofstadter
191dc7c4308a   backend    "java -jar app.jar"      11 minutes ago   Up 11 minutes   0.0.0.0:8082->8084/tcp, :::8082->8084/tcp   stoic_khayyam
[root@ip-172-31-15-19 frontend]#

```
## vi Dockerfile inside
<img width="634" height="240" alt="Screenshot (457)" src="https://github.com/user-attachments/assets/7648b1fa-a8fc-4a21-bd0d-36c89b4023c3" />
# give private IP


## vi requirements.txt inside
<img width="77" height="79" alt="Screenshot (458)" src="https://github.com/user-attachments/assets/a24535e2-5b58-472a-8dad-029e4a5fddcc" />

# add plotly 


👉 **Note:** Frontend runs on **port 8501**, mapped to **8502** externally.

#  copy <public IP>:<port no.> and hit searchbar
<img width="1913" height="962" alt="Screenshot (459)" src="https://github.com/user-attachments/assets/79226f25-475a-4b3b-b053-a903e9936fa2" />
<img width="1917" height="964" alt="Screenshot (460)" src="https://github.com/user-attachments/assets/07fd6518-7d58-4449-a9b2-0597e0fc98a0" />
<img width="1917" height="964" alt="Screenshot (460)" src="https://github.com/user-attachments/assets/ca54309b-bb4d-44cc-b225-0d3b34231027" />


---

## ☁️ Step 5: Push Frontend Image to AWS ECR
1. Go to **AWS ECR Console** → Create repository
2. name: <Dockerhub name>/<IMAGE name>  --> create
  
   Example: `chandan87/frontend`

```bash
[root@ip-172-31-15-19 frontend]# aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 087843402235.dkr.ecr.us-east-1.amazonaws.com
[root@ip-172-31-15-19 frontend]# docker build -t chandan87/frontend .
[root@ip-172-31-15-19 frontend]# docker tag chandan87/frontend:latest 087843402235.dkr.ecr.us-east-1.amazonaws.com/chandan87/frontend:latest
[root@ip-172-31-15-19 frontend]# docker push 087843402235.dkr.ecr.us-east-1.amazonaws.com/chandan87/frontend:latest
```
👉 **Note:** After push, verify image digest in AWS ECR.

---

## ☁️ Step 6: Push Backend Image to AWS ECR
1. Go to **AWS ECR Console** → Create repository
2. name: <Dockerhub name>/<IMAGE name>  --> create

   Example: `chandan87/backend`

```bash
[root@ip-172-31-15-19 backend]# aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 087843402235.dkr.ecr.us-east-1.amazonaws.com
[root@ip-172-31-15-19 backend]# docker build -t chandan87/backend .
[root@ip-172-31-15-19 backend]# docker tag chandan87/backend:latest 087843402235.dkr.ecr.us-east-1.amazonaws.com/chandan87/backend:latest
[root@ip-172-31-15-19 backend]# docker push 087843402235.dkr.ecr.us-east-1.amazonaws.com/chandan87/backend:latest
```
👉 **Note:** Backend image will now be available in AWS ECR for deployment.
<img width="1895" height="262" alt="Screenshot (464)" src="https://github.com/user-attachments/assets/6718cef1-d068-4a71-92b8-504e604a5799" />

---

## ✅ Verification
- Run `docker ps` → Ensure both containers are running.  
- Check **AWS ECR Console** → Images should appear under repositories.  
- Backend accessible at `http://<EC2-IP>:8082`  
- Frontend accessible at `http://<EC2-IP>:8502`


