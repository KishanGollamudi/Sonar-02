# 🧩 SonarQube Lab Setup & JavaScript/Angular Project Analysis

> 📊 **Analyze Angular Project Code Quality using SonarQube on a Local Ubuntu Server**

[![Ubuntu](https://img.shields.io/badge/OS-Ubuntu%2022.04%20LTS-blue?logo=ubuntu)](https://ubuntu.com)
[![SonarQube](https://img.shields.io/badge/SonarQube-10.6-orange?logo=sonarqube)](https://www.sonarsource.com/products/sonarqube/)
[![Angular](https://img.shields.io/badge/Project-Angular%20App-red?logo=angular)](https://angular.io/)
[![Java](https://img.shields.io/badge/Java-17-green?logo=java)](https://openjdk.org/)

---

## 🎯 Purpose

This guide walks you through:

- Installing **SonarQube** with the embedded H2 database
- Installing **Sonar Scanner CLI**
- Analyzing a **JavaScript/Angular** project
- All done in a **self-contained single-server** setup — no external database or CI/CD tools needed.

---

## ✅ Prerequisites

| Component      | Requirement              |
|----------------|--------------------------|
| OS             | Ubuntu 22.04 LTS         |
| RAM            | Minimum 4GB (8GB recommended) |
| Java           | OpenJDK 17               |
| Access         | `sudo` user              |
| Project        | JavaScript / Angular     |
| Network        | Port `9000` open         |

---

## 1️⃣ Set Hostname

```bash
sudo hostnamectl set-hostname build-sonar-test
````

---

## 2️⃣ Install Required Packages

```bash
sudo apt update -y
sudo apt install -y openjdk-17-jdk git wget unzip
```

### ✅ Verify Installations

```bash
java -version
git --version
```

---

## 3️⃣ Install and Configure SonarQube (Embedded H2 DB)

### Step 1 — Create SonarQube User

```bash
sudo useradd -m -d /opt/sonarqube -r -s /bin/bash sonar
```

### Step 2 — Download and Extract SonarQube

```bash
cd /opt
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.6.0.92116.zip
sudo unzip sonarqube-10.6.0.92116.zip
sudo mv sonarqube-10.6.0.92116 sonarqube
sudo chown -R sonar:sonar sonarqube
```

### Step 3 — Configure SonarQube

```bash
sudo nano /opt/sonarqube/conf/sonar.properties
```

Add:

```
sonar.web.host=0.0.0.0
sonar.web.port=9000
```

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`)

---

## 4️⃣ Start SonarQube

```bash
sudo -u sonar /opt/sonarqube/bin/linux-x86-64/sonar.sh start
```

### Check Status

```bash
sudo -u sonar /opt/sonarqube/bin/linux-x86-64/sonar.sh status
ps aux | grep sonar
```

---

## 5️⃣ Access SonarQube Dashboard

Visit:

```
http://<your-server-ip>:9000
```

**Default credentials:**

* Username: `admin`
* Password: `admin` (you’ll be prompted to change it)

---

## 6️⃣ Generate Authentication Token

1. Log in to the SonarQube dashboard.
2. Navigate to: `My Account → Security → Generate Tokens`
3. Name it: `js-lab-token`
4. Copy and save the generated token securely.

---

## 7️⃣ Install Sonar Scanner (CLI)

### Step 1 — Download

```bash
cd /opt
sudo wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-5.0.1.3006-linux.zip
sudo unzip sonar-scanner-5.0.1.3006-linux.zip
sudo mv sonar-scanner-5.0.1.3006-linux sonar-scanner
```

### Step 2 — Set Environment Variable

```bash
sudo nano /etc/profile.d/sonar-scanner.sh
```

Add:

```bash
export PATH=$PATH:/opt/sonar-scanner/bin
```

Apply:

```bash
source /etc/profile.d/sonar-scanner.sh
```

### Step 3 — Verify

```bash
sonar-scanner -v
```

Expected output includes version info and Java 17 confirmation.

---

## 8️⃣ Clone Your Angular Project

```bash
cd ~
git clone https://github.com/example/Angular-Calc-App.git
cd Angular-Calc-App
```

---

## 9️⃣ Create `sonar-project.properties`

Create and edit:

```bash
sudo nano sonar-project.properties
```

Paste (edit placeholders):

```properties
# Project identification
sonar.projectKey=AngularCalcApp
sonar.projectName=Angular Calculator App
sonar.projectVersion=1.0

# Source settings
sonar.sources=.

# Exclude test files and node_modules
sonar.exclusions=node_modules/**,**/*.spec.ts

# Encoding
sonar.sourceEncoding=UTF-8

# SonarQube server & token
sonar.host.url=http://<your-sonarqube-server-ip>:9000
sonar.login=<your-generated-token>
```

Example:

```properties
sonar.host.url=http://13.59.89.88:9000
sonar.login=sqp_abc123xyz456token
```

Save and exit.

---

## 🔟 Run SonarQube Analysis

From the project root:

```bash
sonar-scanner
```

Expected log lines:

```
INFO: Scanner configuration file: ...
INFO: Project root configuration file: sonar-project.properties
INFO: Analyzing Angular Calculator App
INFO: EXECUTION SUCCESS
```

---

## 1️⃣1️⃣ View Results on Dashboard

Visit:

```
http://<your-server-ip>:9000/dashboard?id=AngularCalcApp
```

Review key metrics:

| Metric             | Description                   |
| ------------------ | ----------------------------- |
| 🐞 Bugs            | Code errors                   |
| 🔐 Vulnerabilities | Security issues               |
| ✅ Code Smells      | Maintainability issues        |
| 📊 Duplications    | Reused/duplicated code blocks |

---

## 1️⃣2️⃣ (Optional) Debugging

If the scan fails:

```bash
sonar-scanner -X
```

Prints detailed error logs for debugging.

---

## 🧠 Summary

This lab setup allows you to:

* ✅ Host **SonarQube** locally with embedded H2 DB
* ✅ Analyze **JavaScript/Angular** code with Sonar Scanner CLI
* ✅ View static code analysis results via the web dashboard

---

## 📸 Screenshots (Optional)

> 

* ✅ **SonarQube Dashboard Home**
<img width="1920" height="1080" alt="12" src="https://github.com/user-attachments/assets/f01ea2b9-aa80-4a49-992d-ae5781a78611" />
<img width="1920" height="1080" alt="18" src="https://github.com/user-attachments/assets/a71d0699-d883-4186-bd0a-b006879da63e" />
<img width="1920" height="1080" alt="17" src="https://github.com/user-attachments/assets/decb7be2-3b24-4c1f-9cab-7efb4adc8695" />
<img width="1920" height="1080" alt="16" src="https://github.com/user-attachments/assets/fbd2d4fc-cdd4-4110-9421-5fac8e91ab75" />
<img width="1920" height="1080" alt="15" src="https://github.com/user-attachments/assets/8a1999aa-0b06-49a5-92f9-e49167d864c0" />
<img width="1920" height="1080" alt="14" src="https://github.com/user-attachments/assets/bca2b89e-9c7b-446c-8f42-b2e4a6578cdb" />
<img width="1920" height="1080" alt="13" src="https://github.com/user-attachments/assets/75659e28-5f16-447a-bb0b-5195dcf61b8b" />

* ✅ **Analysis Overview for AngularCalcApp**
* <img width="1920" height="1080" alt="24" src="https://github.com/user-attachments/assets/825bc815-4683-4e41-9069-0d01e4caa452" />

---

## 📂 Project

**Repo**: [Angular Calculator App](https://github.com/example/Angular-Calc-App)
**Dashboard**: `http://<your-sonarqube-server-ip>:9000`

---

## 👨‍🔧 Maintainer

Feel free to reach out or fork the repo to enhance your own SonarQube lab environments!

---

```

Let me know if you'd like me to:

- Replace placeholder IPs and URLs with real ones
- Add screenshot links
- Add badges for test/build status if you're using GitHub Actions or similar

Would you like this turned into a downloadable file or pasted into a GitHub canvas?
```
