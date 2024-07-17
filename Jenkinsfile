pipeline {
    agent {
       node {
         label 'agent-1' 
     }
}
//    environment {   
//    //    packageVersion = ' '
// //       nexusURL = '172.31.36.245:8081'
// }
    options {
        timeout(time: 1, unit: 'HOURS') 
        disableConcurrentBuilds()
         ansiColor('xterm')
       
    }


   parameters {
        string(name: 'version', defaultValue: ' ', description: 'What is the artifact version?')
        string(name: 'environment', defaultValue: 'dev', description: 'What is the environment?')
        booleanParam(name: 'Destroy', defaultValue: 'false', description: 'What is the destroy?')
        booleanParam(name: 'Create', defaultValue: 'false', description: 'What is the create?')
   }
// //   build
        stages {
          stage('Print version') {
            steps {
                sh """
                    echo "version: ${params.version}"
                    echo "environment: ${params.environment}"
                """
            }
        }
          stage('init') {
            steps {
                sh """
                   cd terraform 
                   terraform init --backend-config=${params.environment}/backend.tf -reconfigure  

                """
            }
        }

           stage('plan') {
            steps {
                sh """
                   cd terraform 
                   terraform plan -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}"
                   """
            }
        }


        stage('apply') {
            when{
                expression{
                    params.Create
                }
            }
            steps {
                sh """
                   cd terraform 
                   terraform apply -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}" -auto-approve
                   """
            }
        }
         stage('Destroy') {
            when{
                expression{
                    params.Destroy
                }
            }
            steps {
                sh """
                   cd terraform 
                   terraform destroy -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}" -auto-approve
                   """
            }
        }
    }
    
    
    //  post build 
     post { 
        always { 
            echo 'I will always say Hello again!'
             deleteDir()
        }
     

        failure {
            echo ' this runs when pipeline is failed used generally to send alerts'
        }
      
       success { 
            echo 'I will always say Hello when pipeline is success'
        }
   
    }

}