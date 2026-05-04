pipeline {
    agent any

    tools {
        jdk 'jdk21'
        nodejs 'node25'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        SONAR_TOKEN = credentials('sonar-token')
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Bhawna2424/Amazon-app-Deployment-using-terraform-and-jenkins'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh """
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectName=Amazon \
                    -Dsonar.projectKey=Amazon \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://13.127.248.100:9000 \
                    -Dsonar.login=${SONAR_TOKEN}
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t amazon-clone ."
            }
        }

        stage('Docker Run') {
            steps {
                sh "docker run -d -p 3000:3000 amazon-clone"
            }
        }
    }
}
