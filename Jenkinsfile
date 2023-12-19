pipeline {
    agent any
    stages{
        stage('Git Checkout'){
            steps{
                script{
                    git 'https://github.com/nekumar5/jenkins.git'
                }
            }
        },
        stage('UNIT testing'){            
            steps{                
                script{                    
                    sh 'mvn test'
                }
            }
        }
    }
}