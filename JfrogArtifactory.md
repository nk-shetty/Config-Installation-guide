# JFrog Artifactory Installation Guide (Linux)

---

# Prerequisites

## System Requirements
- 64-bit Linux server (Ubuntu/CentOS/RHEL)
- Minimum 4 GB RAM (8 GB recommended)
- Minimum 10 GB free disk space
- Root or sudo access

## Software Requirements
- Java (required for non-container installation)
- Supported database for Production:
  - PostgreSQL (recommended)
  - MySQL
  - Oracle

## Required Ports
- 8081 (Artifactory UI)
- 8082 (Artifactory Router)

---

# Note on Editions

- **Open Source (OSS)**: Free edition for community use; typically labeled `artifactory-oss` in Docker images or package downloads.  
- **Professional / Enterprise (PRO)**: Paid edition with additional features like advanced security, high availability, and license-based; labeled `artifactory-pro`.  
- **How to identify:**  
  - **Docker images:** `releases-docker.jfrog.io/jfrog/artifactory-oss:latest` vs `.../artifactory-pro:latest`  
  - **Binary downloads:** `jfrog-artifactory-oss-<version>` vs `jfrog-artifactory-pro-<version>`  
  - **UI / About page:** PRO edition will show license info and extra modules; OSS will be simpler with basic features.

---

# Install JFrog Artifactory (Linux Binary Method)

---

## 1. Download Artifactory

### OSS Version
```bash
wget https://releases.jfrog.io/artifactory/artifactory-oss/org/artifactory/oss/jfrog-artifactory-oss-<version>-linux.tar.gz
```

### PRO Version
```bash
wget https://releases.jfrog.io/artifactory/artifactory-pro/org/artifactory/pro/jfrog-artifactory-pro-<version>-linux.tar.gz
```

---

## 2. Extract Package

```bash
tar -xvzf jfrog-artifactory-*-linux.tar.gz
```

---

## 3. Move to /opt

```bash
sudo mv jfrog-artifactory-* /opt/artifactory
```

---

## 4. Set Permissions

```bash
sudo chown -R artifactory:artifactory /opt/artifactory
```

---

## 5. Start Artifactory

```bash
cd /opt/artifactory/app/bin
./artifactory.sh start
```

---

## 6. Enable as Service (Optional)

```bash
sudo /opt/artifactory/app/bin/installService.sh
sudo systemctl start artifactory
sudo systemctl enable artifactory
```

---

## 7. Verify Installation

Open browser:

```
http://<server-ip>:8081
```

---

# Run JFrog Artifactory Using Docker

---

## OSS Version

```bash
docker run -d \
  --name artifactory \
  -p 8081:8081 \
  -p 8082:8082 \
  releases-docker.jfrog.io/jfrog/artifactory-oss:latest
```

---

## PRO Version

```bash
docker run -d \
  --name artifactory \
  -p 8081:8081 \
  -p 8082:8082 \
  releases-docker.jfrog.io/jfrog/artifactory-pro:latest
```

---

## Verify Docker Deployment

Open browser:

```
http://<server-ip>:8081
```

---

# Initial Setup

1. Set admin password  
2. Configure database (if external DB used)  
3. Upload license (for PRO version)  
4. Finish setup  

---

End of file.
