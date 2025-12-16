pipeline {
  agent any

  parameters {
    string(name: 'TF_DIR', defaultValue: '.', description: 'Dossier Terraform')
  }

  environment {
    TERRAFORM_VERSION = '1.5.0'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Install Terraform (if missing)') {
      steps {
        sh '''
          set -e
          if command -v terraform >/dev/null 2>&1; then
            terraform version | head -n1
            exit 0
          fi

          apt-get update -y
          apt-get install -y curl unzip ca-certificates

          curl -fsSL -o /tmp/terraform.zip \
            "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip"
          unzip -o /tmp/terraform.zip -d /usr/local/bin
          terraform version | head -n1
        '''
      }
    }

    stage('Terraform fmt (check only)') {
      steps {
        sh '''
          set -e
          cd "${TF_DIR}"
          terraform fmt -check -recursive
        '''
      }
    }

    stage('Terraform validate (NO BACKEND / NO NETWORK)') {
      steps {
        sh '''
          set -e
          cd "${TF_DIR}"

          # init SANS backend => ne contacte rien, n'écrit pas dans S3/Dynamo, etc.
          terraform init -backend=false -input=false -no-color

          # validate ne touche pas AWS, c'est juste du parsing
          terraform validate -no-color
        '''
      }
    }

    stage('DRY RUN (no plan, no apply)') {
      steps {
        sh '''
          echo "✅ DRY RUN OK: fmt + validate pass"
          echo "🚫 Aucun terraform plan / apply n'a été exécuté."
        '''
      }
    }
  }

  post {
    always { cleanWs() }
  }
}