In this DevOps task, you need to build and deploy a full-stack CRUD application using the MEAN stack (MongoDB, Express, Angular 15, and Node.js). The backend will be developed with Node.js and Express to provide REST APIs, connecting to a MongoDB database. The frontend will be an Angular application utilizing HTTPClient for communication.  

The application will manage a collection of tutorials, where each tutorial includes an ID, title, description, and published status. Users will be able to create, retrieve, update, and delete tutorials. Additionally, a search box will allow users to find tutorials by title.

## Project setup

### Node.js Server

cd backend

npm install

You can update the MongoDB credentials by modifying the `db.config.js` file located in `app/config/`.

Run `node server.js`

### Angular Client

cd frontend

npm install

Run `ng serve --port 8081`

You can modify the `src/app/services/tutorial.service.ts` file to adjust how the frontend interacts with the backend.

Navigate to `http://localhost:8081/`


# MEAN Stack Application - Containerized Deployment

A full-stack CRUD application built with MongoDB, Express.js, Angular, and Node.js, containerized with Docker and deployed with CI/CD automation.
## About

This project demonstrates a production-ready MEAN stack application with:
- **Containerization** using Docker
- **Multi-container orchestration** with Docker Compose
- **Automated CI/CD** pipeline using GitHub Actions
- **Reverse proxy** setup with Nginx
- **Cloud deployment** on Ubuntu VM

## 🏗️ Architecture
<img width="1020" height="682" alt="image" src="https://github.com/user-attachments/assets/5d41d6af-0b14-4795-a73c-aabccbec0970" />

### Production Deployment
- Ubuntu 20.04/22.04 LTS VM
- Minimum 2GB RAM, 2 vCPU
- Ports 22, 80 , 443 open

## Deployment
### Setup Cloud VM (15 minutes)

**Launch VM:**
- Ubuntu 22.04 LTS
- 2 vCPU, 4GB RAM minimum
- Open ports: 22, 80, 443, 3000, 4200

**Connect and Setup:**
```bash
# SSH into VM
ssh -i your-key.pem ubuntu@YOUR_VM_IP
sudo apt update && sudo apt upgrade -y
# install docker
sudo apt install docker -y
sudo apt install docker-compose -y
sudo usermod -aG docker ubuntu
```
#  Install Nginx
```sudo apt install -y nginx```
## Nginx Configuration
update the below configuration in /etc/nginx/nginx.conf  
```
events {}

http {
    upstream frontend_service {
        server 127.0.0.1:8081;
    }

    upstream backend_service {
        server 127.0.0.1:8080;
    }

    server {
        listen 80;
        server_name _;

        # API → backend
        location /api/ {
            proxy_pass         http://backend_service;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection 'upgrade';
            proxy_set_header   Host $host;
        }

        # Frontend → Angular app
        location / {
            proxy_pass         http://frontend_service;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection 'upgrade';
            proxy_set_header   Host $host;
        }
    }
}

```


#### Restart the nginx
```
sudo systemctl restart nginx
# clone the repo
git clone https://github.com/venkatasureshborra/crud-dd-task-mean-app.git
# change to directory
cd crud-dd-task-mean-app
docker-compose up -d

```
### Pipeline Stages

1. **Build & Push**
   - Builds Docker images for frontend and backend
   - Pushes images to Docker Hub
   - Uses layer caching for faster builds
2. **Deploy**
   - SSH into production VM
   - Pulls latest images
   - Restarts containers
   - Verifies deployment

### Setup GitHub Secrets

Required secrets in repository settings:
```
DOCKER_USERNAME     # Docker Hub username
DOCKER_PASSWORD     # Docker Hub password
VM_HOST            # VM IP address
VM_USER            # VM username (ubuntu)
VM_SSH_KEY         # Private SSH key
```
### Backend Dockerfile Summary Flow

```
1. Start with Node.js 18 Alpine base image (lightweight)
2. Set working directory to /usr/src/app
3. Copy package files and install production dependencies
4. Copy all application source code
5. Document that port 8080 will be used
6. Set default command to start the Node.js server
```
## Frontend Dockerfile Summary Flow

```
STAGE 1 (Build) - Uses Node.js
1. Copy package files and install ALL dependencies
2. Copy Angular source code
3. Build Angular app (TypeScript → JavaScript, optimized)
4. Output: dist/angular-15-crud/ folder with compiled files
   ↓
STAGE 2 (Serve) - Uses Nginx
5. Start fresh with Nginx Alpine
6. Copy ONLY the compiled files from Stage 1
7. Create Nginx config for Angular routing
8. Start Nginx server
```

## Docker-Compose Summary Flow
```
STAGE 1 – Create network & shared communication
1. Define bridge network (app-net)
2. Ensure all services can discover each other internally
   ↓

STAGE 2 – Start MongoDB container
3. Launch mongo image
4. Initialize DB with root username + password
5. Attach persistent volume (mongo_data)
6. Expose DB only to internal Docker network
   ↓

STAGE 3 – Start Backend container
7. Pull backend image from DockerHub
8. Inject MONGO_URI environment variable
9. Connect backend to mongo using internal DNS ("mongo")
10. Expose port 8080 to host for Nginx access
    ↓

STAGE 4 – Start Frontend container
11. Pull frontend image from DockerHub
12. Serve Angular static files via Nginx inside container
13. Expose port 8081 to host for Nginx access
    ↓

STAGE 5 – Run all services on Docker network
14. Containers run together and communicate privately
15. Backend calls mongo via "mongo:27017"
16. Frontend calls backend via "backend:8080" (inside network)
    ↓

STAGE 6 – External access
17. Nginx on host (VM) receives incoming traffic on port 80
18. Nginx routes:
      /       → frontend on port 8081
      /api/   → backend on port 8080

```


