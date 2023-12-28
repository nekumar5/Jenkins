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
                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"               
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
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"
                }
            }
        }

        stage('Docker Image Build'){                
            steps{                    
                script{                        
                    sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID nekumar5/$JOB_NAME:v1.$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID nekumar5/$JOB_NAME:latest'
                }
            }
        }

         stage('Push Image to Docker'){                
            steps{                    
                script{                        
                    withCredentials([string(credentialsId: 'docker_hub_cred', variable: 'docker_hub')]) {
                            sh "dockerhub cred ${docker_hub}"
                            sh 'docker login -u nekumar5 -p ${docker_hub}'
                            sh 'docker image push nekumar5/$JOB_NAME:v1.$BUILD_ID'
                            sh 'docker image pusg nekumar5/$JOB_NAME:latest'
                        }
                }
            }
        }
    }
}
