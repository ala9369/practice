pipeline {
    agent any
    
    tools {
     maven 'maven 3.9.0'   
    }
    stages{
        stage(checkout){
            steps{
                git branch: 'main', url: 'https://github.com/ala9369/practice.git'
                
            }
        }
        stage(build){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('build docker image'){
            steps{
                sh "docker build -t ala9369/practice:${BUILD_NUMBER} ."           
                }
        }
        stage('push docker image'){
            steps{
                withCredentials([string (credentialsId: 'docker_hub_pwd', variable: 'docker_hub_pwd')]) {
                
               sh "docker login -u ala9369 -p ${docker_hub_pwd}"
     }
     sh "docker push ala9369/practice:${BUILD_NUMBER}"
            }
        }
         stage("deploy_app"){
            steps {
              script {

                 
                 sshagent(['deploy_application']) {
                 sh 'ssh -o StrictHostKeyChecking=no ec2-user@3.110.107.43 docker pull ala9369/practice:${BUILD_NUMBER} '
                 sh 'ssh -o StrictHostKeyChecking=no ec2-user@3.110.107.43 docker rm -f webserver || true'
                 sh 'ssh -o StrictHostKeyChecking=no ec2-user@3.110.107.43 docker run -d -p 8080:8080 --name webserver ala9369/practice:${BUILD_NUMBER}'               
            }
              }  
              }
                }
            }
            post{
                    success{
                       emailext body: 'success', recipientProviders: [builduser()], subject: 'report', to: 'rql1kor@bosch.com
                    }
                }
 }
