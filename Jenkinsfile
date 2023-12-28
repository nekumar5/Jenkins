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

        stage('Quality Gate Status'){                
            steps{                    
                script{                        
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                }
            }
        }
        
        stage('Upload war file to nexus repository'){                
            steps{                    
                script{      
                    def readPomVersion = readMavenPom file: 'pom.xml'                  
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', 
                            file: 'target/Uber.jar', 
                            type: 'jar'
                        ]
                    ], 
                    credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '192.168.59.103:8081/', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'demoapp-release', 
                    version: "${readPomVersion.version}"
                }
            }
        }
    }
}
