pipeline {
    agent any
     environment {
        USER_NAME = "balance_inq=[${env.BUILD_NUMBER}]"
        
    }
    
    stages {
        stage('Run') {
             when {
                branch 'master'
            }
            steps {
                echo 'Running build automation'
                sh 'mvn clean install --settings configuration/settings.xml'
                archiveArtifacts artifacts: '**/target/*.jar'
            }
        }
        
       

        
        stage('Login kubernetes') {
           steps {
               withKubeConfig([credentialsId: 'kubeconfigs', serverUrl: 'https://192.168.0.65']) {
                    sh ''
               }
                   
                  
               
           }
       }
//         kubectl get secrets $SECRET_NAME  -o=jsonpath='{.data.token}' -n default | base64 -D
//         https://support.cloudbees.com/hc/en-us/articles/360038636511-Kubernetes-Plugin-Authenticate-with-a-ServiceAccount-to-a-remote-cluster
//         kubectl get secrets $SECRET_NAME  -o=jsonpath='{.data.token}' -n default | base64 -D
//         SECRET_NAME=$(kubectl get serviceaccount default  -o=jsonpath='{.secrets[0].name}' -n default)
        
        
//         stage('Apply Kubernetes Files') {
//             steps {
//                 withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'http://192.168.0.65:6443']) {
                
//                 }
//             }
//         }
        
        stage('build the image') {
             when {
                branch 'master'
            }
            steps {
                echo 'Running build automation'
                
                sh 'mvn --settings configuration/settings.xml fabric8:build -Pkubernetes-deployment -DskipTests -Dfabric8.generator.spring-boot.name=USER_NAME'
               
                
                
            }
        }
        
        stage('Tag and Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        
        stage('deploy') {
             when {
                branch 'master'
            }
            steps {
                echo 'Running deploy automation'
                sh 'mvn fabric8:deploy -kubernetes'
                
                
            }
        }
        
         
       
//         SECRET_NAME=$(kubectl get serviceaccount default  -o=jsonpath='{.secrets[0].name}' -n default)

        


        

        
    
        
    }
}


