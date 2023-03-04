pipeline {
    agent any
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

        stage("maven build") {
            agent {
                docker {
                    image 'maven'
                }
            }
            steps{
                script {
                    sh "mvn clean install"
                }
            }
        }
    }
}