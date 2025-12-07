pipeline {
    agent any
    
    environment {
        APP_NAME = 'simple-django-app'
    }
    
    stages {
        stage('Checkout and Build') {
            steps {
                 git branch: 'main', url: 'https://github.com/manishvishwakarma89/simple-django-app.git'
                sh '''
                    echo "Building Docker image..."
                    docker build -t $APP_NAME .
                    echo "Docker image built successfully!"
                '''
            }
        }
        
        stage('Run Tests in Docker') {
            steps {
                sh '''
                    echo "Running tests inside Docker container..."
                    docker run --rm $APP_NAME python manage.py test --noinput
                    echo "Tests completed successfully!"
                '''
            }
        }
        
        stage('Deploy Application') {
            steps {
                sh '''
                    echo "Stopping any existing container..."
                    docker stop $APP_NAME 2>/dev/null || true
                    docker rm $APP_NAME 2>/dev/null || true
                    
                    echo "Starting new container..."
                    docker run -d -p 8000:8000 --name $APP_NAME $APP_NAME
                    echo "Application deployed successfully!"
                '''
            }
        }
        
        stage('Verify Deployment') {
            steps {
                sh '''
                    echo "Waiting for application to start..."
                    sleep 20
                    
                    echo "Testing health endpoint..."
                    if curl -f http://localhost:8000/health/; then
                        echo "✅ Health check passed!"
                    else
                        echo "❌ Health check failed!"
                        exit 1
                    fi
                    
                    echo "Testing main endpoint..."
                    if curl -f http://localhost:8000/; then
                        echo "✅ Main endpoint working!"
                    else
                        echo "❌ Main endpoint failed!"
                        exit 1
                    fi
                '''
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            echo '🎉 SUCCESS: Application is running at http://localhost:8000'
        }
        failure {
            echo '💥 FAILED: Deployment failed. Check the logs above.'
        }
    }
}
