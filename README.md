# Web-PPT

A modern React + Vite web application, containerized with Docker and served via Nginx, deployable to AWS ECS Fargate.

## Requirements

- [Node.js & npm](https://github.com/nvm-sh/nvm#installing-and-updating)
- [Docker](https://docs.docker.com/get-docker/) (for containerization)
- AWS Account (for ECS deployment)

## Getting Started (Development)

1. **Install dependencies:**
   ```sh
   npm install
   ```

2. **Run the development server:**
   ```sh
   npm run dev
   ```
   The app will be available at [http://localhost:5173](http://localhost:5173).

## Building for Production

1. **Build the app:**
   ```sh
   npm run build
   ```
   The static files will be generated in the `dist/` directory.

## Docker Usage

### Build Docker Image
```sh
docker build -t yourusername/webppt:latest .
```

### Run Docker Container
```sh
docker run -p 80:80 yourusername/webppt:latest
```
The app will be available at [http://localhost](http://localhost).

### Push to Docker Hub
```sh
docker login
docker push yourusername/webppt:latest
```

## AWS ECS Fargate Deployment (UI Steps)

### 1. Create ECS Cluster
1. Open [AWS ECS Console](https://console.aws.amazon.com/ecs/)
2. Navigate to "Clusters" > "Create Cluster"
3. Select "Networking only" (Fargate) template
4. Enter cluster name (e.g., "webppt-cluster")
5. Click "Create"

### 2. Create Task Definition
1. Go to "Task Definitions" > "Create new Task Definition"
2. Select "Fargate" launch type
3. Configure:
   - Task Definition Name: "webppt-task"
   - Task Role: None (or create new if needed)
   - OS: Linux
   - Task Memory: 1GB (or as needed)
   - Task CPU: 0.5 vCPU (or as needed)
4. Add Container:
   - Container name: "webppt-container"
   - Image: `yourusername/webppt:latest`
   - Port mappings: Add port 80 (TCP)
   - Environment variables (if any)
5. Click "Create"

### 3. Run the Service
1. Go to your cluster
2. Click "Services" tab > "Create"
3. Configure:
   - Launch type: FARGATE
   - Task Definition: Select your created task
   - Service name: "webppt-service"
   - Number of tasks: 1
4. Configure VPC and networking:
   - Cluster VPC
   - Subnets (select at least 2 public subnets)
   - Security group (create new or select existing)
     - Must allow inbound port 80 (0.0.0.0/0)
   - Auto-assign public IP: ENABLED
5. (Optional) Configure load balancer
6. Click "Create Service"

### 4. Access Your Application
1. After service is stable, go to "Tasks" tab
2. Select your running task
3. Copy the "Public IP" from network section
4. Access in browser at `http://<public-ip>`

## Advanced Configuration

### Using Application Load Balancer (Recommended)
1. Create ALB in EC2 console
2. Configure listener on port 80
3. Create target group pointing to your ECS service
4. Update service to use load balancer

### Auto Scaling
1. Edit your service
2. Configure auto-scaling policies
3. Set minimum/maximum number of tasks

## GitHub Actions

This project includes a GitHub Actions workflow to automatically:
- Build and push Docker images to Docker Hub on every push to `main`
- Deploy to ECS when new image is pushed (optional)
Images are tagged with both `latest` and the short commit SHA.

## Nginx Configuration

The app uses a custom `nginx.conf` to support client-side routing.

## Troubleshooting

- **Connection timeouts**: Check security group inbound rules
- **Task fails to start**: Check CloudWatch logs for errors
- **Image pull errors**: Verify ECS task execution role has permissions

---

## License

MIT
```

Key additions:
1. Detailed ECS Fargate deployment steps via AWS Console UI
2. Visual workflow from cluster creation to service deployment
3. Load balancer and scaling recommendations
4. Common troubleshooting tips
5. Clear separation between local development and cloud deployment sections