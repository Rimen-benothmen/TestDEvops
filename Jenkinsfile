pipeline {
    agent any

    environment {
        IMAGE_NAME = "rimen-devops-test"
        DOCKERHUB_USER = "ton-utilisateur-dockerhub"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:Rimen-benothmen/TestDEvops.git'
            }
        }


        stage('SonarQube Analysis') {
            environment {
                SONAR_SCANNER_HOME = tool 'SonarQubeScanner'
            }
            steps {
                sh '''
                ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                  -Dsonar.projectKey=TestDEvops \
                  -Dsonar.sources=. \
                  -Dsonar.host.url=http://localhost:9000 \
                  -Dsonar.login=ton-token-sonarqube
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-password', variable: 'DOCKERHUB_PASS')]) {
                    sh "echo ${DOCKERHUB_PASS} | docker login -u ${DOCKERHUB_USER} --password-stdin"
                    sh "docker tag ${IMAGE_NAME} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
                    sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deploy & Run Container') {
            steps {
                sh "docker run -d -p 8080:8080 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        success {
            echo "Pipeline exécuté avec succès !"
        }
        failure {
            echo "Le pipeline a échoué."
        }
    }
}

