pipeline {
    agent any

    environment{
        VERCEL_TOKEN = credentials('vercel_token')
        CONTAINER_NAME = "react-app"
        IMAGE_NAME = "my-react-image"
        EMAIL = "yashmp01234@gmail.com"
        PORT = "5173"
    }

    stages {
        stage("Clone Repo") {
            steps {
                git branch : "main" , url : "https://github.com/YashPatel1912/normal-react-app.git"
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Build React App') {
            steps {
                bat 'npm run build'
            }
        }

        /* ================= DOCKER BUILD ================= */

        stage("Build Docker image") {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage("Stop & Remove previous container") {
            steps {
                bat '''
                    docker stop %CONTAINER_NAME% || true
                    docker rm %CONTAINER_NAME% || true

                '''
            }
        }
        
        stage("Docker container Run") {
            steps {
                bat '''
                    docker run -d -p %PORT%:%PORT% --name $CONTAINER_NAME %IMAGE_NAME%

                '''
            }
        }

        stage("Send Email Notification") {
            steps {
                emailext(
                subject: "React App Deployed Successfully",
                body: "Your React app is running on Docker.",
                to: "${EMAIL}"
            )
            }
        }

         /* ================= VERCEL DEPLOY ================= */

         stage('Deploy to Vercel') {
            steps {
                bat 'npx vercel --prod --yes --token=%VERCEL_TOKEN%'
            }
        }
    }

    post {
        success {
            echo "✅ Docker + Vercel deployment successful"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}