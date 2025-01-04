pipeline{
    agent {
        // run on the AGENT-1 node
        node {
            label 'AGENT-1'
        }
    }
    options {
        ansiColor('xterm')
        timeout(time: 1, unit: 'HOURS')
        disableConcurrentBuilds()
    }
       
    parameters {
        string(name: 'version', defaultValue: '', description: 'what is the artifact version?')
        string(name: 'environment', defaultValue: '', description: 'what is the Environment?')

    }
    
    //build stages
    stages {
        stage('Print Version') {
            steps {
                sh """
                    echo "current build version is ${params.version}"
                    echo "Environment is ${params.environment}"
                """
            }
        }
// the following will init the backend config with the env directory and reconfigures the environment
        stage('Terraform Init') {
            steps {
                sh """
                   echo 'Terraform Init'
                   pwd
                   cd terraform
                   pwd
                    terraform init --backend-config=${params.environment}/backend.tf -reconfigure
                """
            }
        }
        stage('Terraform destroy') {
            steps {
                sh """
                   echo 'Terraform apply'
                   pwd
                   cd terraform
                   pwd
                   terraform destroy -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}" -auto-approve
                """
            }
        }
    }

    // post section
    post {
        always {
            echo 'This will always run irrespective of status of the pipeline'
            deleteDir()
        }
        failure {
            echo 'This will run only if the pipeline is failed, We use thsi for alerting the team' 
        }
        success {
            echo 'This will run only if the pipeline is successful'
        }
    }
}
