pipeline {
    agent any
    stages {
        stage('git pull') {
            steps {
                git branch: 'main', url: 'https://github.com/orion-pax77/Jenkins-Terraform-EKS.git'
            }
        }
        stage('terraform init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('terraform validate') {
            steps {
              sh 'terraform validate'
            }
        }
        stage('terraform apply ') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                sh 'terraform apply --auto-approve'
             }
            }
        }
    }
}
