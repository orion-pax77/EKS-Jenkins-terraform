# 🚀 Jenkins + Terraform + EKS Setup Guide (Ubuntu EC2)

This project provisions an **Amazon EKS Cluster and Node Group** using **Terraform** executed through a **Jenkins Pipeline**.

---

# 📌 Prerequisites

* AWS Account
* Key Pair (.pem file)
* IAM user with programmatic access (Access Key & Secret Key)
* GitHub Repository:

```
https://github.com/orion-pax77/Jenkins-Terraform-EKS.git
```

---

# 🟢 STEP 1: Launch EC2 Instance (Ubuntu)

Go to **AWS Console → EC2 → Launch Instance**

### Select:

* AMI → **Ubuntu Server 22.04 LTS**
* Instance Type → `c7i-flex.large`
* Storage → 20GB
* Security Group:

  * Port 22 (SSH)
  * Port 8080 (Jenkins)
  * Port 80 (Optional)

Launch instance.

---

## 🔹 Connect to EC2

```bash
ssh -i your-key.pem ubuntu@your-public-ip
```

---

# 🟢 STEP 2: Install Required Software

---

## 🔹 Update System

```bash
sudo apt update -y
```

---

# ☕ Install Java (Required for Jenkins)

```bash
sudo apt install openjdk-17-jdk -y
```

---

# 🛠 Install Jenkins (Ubuntu)

```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install jenkins -y
```

Start and enable Jenkins:

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins
```

---

## 🔹 Access Jenkins

Get initial password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Open in browser:

```
http://<EC2-PUBLIC-IP>:8080
```

Install suggested plugins and complete setup.

---

# 🟢 Install Terraform (Ubuntu)

```bash
sudo apt install -y gnupg software-properties-common curl

curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o \
  /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
  https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt install terraform -y
```

Verify:

```bash
terraform -version
```

---

# 🟢 Install AWS CLI

```bash
sudo snap insatll aws-cli --classic 
```

Verify:

```bash
aws --version
```

---

# 🟢 Configure AWS CLI

```bash
aws configure
```

Enter:

* AWS Access Key
* AWS Secret Key
* Region → `us-east-1`
* Output → `json`

---

# 🟢 STEP 3: Add AWS Credentials in Jenkins

Instead of using `aws configure`, production best practice is to store credentials in Jenkins.

### Go to:

```
Manage Jenkins → Credentials → Global → Add Credentials
```

### Select:

* Kind → **AWS Credentials**
* ID → `aws-creds`
* Add:

  * Access Key
  * Secret Key

Click **Save**

---

# 🟢 STEP 4: Create Jenkins Pipeline Job

---

## 🔹 1. Create Job

* Click **New Item**
* Name → `eks-terraform-pipeline`
* Select → **Pipeline**
* Click OK

---

## 🔹 2. Configure Pipeline

Scroll to **Pipeline Section**

Select:

* Definition → **Pipeline script from SCM**
* SCM → **Git**

Repository URL:

```
https://github.com/orion-pax77/Jenkins-Terraform-EKS.git
```

Branch Specifier:

```
*/main
```

Script Path:

```
Jenkinsfile
```

Click **Save**

---

# 🟢 STEP 5: Build the Job

Click:

```
Build Now
```

Jenkins will:

1. Clone GitHub repository
2. Run `terraform init`
3. Run `terraform validate`
4. Run `terraform plan`
5. Ask for approval
6. Run `terraform apply`
7. Create:

   * EKS Cluster
   * Node Group
   * IAM Roles

---

# ⏳ Expected Time

* EKS creation: 10–15 minutes
* Node Group provisioning: 5–10 minutes

---
# 🎯 Final Result

After successful build:

* ✅ EKS Cluster Created
* ✅ Node Group Created
* ✅ IAM Roles Attached
* ✅ Infrastructure managed through Terraform
* ✅ CI/CD handled by Jenkins

---


# 🛑 To Destroy Infrastructure

Navigate to the terraform.tfstate file:

```bash
cd /var/lib/jenkins/workspace/eks-terraform-pipeline
```
Run this command:

```bash
terraform destroy --auto-approve
```
Or create a separate Jenkins destroy pipeline.

---

