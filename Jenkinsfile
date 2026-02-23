pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "eu-east-1"
        TF_WORKING_DIR     = "terraform"   // Change if needed
    }

    stages {

        stage('Checkout Repo') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/orion-pax77/Jenkins-Terraform-EKS.git'
            }
        }

        stage('Terraform Init') {
            steps {
                dir("${env.TF_WORKING_DIR}") {
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'aws-eks-creds']
                    ]) {
                        sh 'terraform init -input=false'
                    }
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                dir("${env.TF_WORKING_DIR}") {
                    sh 'terraform validate'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir("${env.TF_WORKING_DIR}") {
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'aws-eks-creds']
                    ]) {
                        sh 'terraform plan -out=tfplan'
                    }
                }
            }
        }

        stage('Approval') {
            steps {
                input message: "Approve Terraform Apply?"
            }
        }

        stage('Terraform Apply') {
            steps {
                dir("${env.TF_WORKING_DIR}") {
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'aws-eks-creds']
                    ]) {
                        sh 'terraform apply -input=false tfplan'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'EKS Infrastructure Created Successfully!'
        }
        failure {
            echo 'Pipeline Failed!'
        }
    }
}
