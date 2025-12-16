pipeline {
  agent any

  environment {
    TERRAFORM_VERSION = '1.5.0'
    TF_DIR = '.'
  }

  stages {
    stage('Checkout') { steps { checkout scm } }

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

    stage('Fmt (info only, never fail)') {
      steps {
        sh '''
          cd "${TF_DIR}"
          terraform fmt -check -recursive || true
          echo "fmt check done (ignored if differences)"
        '''
      }
    }

    stage('Validate (no backend, best effort)') {
      steps {
        sh '''
          cd "${TF_DIR}"
          terraform init -backend=false -input=false -no-color || true
          terraform validate -no-color || true
          echo "validate done (best effort, no fail)"
        '''
      }
    }

    stage('DRY RUN') {
      steps {
        echo "✅ DRY RUN: No plan, no apply, nothing changed in AWS."
      }
    }
  }

  post { always { cleanWs() } }
}