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
    }
}