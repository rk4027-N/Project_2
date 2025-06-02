pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/rk4027-N/Project_2.git'
            }
        }

        stage('Terraform Apply') {
            steps {
                script {
                    dir('terraform') {
                        sh 'terraform init'
                        sh 'terraform apply -auto-approve'
                    }
                }
            }
        }

        stage('Ansible Deploy') {
            steps {
                script {
                    sh 'ansible-playbook -i terraform/hosts ansible/deploy.yml'
                }
            }
        }

        stage('Docker Build and Push') {
            steps {
                script {
                    sh 'docker build -t my-web-app .'
                    sh 'docker push my-web-app'
                }
            }
        }

        stage('Test and Deploy') {
            steps {
                script {
                    sh 'curl http://$(terraform output -raw instance_public_ip)'
                }
            }
        }
    }
}
