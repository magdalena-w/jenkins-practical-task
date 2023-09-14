pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY_MR = "magdalena01/mr"
        DOCKER_REGISTRY_MAIN = "magdalena01/main"
    }

    stages {
        stage('Checkstyle') {
            steps {
                sh './mvnw checkstyle:checkstyle'
                archiveArtifacts artifacts: '**/target/checkstyle-result.xml', allowEmptyArchive: true
            }
        }

        stage('Test') {
            steps {
                sh './mvnw test'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean install -DskipTests'
            }
        }

        stage('CreateDockerImageMR') {
            steps {
                script {
                    sh 'docker build -t ${DOCKER_REGISTRY_MR}:${GIT_COMMIT:0:7} -f Dockerfile .'
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                    sh 'docker push ${DOCKER_REGISTRY_MR}:${GIT_COMMIT:0:7}'
                    }
                }
            }
        }

        stage('CreateDockerImageMain') {
            when {
                branch 'main'
            }
            steps {
                script {
                    sh 'docker build -t ${DOCKER_REGISTRY_MAIN}:latest -f Dockerfile .'
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        sh 'docker push ${DOCKER_REGISTRY_MAIN}:latest'
                    }
                }
            }
        }
    }
}
