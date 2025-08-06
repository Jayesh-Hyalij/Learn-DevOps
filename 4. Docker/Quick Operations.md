### Task
- Create custom image
- Create cintainer from image
- Add volume
- Backups Image, Container, Volume
- Launch new EC2
- Restore all backup and run

### Steps:
1. Launch Amazon Linux
2. SSH and Install docker
    
    1. Update the system
    ```
    sudo yum update -y
    ```
    2. Install Docker
    ```
    sudo yum install docker -y
    ```
    3. Enable and start Docker
    ```
    sudo systemctl enable docker
    sudo systemctl start docker
    ```
    4. Varify Installation
    ```
    docker version
    docker info
    ```
    5. Download Docker Compose
    ```
    sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    ```
    6. Make it executable
    ```
    sudo chmod +x /usr/local/bin/docker-compose
    ```
    7. Verify Docker Compose installation
    ```
    docker-compose version
    ```
3. Create Docker Compose file
    1. Create file Structure like -
    ```
    project-root/
    │
    ├── docker-compose.yml
    │
    ├── DB/
    │   └── init.sql
    │
    ├── Frontend/
    │   ├── Dockerfile
    │   ├── .dockerignore
    │   └── UDKMS-Frontend/
    │
    └── Backend/
        ├── Dockerfile
        ├── .env
        ├── .dockerignore
        └── UDKMS-Backend/
    ```
    2. Write DockerFile
    3. Copy this file to host machine
        - Access key must be present on that path with permission (chmod 400)
    ```
    scp -i ./my-key.pem -r ./Three-tier-Docker-project ec2-user@<Public IPv4 address>:/home/ec2-user
    ```
4. Build Image and run container with attached volume
    - We can create Volume using Dockerfile while writing file
    - Build image and run container using single command
    ```
    sudo docker compose up --build -d
    ```
    - Varify Volumes
    ```
    sudo docker volume ls
    ```
    - Inspect volume location:
    ```
    sudo docker volume inspect backend_data
    ```
    
5. Backup
6. Launch another Instance and install Docker
7. Restore backups
8. Run container

---
## 🧱 Ways to Create Docker Images
1. From a Dockerfile ✅ (Most Common & Recommended)
    
    ```
    docker build -t <image-name> .
    ```
    - Create an image from instructions in a Dockerfile.
    - Supports layering, versioning, automation.
    
2. Commit from a Running Container
    ```
    docker commit <container-id> <image-name>
    ```
    - Create a new image from the current state of a container.
    - Useful for capturing manual changes.
        
     ⚠️ Not ideal for automation or production.
    
3. Using Dockerfile with Remote Context
    ```
    docker build -t <image-name> https://github.com/user/repo.git
    ```
    - Build image from a Git repo or remote Docker context.

4. Pull from Registry
    ```
    docker pull <image-name>
    ```
    - Download a prebuilt image from Docker Hub or private registry.

5. Export/Import Tar Files (Advanced)
    ```
    docker save <image-name> > image.tar
    docker load < image.tar
    ```
    - Create image files manually and share/import across systems.

## 🖥️ Ways to Create Docker Containers
1. Run a New Container from an Image
    ```
    docker container run <image-name>
    ```
    - The most common method.
    - Creates and starts container in one step.
        
    Example:

    ```
    docker container run -it ubuntu /bin/bash
    ```

2. Create Without Starting (Then Start Later)
    ```
    docker container create <image-name>
    docker container start <container-id>
    ```
    - Useful for advanced workflows (e.g., init config before start).
    
3. Using docker-compose (for Multi-container apps)
    ```
    docker-compose up -d
    ```
    - Uses docker-compose.yml to define containers and networking.
    - Supports building images and volumes.
    
4. Container from Dockerfile (build + run)
    ```
    docker build -t myapp .
    docker run myapp
    ```
    - Build an image, then use it to run a container.

5. Restore from Exported Container
    ```
    docker export <container-id> > container.tar
    cat container.tar | docker import - mycontainer
    ```
    - Advanced use case for backups or transfers.

---
## Docker backup strategies
### 1. 🔄 Docker Image Backup
    
### Option 1: Export and Import Image using Tar
  
```
docker save -o <image-name>.tar <image-name>
```
- Saves image as a tarball.
- To restore:
```
docker load -i <image-name>.tar
```
📌 Use case: Share or archive Docker images offline.

---
### 2. 🔁 Docker Container Backup

### Option 1: Commit and Save as Image
(Local image + tar backup only. Not DockerHub)
```
docker commit <container-id> my-backup-image
docker save -o my-backup-image.tar my-backup-image
```

### Option 2: Export Filesystem Only
(Only in file System)
```
docker export <container-id> -o container-backup.tar
```
To restore:
```
cat container-backup.tar | docker import - my-restored-container
```

📌 Use case: Capture container file changes, but loses environment/configs (like volumes and ports).

---
### 📦 3. 💾 Docker Volume Backup
#### 🔹 a. Bind Mount Volume Backup
Bind mounts are just host directories, so back them up like any regular directory.
```
tar -czvf bind-volume-backup.tar.gz /path/on/host
```
To restore:
```
tar -xzvf bind-volume-backup.tar.gz -C /path/on/host
```
📌 Use case: Easy and direct since files are on the host filesystem.
#### 🔹 b. Named Volume Backup (Docker-managed)
You need to access the volume via a temporary container.

Backup:
```
docker run --rm -v myvolume:/volume -v $(pwd):/backup busybox \
  tar czf /backup/myvolume-backup.tar.gz -C /volume .
```
Restore:
```
docker run --rm -v myvolume:/volume -v $(pwd):/backup busybox \
  sh -c "cd /volume && tar xzf /backup/myvolume-backup.tar.gz"
```
📌 Use case: Ideal for named Docker volumes used in production.

---
## Summary

| Type of Volume / Data                  | Backed Up in Docker Hub?         | Description                                                                 |
|----------------------------------------|----------------------------------|-----------------------------------------------------------------------------|
| **Named Volume**                       | ❌ No                            | Data is stored locally on host, not included in images pushed to Docker Hub. Must be backed up separately. |
| **Bind Volume**                        | ❌ No                            | Points to host directory, not part of image or pushed to registry. Backup must be manual. |
| **Docker Image**                       | ✅ Yes (when pushed)             | Can be pushed to Docker Hub via `docker push`. Contains application and environment, but not runtime data. |
| **Container Filesystem (committed image)** | ✅ Yes (via commit + push)     | Can be committed to image, then pushed. Still excludes mounted volume data. |


📌 Note: Docker Hub is an image repository. It does not store volumes or runtime data. To back up actual application data, you must handle volume backups separately.

---
## Notes
- Using 1 docker compose file we create 3 diff images and 3 different containers
- Widely use 1 host machine for multi container
- In production level or big projects we use mmulti host multi container
   - to archive this setup was complicated connection between multi host + container
- for multi-host recommend to use Docker Swarm

---