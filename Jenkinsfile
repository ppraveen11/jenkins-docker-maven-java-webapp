pipeline {
    
    agent {
        
        label "mavenslave"
    } 

    stages {
        stage('SCM') {
            steps {
                    git 'https://github.com/ppraveen11/jenkins-docker-maven-java-webapp.git'
            }
        }
        
        
        stage("build package by maven"){
                   steps{
                      sh 'mvn clean package'
                   }
                  } 
        
        stage("build docker image"){
                    
                      steps{ 
                          
                          sh "sudo docker build  -t   ppraveen11/javaweb:${BUILD_TAG}  ."
                          
                      }
        }
        
        stage("push image to docker hub"){
                    
           steps{ 
                          
            withCredentials([string(credentialsId: 'DOCKER-HUB-PASSWD', variable: 'docker_hub_var')]) {
    // some block

                sh " sudo docker login  -u  ppraveen11  -p  $docker_hub_var " 
                  
              }
                          
           sh "sudo docker push   ppraveen11/javaweb:${BUILD_TAG}"
              
                           
   }
        }
        
        stage("Deploying WEBAPP in dev env "){ 
            steps{ 
                
                sh 'sudo docker rm -f javaweb'
                
                sh "sudo docker run -d  -p  1234:8080  --name  javaweb    ppraveen11/javaweb:${BUILD_TAG} "  
                
            } }
            
        stage("Deploying WEBAPP in QAT/TEST env "){ 
            steps{ 
                
                sshagent(['FORQAT']) {
    // some block
                 sh 'ssh  -o  StrictHostKeyChecking=no ec2-user@13.126.178.231' 
                 
                  sh 'sudo docker rm -f javaweb'
                  
                  sh "sudo docker run -d  -p  1234:8080  --name  javaweb   ppraveen11/javaweb:${BUILD_TAG}" 
                
                 
                }
        }   }
            
            stage('QAT/TEST'){
                
                
                 steps{
                        //sh 'curl --silent  http://13.126.178.231:1234/java-web-app/ | grep India'
                        
                        echo "hello its QAT"
                     
                   }
            }
                
        }
                 
    } 