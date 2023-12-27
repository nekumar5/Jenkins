pipeline {
    agent any
    stages{
        stage('Git Checkout'){
            steps{
                script{
                    git credentialsId: 'git', url: 'https://github.com/nekumar5/jenkins.git'
                }
            }
        }
    }
}
