pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build("ayeshaaltaf/aAyeshaaltaf-portfolio:${env.BUILD_ID}")
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-cred-a') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Test') {
            steps {
                sh 'ls -l index.html' // Simple check for index.html
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Deploy the new version
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: "Aqsa", 
                                transfers: [sshTransfer(
                                    execCommand: """
                                        docker pull ayeshaaltaf/ayeshaaltaf-portfolio:${env.BUILD_ID}
                                        docker stop ayeshaaltaf-portfolio-container || true
                                        docker rm ayeshaaltaf-portfolio-container || true
                                        docker run -d --name ayeshaaltaf-portfolio-container -p 400:80 ayeshaaltaf/ayeshaaltaf-portfolio:${env.BUILD_ID}
                                    """
                                )]
                            )
                        ]
                    )
                }
            }
        }
    }

    post {
        failure {
            mail(
                to: 'ayeshaaltaf685@gmail.com',
                subject: "Failed Pipeline: ${env.JOB_NAME} [${env.BUILD_NUMBER}]",
                body: "Something is wrong with the build ${env.BUILD_URL}"
            )
        }
    }
}
