pipeline {
    agent any
     environment {
        USER_NAME = "balance_inq=[${env.BUILD_NUMBER}]"
        
    }
    
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
                withKubeConfig([credentialsId: 'kubeconfigs', serverUrl: 'https://192.168.0.65']) {
                    sh 'mvn --settings configuration/settings.xml fabric8:build -Pkubernetes-deployment -DskipTests -Dfabric8.generator.spring-boot.name=USER_NAME'
               
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
                        app.push("${env.BUILD_NUMBER}")
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
                echo 'Running deploy automation'
                sh 'mvn fabric8:deploy -kubernetes'
                
                
            }
        }
     
    }
}


