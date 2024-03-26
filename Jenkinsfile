pipeline {
    agent {
        node {
            label 'AGENT-1'
        }
    }
    environment { 
        packageVersion = ''
        nexusURL = '172.31.20.61:8081'
    }
    options {
        timeout(time: 20, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    parameters {
        string(name: 'version', defaultValue: '${params.version}', description: 'What is the artifact version?')
        string(name: 'environment', defaultValue: 'dev', description: 'What is environment?')
        booleanParam(name: 'Destroy', defaultValue: 'false', description: 'What is Destroy?')
        booleanParam(name: 'Create', defaultValue: '', description: 'What is Create?')
    }
    // build
    stages {
        stage('Print version') {
            steps {
                sh """
                    echo "version: ${params.version}"
                    echo "environment: ${params.environment}"
                    echo "Create: ${params.Create}"
                    echo "Destroy: ${params.Destroy}"
                """
            }
        }

        stage('Init') {
            steps {
                sh """
                    cd terraform
                    terraform init --backend-config=${params.environment}/backend.tf -reconfigure
                """
            }
        }

        stage('Plan') {
            when{
                expression{
                    Create = ${params.Create}
                }
            }
            steps {
                sh """
                    cd terraform
                    terraform plan -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}"
                """
            }
        }

        // stage('Apply') {
        //     when{
        //         expression{
        //             params.Create
        //         }
        //     }
        //     steps {
        //         sh """
        //             cd terraform
        //             terraform apply -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}" -auto-approve
        //         """
        //     }
        // }
        // stage('Destroy') {
        //     when{
        //         expression{
        //             params.Destroy
        //         }
        //     }
        //     steps {
        //         sh """
        //             cd terraform
        //             terraform destroy -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}" -auto-approve
        //         """
        //     }
        // }
        
    }
    // post build
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        failure { 
            echo 'this runs when pipeline is failed, used generally to send some alerts'
        }
        success{
            echo 'I will say Hello when pipeline is success'
        }
    }
}