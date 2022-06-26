pipeline {
    agent any
     environment {
      
        
        USER_NAME = "balance-inquiry:latest:${env.BUILD_NUMBER}" 
        DOCKER_IMAGE_NAME = "vickvick/balance-inquiry:latest"
        tag = "${env.BUILD_NUMBER}"
        
 
        
    }
//     parameters {
        
//         imageTag(name: '$DOCKER_IMAGE', image: 'balance-inquiry:latest:${env.BUILD_NUMBER}')
        
//       }
    
    stages {
        stage('Run and Compile Code') {
             when {
                branch 'master'
            }
            steps {
                echo 'Running build automation'
                sh 'mvn clean install --settings configuration/settings.xml'
                archiveArtifacts artifacts: '**/target/*.jar'
            }
        }
        
       

        
//         stage('Login kubernetes') {
//            steps {
//                withKubeConfig([credentialsId: 'kubeconfigs', serverUrl: 'https://192.168.0.65']) {
//                     sh ''
//                }
                   
                  
               
//            }
//        }

        

        
        stage('Build balance-inquiry image') {
             when {
                branch 'master'
            }
            steps {
                echo 'Running build automation'
                withKubeConfig([credentialsId: 'kubeconfigs', serverUrl: 'https://192.168.0.65:6443']) {
                    
//                      sh 'mvn --settings configuration/settings.xml fabric8:build -Pkubernetes-deployment -DskipTests -Dfabric8.generator.spring-boot.name=${env.USER_NAME}'
                    sh 'mvn --settings configuration/settings.xml fabric8:build -Pkubernetes-deployment -DskipTests -Dfabric8.generator.spring-boot.name="balance-inquiry"'
//                     sh 'mvn docker push'
                    
                    
                }
                
            }
        }
        
        stage('Tag and Push Image to DockerHub') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {    
                      
//                         
//                         sh 'docker tag ${balance-inquiry:latest}:${BUILD_NUMBER} ${balance-inquiry}:latest' 
//                         sh 'docker tag balance-inquiry:latest balance-inquiry":{$env.tag}" '
                        sh "docker tag balance-inquiry:latest 'balance-inquiry:${env.tag}'"
//                         "my-image:${env.BUILD_ID}"
//                         docker tag balance-inquiry:latest balance -inquiry:$env.tag
                        
                        
                      
                        sh 'docker images' 
                        
//                         
//                         sh 'docker tag balance-inquiry:latest vickvick/latest' 
//                          
//                         sh 'docker push balance-inquiry:latest https://registry.hub.docker.com/balance-inquiry:latest'
//                          sh 'docker push '
                        
                        
//                         app.push("balance-inquiry:latest vickvick/balance-inquiry")
                        app.push("balance-inquiry:${env.tag}")
                        app.push("latest")
                    }
                }
            }
        }
        
       

        stage('Deploy Balance-Inquiry Service') {
             when {
                branch 'master'
            }
            steps {
                echo 'Running build automation'
                withKubeConfig([credentialsId: 'kubeconfigs', serverUrl: 'https://192.168.0.65:6443']) {
                    

                    sh 'mvn fabric8:deploy -kubernetes'
                       
                    
                    
                }
                
            }
        }
     
    }
}


