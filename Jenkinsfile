pipeline {
    agent any
    
    triggers {
        githubPush()
    }
    
    environment {
        DOCKER_IMAGE = 'html-test-app'
        DOCKER_TAG = 'latest'
    }
    
    stages {
        stage('Debug SCM Info') {
            steps {
                sh 'env | grep -i git'
                sh 'curl -v https://github.com/MuyleangIng/boostraptest.git'
            }
        }
        
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Create Dockerfile') {
            steps {
                script {
                    // Create Dockerfile for testing HTML
                    writeFile file: 'Dockerfile', text: '''
FROM nginx:alpine

# Copy the HTML files to nginx directory
COPY . /usr/share/nginx/html/

# Expose port 80
EXPOSE 80

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost/ || exit 1

CMD ["nginx", "-g", "daemon off;"]
'''
                }
            }
        }
        
        stage('Docker Build') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }
        
        stage('Test Container') {
            steps {
                script {
                    // Start the container
                    sh "docker run -d -p 8080:80 --name test-html ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    
                    // Wait for container to be ready
                    sh 'sleep 10'
                    
                    // Basic test to check if nginx is serving content
                    sh 'curl -f http://localhost:8080 || exit 1'
                }
            }
            post {
                always {
                    // Cleanup: Stop and remove the test container
                    sh 'docker stop test-html || true'
                    sh 'docker rm test-html || true'
                }
            }
        }
        
        stage('Cleanup') {
            steps {
                // Clean up old images
                sh "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG} || true"
                cleanWs()
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        always {
            // Additional cleanup if needed
            sh 'docker system prune -f || true'
        }
    }
}
