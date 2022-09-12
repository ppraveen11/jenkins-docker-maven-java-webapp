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
                          
           withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWD', variable: 'docker_hub_psswd_var')]) {
    // some block

                       sh "sudo docker login  -u  ppraveen11  -p  $docker_hub_passwd_var" 

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
                 sh 'ssh  -o  StrictHostKeyChecking=no ec2-user@43.205.95.151' 
                 
                  sh 'sudo docker rm -f javaweb'
                  
                  sh "sudo docker run -d  -p  1234:8080  --name  javaweb   ppraveen11/javaweb:${BUILD_TAG}" 
                
                 
                }
        }   }
            
            stage('QAT/TEST'){
                
                
                 steps{
                        //sh 'curl --silent  http://43.205.135.213.231:1234/java-web-app/ | grep India'
                        
                        echo "hello its QAT"
                     
                   }
            }
                
          stage('approved') {
            steps {
                
            
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }
            
                
            }
        }
        }
          stage("Deploying WEBAPP in prod env "){ 
            steps{ 
                
                sshagent(['FORQAT']) {
              
                    sh 'ssh  -o  StrictHostKeyChecking=no ec2-user@43.205.95.151' 
                 
                    sh 'sudo docker rm -f javaweb'
                  
                    sh "sudo docker run -d  -p  1233:8080  --name  javaweb   ppraveen11/javaweb:${BUILD_TAG}" 
                
                 
                }
        }   }
      


        }
                 
    } 
