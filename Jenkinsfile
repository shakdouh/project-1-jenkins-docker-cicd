pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "my-secure-app"
        CONTAINER_NAME = "secure-app-container"
    }

    stages {
        stage('Checkout') {
            steps {
                // سحب الكود من GitHub
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                // بناء صورة الدوكر
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Security Scan (Trivy)') {
            steps {
                script {
                    echo "Running Vulnerability Scan & Generating Report..."

                    // 1. توليد تقرير HTML (للعرض المرئي داخل جينكينز)
                    // نستخدم القالب الافتراضي المتوفر في Trivy
                    sh "trivy image --format template --template '@/usr/local/share/trivy/templates/html.tpl' -o report.html ${IMAGE_NAME}:latest"

                    // 2. الفحص الأمني الفعلي (Gate)
                    // سيفشل الـ Build فقط إذا وُجدت ثغرات من نوع CRITICAL
                    sh "trivy image --severity CRITICAL --exit-code 1 ${IMAGE_NAME}:latest"
                }
            }
            post {
                always {
                    // نشر التقرير ليظهر في القائمة اليسرى للجوب في جينكينز
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: '.',
                        reportFiles: 'report.html',
                        reportName: 'Trivy Security Report',
                        reportTitles: 'Vulnerability Scan Results'
                    ])
                }
            }
        }

        stage('Deploy') {
            steps {
                // هذه المرحلة لن تعمل إلا إذا نجح الفحص الأمني (صفر ثغرات Critical)
                sh """
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p 3000:3000 ${IMAGE_NAME}:latest
                """
                echo "Application deployed successfully on port 3000."
            }
        }
    }

    post {
        failure {
            // يتم إرسال هذا الإيميل فقط في حالة فشل الـ Pipeline (مثلاً عند وجود ثغرات)
            mail to: 'hesham@example.com', // استبدله بإيميلك الحقيقي
                 subject: "⚠️ Security Alert: Pipeline Failed - ${env.JOB_NAME} [Build #${env.BUILD_NUMBER}]",
                 body: """⚠️ Security Vulnerabilities Detected!
                 
The CI/CD pipeline for ${env.JOB_NAME} has failed during the Security Scan stage.
A critical vulnerability was found that prevents deployment to ensure production safety.

Please check the detailed Trivy Security Report here: ${env.BUILD_URL}Trivy_20Security_20Report/

Build Details:
- Project: ${env.JOB_NAME}
- Build Number: ${env.BUILD_NUMBER}
- Status: FAILURE (Security Gate)"""
        }
        
        success {
            echo "Congratulations! The pipeline completed successfully and the image is secure."
        }
    }
}
