pipeline {
  agent any

  parameters {
    string(name: 'TF_DIR', defaultValue: '.', description: 'Fake - ignored')
    choice(name: 'ENV', choices: ['dev'], description: 'Only dev (fake)')
    booleanParam(name: 'SIMULATE_FAILURE', defaultValue: false, description: 'Fake: force a failure')
  }

  environment {
    AWS_REGION = 'eu-west-3'
    TERRAFORM_VERSION = '1.5.0'
  }

  stages {
    stage('Checkout') {
      steps {
        echo "Checking out Terraform repo (fake pipeline)..."
        checkout scm
      }
    }

    stage('Terraform Init') {
      steps {
        sh '''
          set +e
          echo "==> terraform init"
          echo "Initializing the backend..."
          echo "Initializing provider plugins..."
          echo "- Finding hashicorp/aws versions matching \\"~> 5.0\\"..."
          echo "- Installing hashicorp/aws v5.0.0..."
          echo "Terraform has been successfully initialized!"
          sleep 1
          exit 0
        '''
      }
    }

    stage('Terraform fmt') {
      steps {
        sh '''
          set +e
          echo "==> terraform fmt -check -recursive"
          echo "All Terraform configuration files are properly formatted."
          sleep 1
          exit 0
        '''
      }
    }

    stage('Terraform validate') {
      steps {
        sh '''
          set +e
          echo "==> terraform validate"
          echo "Success! The configuration is valid."
          sleep 1
          exit 0
        '''
      }
    }

    stage('Terraform plan') {
      steps {
        sh '''
          set +e
          echo "==> terraform plan -out=tfplan"
          echo "Refreshing Terraform state in-memory prior to plan..."
          echo ""
          echo "Terraform will perform the following actions:"
          echo "  # aws_vpc.main will be created"
          echo "  # aws_eks_cluster.platform will be updated in-place"
          echo "  # aws_db_instance.platform will be created"
          echo ""
          echo "Plan: 3 to add, 1 to change, 0 to destroy."
          echo ""
          echo "Saved the plan to: tfplan"
          sleep 2
          exit 0
        '''
      }
    }

    stage('Terraform apply') {
      steps {
        sh '''
          set +e
          echo "==> terraform apply tfplan"
          echo "Applying (simulation only)..."
          echo "aws_vpc.main: Creating..."
          echo "aws_vpc.main: Creation complete after 2s [id=vpc-FAKE123]"
          echo "aws_eks_cluster.platform: Modifying... [id=platform-eks]"
          echo "aws_eks_cluster.platform: Modifications complete after 3s [id=platform-eks]"
          echo "aws_db_instance.platform: Creating..."
          echo "aws_db_instance.platform: Creation complete after 4s [id=db-FAKE456]"
          echo ""
          echo "Apply complete! Resources: 3 added, 1 changed, 0 destroyed."
          sleep 1
          exit 0
        '''
      }
    }

    stage('Outputs') {
      steps {
        sh '''
          set +e
          echo "==> terraform output"
          echo "cluster_name = \\"platform-eks-dev\\""
          echo "cluster_endpoint = \\"https://FAKE.eks.eu-west-3.amazonaws.com\\""
          echo "rds_endpoint = \\"platform-dev.FAKE.eu-west-3.rds.amazonaws.com\\""
          sleep 1
          exit 0
        '''
      }
    }

    stage('Optional Failure') {
      when { expression { return params.SIMULATE_FAILURE } }
      steps {
        error("FAKE failure triggered by parameter SIMULATE_FAILURE=true")
      }
    }
  }

  post {
    success {
      echo "Terraform pipeline completed successfully."
    }
    failure {
      echo "Terraform pipeline failed."
    }
    always {
      cleanWs()
    }
  }
}
