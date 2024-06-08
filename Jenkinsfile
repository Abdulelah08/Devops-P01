
pipeline {

    parameters {
        string(name: 'environment', defaultValue: 'terraform', description: 'Workspace/environment file to use for deployment')
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')

    }


     environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

   agent  any
        options {
                timestamps ()
                ansiColor('xterm')
            }
    stages {
        stage('checkout') {
            steps {
                 script{
                        dir("terraform")
                        {
                            git "https://github.com/Abdulelah08/Devops-P01"
                        }
                    }
                }
            }

        stage('Plan') {
            steps {
                bat 'pwd;cd terraform/Devops-P01 ; terraform init -input=false'
                bat 'pwd;cd terraform/Devops-P01 ; terraform workspace new ${environment}'
                bat 'pwd;cd terraform/Devops-P01 ; terraform workspace select ${environment}'
                bat "pwd;cd terraform/Devops-P01 ; terraform plan -input=false -out tfplan "
                bat 'pwd;cd terraform/Devops-P01 ; terraform show -no-color tfplan > tfplan.txt'
            }
        }
        stage('Approval') {
           when {
               not {
                   equals expected: true, actual: params.autoApprove
               }
           }

           steps {
               script {
                    def plan = readFile 'terraform/Devops-P01/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
               }
           }
       }

        stage('Apply') {
            steps {
                bat "pwd;cd terraform/Devops-P01 ; terraform apply -input=false tfplan"
            }
        }
    }

  }