# SonarQube Installation on Linux (Step-by-Step)

This guide shows how to install and configure SonarQube on Linux using bash commands step by step.

---

## Step 1: Install prerequisites (Java & unzip)
```bash
sudo apt update
sudo apt install -y openjdk-17-jdk unzip wget
```
Check Java is installed:
```bash
java -version
```
---

## Step 2: Create a dedicated SonarQube user
```bash
sudo useradd -r -s /bin/false sonar
```
---

## Step 3: Download SonarQube
```bash
SONAR_VERSION="10.5.0.60888" 
SONAR_ZIP="sonarqube-$SONAR_VERSION.zip"
SONAR_URL="https://binaries.sonarsource.com/Distribution/sonarqube/$SONAR_ZIP"

cd /tmp
wget $SONAR_URL
```
---

## Step 4: Extract and move to /opt
```bash
sudo unzip $SONAR_ZIP -d /opt/
sudo mv /opt/sonarqube-$SONAR_VERSION /opt/sonarqube
sudo chown -R sonar:sonar /opt/sonarqube
```
---

## Step 5: Configure systemd service
```bash
sudo tee /etc/systemd/system/sonar.service > /dev/null <<EOF
[Unit]
Description=SonarQube service
After=network.target

[Service]
Type=forking
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
User=sonar
Group=sonar
Restart=always
LimitNOFILE=65536
LimitNPROC=4096
TimeoutSec=600

[Install]
WantedBy=multi-user.target
EOF
```
---

## Step 6: Reload systemd and enable/start SonarQube
```bash
sudo systemctl daemon-reload
sudo systemctl enable sonar
sudo systemctl start sonar
```
Check the service status:
```bash
sudo systemctl status sonar --no-pager
```
---

## Step 7: Access SonarQube

Open a browser and go to:

http://localhost:9000

Default credentials:

Username: admin
Password: admin

# SonarQube Installation on Windows (Step-by-Step)

This guide shows how to install and configure SonarQube on Windows.

---

## Step 1: Download SonarQube

1. Go to [https://www.sonarqube.org/downloads/](https://www.sonarqube.org/downloads/)
2. Download the **SonarQube Community Edition** (or Developer/Enterprise if licensed).

---

## Step 2: Extract SonarQube

1. Extract the zip file to a folder, e.g., `C:\sonarqube`.

---

## Step 3: Configure database (optional for production)

1. Open `C:\sonarqube\conf\sonar.properties`.
2. For production database (PostgreSQL/MySQL/SQL Server), uncomment and set:

sonar.jdbc.username=your_db_user  
sonar.jdbc.password=your_db_password  
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube  

3. For development/testing, the embedded H2 database requires no changes.

---

## Step 4: Run SonarQube

1. Navigate to `C:\sonarqube\bin\windows-x86-64`.
2. Run `StartSonar.bat`.
3. Wait 1–2 minutes for startup.

---

## Step 5: Access SonarQube

1. Open a browser and go to:

http://localhost:9000

2. Default credentials:

Username: admin  
Password: admin

---

## Step 6: Optional – Install as Windows Service

1. Navigate to `C:\sonarqube\bin\windows-x86-64`.
2. Run `InstallNTService.bat` to install SonarQube as a Windows service.
3. The service will start automatically on system boot.
