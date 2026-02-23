Below is a **proper README documentation (Ubuntu/Debian based EC2)** that you can directly paste into your GitHub repository.

It includes:

* EC2 setup (Ubuntu)
* Jenkins installation
* Terraform installation
* AWS CLI installation
* Adding AWS credentials in Jenkins
* Creating Pipeline Job from SCM
* Building the job

---

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
* Instance Type → `t2.medium`
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
sudo apt upgrade -y
```

---

# ☕ Install Java (Required for Jenkins)

```bash
sudo apt install openjdk-17-jdk -y
java -version
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

# 🟢 Install Git

```bash
sudo apt install git -y
git --version
```

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
sudo apt install awscli -y
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

# 🛑 To Destroy Infrastructure

Run in terminal:

```bash
terraform destroy
```

Or create a separate Jenkins destroy pipeline.

---

# 📂 Repository Structure

```
Jenkins-Terraform-EKS/
│
├── main.tf
├── variables.tf
├── outputs.tf
└── Jenkinsfile
```

---

# 🔐 Best Practice (Recommended for Production)

Instead of Access Keys:

* Attach IAM Role to EC2
* Give it EKS + EC2 + IAM permissions
* Avoid hardcoding credentials

---

# 🎯 Final Result

After successful build:

* ✅ EKS Cluster Created
* ✅ Node Group Created
* ✅ IAM Roles Attached
* ✅ Infrastructure managed through Terraform
* ✅ CI/CD handled by Jenkins

---

If you want, I can now create:

* 📊 Architecture Diagram
* 🧠 Interview explanation for this project
* 🏆 Resume-ready DevOps project description
* 🔥 Production-grade setup with S3 backend + DynamoDB locking

Just tell me what you need next 🚀
