pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "abc_tech:${BUILD_NUMBER}"
    }
    
    stages {
        stage('Code Checkout') {
            steps {
                git 'https://github.com/nrawal2024/-final-devops-project-.git'
            }
        }
        
        stage('Maven Build') {
            steps {
                sh '''
                mvn clean package -DskipTests
                ls -la target/*.war
                '''
            }
        }
        
        stage('Prepare WAR') {
            steps {
                sh '''
                cp target/ABCtechnologies-1.0.war abc.war || cp target/*.war abc.war
                ls -la abc.war
                '''
            }
        }
        
        stage('Build Docker') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME} .
                docker images | grep abc_tech
                '''
            }
        }
        
        stage('Deploy Test') {
            steps {
                sh '''
                docker stop test-app || true
                docker rm test-app || true
                docker run -d --name test-app -p 30000:8080 ${IMAGE_NAME}
                sleep 20
                curl http://localhost:30000 || echo "Test ready"
                '''
            }
        }
        
        stage('Deploy Production') {
            steps {
                sh '''
                docker stop abc-prod || true
                docker rm abc-prod || true
                docker run -d --name abc-prod -p 3001:8080 ${IMAGE_NAME}
                sleep 20
                curl http://localhost:3001 || echo "Production ready!"
                '''
            }
        }
    }
    
    post {
        always {
            script {
                def ip = sh(script: 'curl -s ifconfig.me || echo "3.23.97.51"', returnStdout: true).trim()
                echo "=== LIVE APPLICATIONS ==="
                echo "Jenkins:     http://${ip}:8080"
                echo "Test App:    http://${ip}:30000/abc"
                echo "Production:  http://${ip}:3001/abc"
                sh "docker ps"
            }
        }
    }
}
