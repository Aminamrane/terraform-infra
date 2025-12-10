# Terraform Repository

Repository contenant la définition de l'infrastructure AWS (Infrastructure as Code).

## Structure

```
terraform-repo/
├── main.tf              # Configuration principale
├── variables.tf         # Variables Terraform
├── outputs.tf           # Outputs Terraform
├── vpc.tf               # Configuration VPC
├── eks.tf               # Configuration EKS
├── rds.tf               # Configuration RDS
├── alb.tf               # Configuration ALB
├── security-groups.tf   # Security Groups
├── iam.tf               # IAM Roles
├── secrets-manager.tf   # AWS Secrets Manager
├── s3.tf                # S3 buckets
├── terraform.tfvars.*   # Variables par environnement
├── Jenkinsfile          # Pipeline CI/CD Jenkins
└── README.md
```

## Pipeline Jenkins

La pipeline Jenkins effectue les étapes suivantes :

1. **Checkout** : Récupération du code Terraform
2. **Terraform Init** : Initialisation de Terraform
3. **Terraform Validate** : Validation de la configuration
4. **Terraform Plan** : Planification des changements
5. **Terraform Apply** : Application des changements (déploiement de l'infrastructure)
6. **Output Infrastructure Info** : Affichage des informations de l'infrastructure

## ⚠️ Important

Cette pipeline se déclenche **automatiquement** lors d'un commit et redéploie l'infrastructure complète.

**Ne pas modifier le code Terraform directement** - ce repository contient uniquement la structure. Le code Terraform réel doit être créé séparément.

## Variables d'environnement

- `AWS_REGION` : Région AWS (eu-west-3)
- `TERRAFORM_VERSION` : Version de Terraform à utiliser
- `TERRAFORM_WORKING_DIR` : Répertoire de travail Terraform

## Configuration Jenkins

Créer les credentials suivants dans Jenkins :
- **aws-credentials** : Credentials AWS (Access Key ID / Secret Access Key)

## Infrastructure créée

Cette infrastructure crée :
- VPC avec subnets publics/privés
- Cluster EKS (Elastic Kubernetes Service)
- RDS PostgreSQL Multi-AZ
- Application Load Balancer (ALB)
- Security Groups
- IAM Roles
- AWS Secrets Manager
- S3 buckets (pour logs, backups, etc.)

## Déclenchement automatique

La pipeline se déclenche automatiquement lors d'un commit sur la branche principale.

