pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        TF_WORKSPACE = "default"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/orion-pax77/Jenkins-Terraform-EKS.git'
            }
        }

        stage('Terraform Init') {
            steps {
                sh '''
                terraform init \
                  -backend-config="region=${AWS_REGION}"
                '''
            }
        }

        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh '''
                    terraform plan \
                      -var "aws_region=${AWS_REGION}" \
                      -out=tfplan
                    '''
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh 'terraform apply -auto-approve tfplan'
                }
            }
        }

    }

    post {
        always {
            cleanWs()
        }
        failure {
            echo "Build failed!"
        }
    }
}
