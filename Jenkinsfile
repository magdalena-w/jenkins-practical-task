pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE_NAME = "magdalena01/main"
    }

    stages {
        stage('Checkstyle') {
            steps {
                // This stage runs the Checkstyle analysis using Maven and saves the report as an artifact.
                sh './mvnw checkstyle:checkstyle'
                archiveArtifacts artifacts: '**/target/checkstyle-result.xml', allowEmptyArchive: true
                // Archive the Checkstyle report as a build artifact
            }
        }

        stage('Test') {
            steps {
                // This stage runs the tests using Maven.
                sh './mvnw test'
            }
        }

        stage('Build') {
            steps {
                // This stage builds the application without running tests using Maven.
                sh './mvnw clean install -DskipTests'
            }
        }

        stage('BuildDockerImageMR') {
            when {
                branch 'main'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }

        stage('PushDockerImageMR') {
            when {
                branch 'main'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('CreateDockerImageMain') {
            steps {
                // This stage builds a Docker image using the Dockerfile in your repository.
                script {
                    sh "docker build -t magdalena01/main:latest ."
                    // Build the Docker image and tag it as "latest"
                    sh "docker push magdalena01/main:latest"
                    // Push the Docker image to the "main" repository on Docker Hub
                }
            }
        }
    }
}
