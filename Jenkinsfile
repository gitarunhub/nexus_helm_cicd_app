pipeline {
    agent any
    environment {
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("sonar quality status") {
            agent{
                docker{
                    image 'maven'
                }
            }
            steps{
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-server') {
                        sh " mvn clean package sonar:sonar"
                    }
                    
                }
                
            }
        }
        stage("quality gate status"){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-server'
                }
            }
        }
        stage("mvn install"){
            steps{
                script{
                    sh "mvn clean install"
                }
            }
        }
        stage("docker image"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus_pass', variable: 'nexus_cred')]) {
                        sh'''
                        docker build -t 192.168.1.24:8085/springboot:$VERSION .
                        docker login -u admin -p $nexus_cred 192.168.1.24:8085 
                        docker push 192.168.1.24:8085/springboot:${VERSION}
                        docker rmi 192.168.1.24:8085/springboot:${VERSION}
                        '''
                    }
                    
                }
            }
        }
        stage(ssh_kube){
            steps{
                script{
                    sshagent(['kube']) {
                        sh 'ssh -o StrictHostKeyChecking=no kube@192.168.1.21 uname -a'
                    }
                }
            }
        }
    }
}