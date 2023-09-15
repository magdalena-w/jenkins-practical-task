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
                    def gitCommit = currentBuild.displayName
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        def dockerImage = docker.build("$DOCKER_REGISTRY_MR:spring-petclinic-$gitCommit")
                        dockerImage.push()
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
                    docker.withregistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        def dockerImage = docker.build("$DOCKER_REGISTRY_MAIN:spring-petclinic-latest")
                    }
                }
            }
        }
    }
}
