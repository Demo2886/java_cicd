pipeline {
    agent any //agent {  label "master"    }
    
    stages {
        // Step 1
        stage('SCM') {
                steps {
                    git 'https://github.com/Demo2886/java_cicd.git'
                }        
        }
        // Step 2
        stage('Build by Maven') {
                steps {
                    sh 'mvn clean package'
                }
        }
        
        // Step 3
        stage('Build docker image') {
                steps {
                    sh "docker build -t jokercat2886/test-jenkins:${BUILD_NUMBER} ."
                }
        }
        
        // Step 4
        stage('Push docker image') {
                steps {
                    withCredentials([string(credentialsId: 'Docker_PASS', variable: 'Docker_PASS')]) {
                    sh "docker login -u jokercat2886 -p $Docker_PASS"
                    }
                    sh "docker push jokercat2886/test-jenkins:${BUILD_NUMBER}"
                }
        }
        
        // Step 5 
        stage('Deploy Java App in  Dev Env') {
                steps {
                        sh "docker rm -f jokercat2886"
                        sh "docker run  -d -p 1222:8080 --name jokercat2886 jokercat2886/test-jenkins:${BUILD_NUMBER}"
                }
        }
        
        // Step 6  in  Redhat CLI 1 
        stage('Deploy Java in QA/Test Env') {
            steps {
                    // sshagent(['QA_ENV_SSH_CRED']) {
                        // sh "ssh root@192.168.43.229 docker rm -f myjavaapp"
                        // sh "ssh root@192.168.43.229 docker run  -d -p 8080:8080 --name myjavaapp jokercat2886/test-jenkins:${BUILD_NUMBER}"
            sh "docker rm -f myjavaappqatestenv"            
            sh "docker run  -d -p 1223:8080 --name myjavaappqatestenv jokercat2886/test-jenkins:${BUILD_NUMBER}"           
                    // }
            }
        }
            
        stage('QAT Test') {
            steps {
        	// bcz tomcat take some sec. to display data , so apply some delay here        
                retry(30) {
                    sh 'curl --silent http://localhost:1223/java-web-app/ |  grep India'
                }   
            }
        }
        
        // Step  in Redhat 8 CLI 2
        stage('Deploy webAPP in Prod Env') {
            steps {
               
            //   sshagent(['QA_ENV_SSH_CRED']) {                    
                        // sh "ssh root@192.168.43.229 docker rm -f myjavaapp"
                        // sh "ssh root@192.168.43.229 docker run  -d -p 8080:8080 --name myjavaapp jokercat2886/test-jenkins:${BUILD_NUMBER}"                   
                // }
                sh "docker rm -f myjavaappprodenv"
                sh "docker run  -d -p 1224:8080 --name myjavaappprodenv jokercat2886/test-jenkins:${BUILD_NUMBER}"  
            }
        }
    }
}      
