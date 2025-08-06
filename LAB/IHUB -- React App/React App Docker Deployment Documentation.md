# 📄 React App Docker Deployment Documentation

## 1. **Clone the Repository**

If your app is in GitHub:

```bash
git clone https://github.com/SatyajitNayak-Exelixi/1HUB.git
cd 1HUB
```

---

## 2. **Project Structure**

Ensure your React app has:

```
1HUB/
 ├── public/
 ├── src/
 ├── package.json
 ├── package-lock.json
 ├── .gitignore
 ├── Dockerfile
 └── .dockerignore
```

---

## 3. **Dockerfile for React + Nginx**

Create a file named `Dockerfile` in the project root:

```dockerfile
# Stage 1: Build React app
FROM node:18-alpine AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## 4. **.dockerignore**

Prevent unnecessary files from being copied into the Docker image:

```
node_modules
build
.dockerignore
Dockerfile
.git
.gitignore
```

---

## 5. **Build Docker Image**

Run the following in the project root:

```bash
docker build -t exelixiquest/1hub:v1 .
```

* `-t` → Name and tag your image (`repo:tag`)
* `.` → Current directory as build context

---

## 6. **Run Docker Container Locally**

```bash
docker run -d -p 3000:80 --name my-1hub exelixiquest/1hub:v1
```

* `-d` → Run in detached mode
* `-p 3000:80` → Map local port 3000 → container port 80
* `--name my-1hub` → Name the container

Now, open:
🔗 [http://localhost:3000](http://localhost:3000)

---

## 7. **Push Image to Docker Hub**

Login to Docker Hub:

```bash
docker login
```

Push the image:

```bash
docker push exelixiquest/1hub:v1
```

---

## 8. **Deploy to AWS EC2**

**On EC2 instance:**

1. **Install Docker** (if not installed)

```bash
sudo apt update && sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

2. **Login to Docker Hub**

```bash
docker login
```

3. **Pull Image**

```bash
docker pull exelixiquest/1hub:v1
```

4. **Run Container**

```bash
docker run -d -p 80:80 --name my-1hub exelixiquest/1hub:v1
```

---

## 9. **AWS Security Group Settings**

* Go to **EC2 > Security Groups**.
* Edit **Inbound rules**:

  * Add **HTTP (80)** from **0.0.0.0/0** and **::/0**.

---

## 10. **Verify Deployment**

On EC2 terminal:

```bash
curl http://localhost
```

From your browser:

```
http://<EC2_PUBLIC_IP>
```

---

✅ **Final Notes:**

* If React app originally runs on port 3000, **it doesn’t matter** inside Docker — Nginx will serve it on **port 80**.
* Always use `.gitignore` to avoid pushing secrets (e.g., `.npmrc`).
* If secrets were committed before, you must rewrite Git history before pushing.
