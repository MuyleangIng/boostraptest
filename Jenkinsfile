// In your Jenkinsfile, add explicit logging:
pipeline {
    agent any
    triggers {
        githubPush()
    }
    stages {
        stage('Debug') {
            steps {
                sh 'env | grep -i git'
                sh 'curl -v https://github.com/MuyleangIng/boostraptest.git'
            }
        }
    }
}
