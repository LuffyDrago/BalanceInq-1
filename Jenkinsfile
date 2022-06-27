pipeline {
    agent any
     environment {
              
        tag = "${env.BUILD_NUMBER}"
        USER_NAME = "vickvick"                        
        
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
        
       
 
        stage('Build balance-inquiry image') {
             when {
                branch 'master'
            }
            steps {
                echo 'Running build automation'
                withKubeConfig([credentialsId: 'kubeconfigs', serverUrl: 'https://192.168.0.65:6443']) {
                    
                    sh 'mvn --settings configuration/settings.xml fabric8:build -Pkubernetes-deployment -DskipTests -Dfabric8.generator.spring-boot.name="balance-inquiry"'

                    
                    
                }
                
            }
        }
        
        stage('Tag and Push Image to DockerHub') {
            when {
                branch 'master'
            }
            steps {
                script {
                        def repo_name= "vickvick/balance-inquiry" 
                        sh "docker tag balance-inquiry:latest '${repo_name}:${env.tag}'"
                        
                        docker.withRegistry('', 'docker_hub_login') {                                                                             
                        sh 'docker images'   
                        sh "docker push '${repo_name}:${env.tag}'"
                        
                    
                    }
                }
            }
        }
        
         

       

        stage('Deploy Balance-Inquiry Service') {
             when {
                branch 'master'
            }
            steps {
                script {
                        def repo_name= "vickvick/balance-inquiry" 
                        echo 'Running build automation'
                        withKubeConfig([credentialsId: 'kubeconfigs', serverUrl: 'https://192.168.0.65:6443']) {

                            sh 'kubectl config set-context --current --namespace=balance-inquiry' 
                            sh "mvn --settings configuration/settings.xml fabric8:deploy -Pkubernetes-deployment -DskipTests -Dfabric8.generator.spring-boot.name='${repo_name}:${env.tag}'" 
       
                        }
                    
                    
                }
                
            }
        }
     
    }
}

