pipeline {
    // agent any
    agent {
        label 'Jenkins-Agent'
    }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    stages{
        stage('Git Checkout'){
            steps{
                script{
                    git credentialsId: 'git', url: 'https://github.com/nekumar5/jenkins.git'
                }
            }
        }

        stage('UNIT testing'){
            steps{
                script{
                    sh 'mvn test'
                }
            }
        }

        stage('Integration testing'){
            steps{
                script{
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }

        stage('Maven build'){            
            steps{                
                script{                    
                    sh 'mvn clean install'
                }
            }
        }

        stage('Static code analysis'){            
            steps{                
                script{                    
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }                    
                }
            }
    }
}
