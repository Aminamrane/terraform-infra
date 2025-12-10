pipeline {
    agent any
    
    environment {
        AWS_REGION = 'eu-west-3'
        TERRAFORM_VERSION = '1.5.0'
        TERRAFORM_WORKING_DIR = '.'
    }
    
    stages {
        stage('Checkout') {
            steps {
                script {
                    echo "Checking out Terraform code from ${env.GIT_URL}"
                    checkout scm
                }
            }
        }
        
        stage('Terraform Init') {
            steps {
                script {
                    echo "Initializing Terraform..."
                    withCredentials([
                        string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                        string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
                    ]) {
                        sh """
                            export AWS_ACCESS_KEY_ID=\${AWS_ACCESS_KEY_ID}
                            export AWS_SECRET_ACCESS_KEY=\${AWS_SECRET_ACCESS_KEY}
                            export AWS_DEFAULT_REGION=${AWS_REGION}
                            
                            cd ${TERRAFORM_WORKING_DIR}
                            terraform init
                        """
                    }
                }
            }
        }
        
        stage('Terraform Validate') {
            steps {
                script {
                    echo "Validating Terraform configuration..."
                    sh """
                        cd ${TERRAFORM_WORKING_DIR}
                        terraform validate
                    """
                }
            }
        }
        
        stage('Terraform Plan') {
            steps {
                script {
                    echo "Running Terraform plan..."
                    withCredentials([
                        string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                        string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
                    ]) {
                        sh """
                            export AWS_ACCESS_KEY_ID=\${AWS_ACCESS_KEY_ID}
                            export AWS_SECRET_ACCESS_KEY=\${AWS_SECRET_ACCESS_KEY}
                            export AWS_DEFAULT_REGION=${AWS_REGION}
                            
                            cd ${TERRAFORM_WORKING_DIR}
                            terraform plan -out=tfplan
                        """
                    }
                }
            }
        }
        
        stage('Terraform Apply') {
            steps {
                script {
                    echo "Applying Terraform configuration..."
                    echo "⚠️ This will deploy/update the complete infrastructure!"
                    withCredentials([
                        string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                        string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
                    ]) {
                        sh """
                            export AWS_ACCESS_KEY_ID=\${AWS_ACCESS_KEY_ID}
                            export AWS_SECRET_ACCESS_KEY=\${AWS_SECRET_ACCESS_KEY}
                            export AWS_DEFAULT_REGION=${AWS_REGION}
                            
                            cd ${TERRAFORM_WORKING_DIR}
                            terraform apply -auto-approve tfplan
                        """
                    }
                }
            }
        }
        
        stage('Output Infrastructure Info') {
            steps {
                script {
                    echo "Retrieving infrastructure outputs..."
                    withCredentials([
                        string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                        string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
                    ]) {
                        sh """
                            export AWS_ACCESS_KEY_ID=\${AWS_ACCESS_KEY_ID}
                            export AWS_SECRET_ACCESS_KEY=\${AWS_SECRET_ACCESS_KEY}
                            export AWS_DEFAULT_REGION=${AWS_REGION}
                            
                            cd ${TERRAFORM_WORKING_DIR}
                            terraform output
                        """
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo "✅ Terraform pipeline completed successfully!"
            echo "Infrastructure deployed/updated in ${AWS_REGION}"
        }
        failure {
            echo "❌ Terraform pipeline failed!"
        }
        always {
            cleanWs()
        }
    }
}