## 📸 Screenshots

Application Status<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a2444a6e-6d6d-44e4-863a-8decfe9d9f31" />
Application Status-details<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7719375b-954b-4d30-b383-f57f31bcbcbc" />
Docker_Repository<img width="1908" height="566" alt="image" src="https://github.com/user-attachments/assets/3de2f765-0bf5-4bba-873b-95a6a9ac87b5" />
CI/CD<img width="1868" height="931" alt="image" src="https://github.com/user-attachments/assets/7d74379b-ab39-4d91-af68-31e97923b87a" />

## 🛠️ Technologies Used

### Frontend
- **Angular 15+** - Frontend framework
- **TypeScript** - Type-safe JavaScript
- **RxJS** - Reactive programming
- **Angular Material** - UI components

### Backend
- **Node.js 18- alpine** - Runtime environment
- **Express.js 4** - Web framework
- **Mongoose** - MongoDB ODM
- **CORS** - Cross-origin resource sharing

### Database
- **MongoDB 8.2-noble** - NoSQL database

### DevOps
- **Docker** - Containerization
- **Docker Compose** - Multi-container orchestration
- **GitHub Actions** - CI/CD automation
- **Nginx** - Reverse proxy for backend and frontend services

### Cloud
- **AWS** - Cloud hosting
- **Ubuntu 24.2.04 LTS** - Operating system

## 📁 Project Structure

```
mean-stack-deployment/
├── .github/
│   └── workflows/
│       └── deploy.yml              # CI/CD pipeline
├── backend/
│   ├── models/                     # Mongoose models
│   ├── routes/                     # API routes
│   ├── controllers/                # Business logic
│   ├── config/                     # Configuration files
│   ├── server.js                   # Entry point
│   ├── package.json
│   ├── Dockerfile                  # Backend container
│   └── .dockerignore
├── frontend/
│   ├── src/
│   │   ├── app/                    # Angular components
│   │   ├── assets/                 # Static assets
│   │   └── environments/           # Environment configs
│   ├── angular.json
│   ├── package.json
│   ├── Dockerfile                  # Frontend container
│   ├── nginx.conf                  # Nginx config for frontend
│   └── .dockerignore
├── docker-compose.yml              # Local development
├── docker-compose.prod.yml         # Production deployment
├── setup-vm.sh                     # VM setup script
└── README.md
```
### MongoDB Configuration

Default credentials (change in production):
- Username: `admin`
- Password: `admin123`
- Database: `dd_db`
- Port: `27017`

## 🐛 Troubleshooting

### Common Issues

**Containers won't start**
```bash
docker-compose logs
docker-compose down && docker-compose up -d
```

**MongoDB connection error**
```bash
docker exec -it mongodb mongosh -u admin -p password123
# Check connection string
docker exec -it backend env | grep MONGO_URI
```

**Nginx errors**
```bash
sudo nginx -t
sudo tail -f /var/log/nginx/error.log
```

**CI/CD pipeline fails**
- Verify GitHub secrets are set correctly
- Check SSH access to VM
- Ensure Docker Hub credentials are valid

### Useful Commands

```bash
# View logs
docker-compose logs -f [service_name]

# Restart service
docker-compose restart [service_name]

# Access container shell
docker exec -it [container_name] sh

# Clean up
docker system prune -f
```

### Health Checks

- Frontend: `http://YOUR_VM_IP/`
- Backend: `http://YOUR_VM_IP/api/health`
- MongoDB: `docker exec mongodb mongosh --eval "db.adminCommand('ping')"`

### Logs

```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f backend

# Nginx logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

## 🔒 Security Considerations

- [ ] Change default MongoDB credentials
- [ ] Use strong passwords
- [ ] Environment variable encryption

## 🚦 Performance Optimization

- **Docker layer caching** - Faster builds
- **Multi-stage builds** - Smaller images
- **Nginx caching** - Faster response times

## 📈 Future Enhancements

- [ ] Add HTTPS/SSL support
- [ ] Implement monitoring with Prometheus/Grafana
- [ ] Add automated testing
- [ ] Implement logging with ELK stack
- [ ] Add database backups automation
- [ ] Kubernetes deployment option
- [ ] Load balancing for high availability

## 👤 Author

**Venkata Suresh**
- [GitHub](https://github.com/venkatasureshborra)
- [LinkedIn](https://linkedin.com/in/venkatasureshborra)

## 🙏 Acknowledgments

- MongoDB documentation
- Docker documentation
- Angular documentation
- Express.js documentation
- GitHub Actions documentation

---


- **url:** http://VM_IP
- **Docker Hub:** https://hub.docker.com/u/venkat14489



