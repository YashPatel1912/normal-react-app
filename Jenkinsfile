pipeline {
    agent any

    environment {
        VERCEL_TOKEN   = credentials('vercel_token')
        VERCEL_URL     = "https://normal-react-app.vercel.app"
        CONTAINER_NAME = "react-app"
        IMAGE_NAME     = "my-react-image"
        EMAIL          = "yashmp01234@gmail.com"
        PORT           = "80"
    }

    stages {

        stage("Clone Repo") {
            steps {
                git branch: "main",
                    url: "https://github.com/YashPatel1912/normal-react-app.git"
            }
        }

        /* ================= DOCKER BUILD ================= */

        stage("Build Docker Image") {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage("Stop & Remove Old Container") {
            steps {
                bat '''
                docker stop %CONTAINER_NAME% || exit 0
                docker rm %CONTAINER_NAME% || exit 0
                '''
            }
        }

        stage("Run Docker Container") {
            steps {
                bat '''
                docker run -d -p %PORT%:%PORT% --name %CONTAINER_NAME% %IMAGE_NAME%
                '''
            }
        }

        /* ================= VERCEL DEPLOY ================= */

        stage("Deploy to Vercel") {
            steps {
                bat 'npx vercel --prod --yes --token=%VERCEL_TOKEN%'
            }
        }
    }

    post {
        success {
            emailext(
                subject: "🚀 React App Deployed Successfully",
                body: """
Hello Yash,

Your React application has been deployed successfully 🎉

🔹 Docker Details:
• Image Name   : ${IMAGE_NAME}
• Container    : ${CONTAINER_NAME}
• Port         : ${PORT}

🔹 Vercel Deployment:
🌐 Live URL: ${VERCEL_URL}

Regards,
Jenkins CI/CD
""",
                to: "${EMAIL}"
            )

            echo "✅ Docker + Vercel deployment successful"
        }

        failure {
            emailext(
                subject: "❌ React App Deployment Failed",
                body: """
Hello Yash,

The Jenkins pipeline failed ❌
Please check the Jenkins console logs.

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}

Regards,
Jenkins CI/CD
""",
                to: "${EMAIL}"
            )

            echo "❌ Pipeline failed"
        }
    }
}
