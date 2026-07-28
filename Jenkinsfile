pipeline {
    agent any

    environment {
        IMAGE_NAME     = 'mysite'
        CONTAINER_NAME = 'mysite'
        HOST_PORT      = '8090'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%BUILD_NUMBER% -t %IMAGE_NAME%:latest ."
            }
        }

        stage('Deploy') {
            steps {
                bat "docker rm -f %CONTAINER_NAME% || exit 0"
                bat "docker run -d -p %HOST_PORT%:80 --restart unless-stopped --name %CONTAINER_NAME% %IMAGE_NAME%:latest"
            }
        }

        stage('Smoke test') {
            steps {
                bat "powershell -Command \"Start-Sleep -Seconds 3; \$r = Invoke-WebRequest -Uri http://localhost:%HOST_PORT% -UseBasicParsing; if (\$r.StatusCode -ne 200) { exit 1 }\""
            }
        }
    }

    post {
        success { echo "Deployed to http://localhost:${env.HOST_PORT}" }
        failure { echo 'Build failed - container left as-is' }
    }
}