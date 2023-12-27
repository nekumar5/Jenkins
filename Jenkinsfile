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
    }
}
