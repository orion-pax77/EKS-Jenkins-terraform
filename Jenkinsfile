pipeline {
    agent any

    environment {
        AWS_REGION = "eu-north-1"
    }

    stages {

        stage('Deploy EKS with Terraform') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds'
                ]]) {
                    sh '''
                        terraform init
                        terraform plan -auto-approve
                        terraform apply -auto-approve 
                    '''
                }
            }
        }
    }
}
