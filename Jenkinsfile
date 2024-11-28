pipeline {
    agent any

    stages {
        stage('Cloner le Code') {
            steps {
                git branch: 'main', url: 'https://github.com/kenaubry/myapp.git'
            }
        }

        stage('Construire l\'Image Docker') {
            steps {
                script {
                    dockerImage = docker.build("myapp:${env.BUILD_ID}")
                }
            }
        }

        stage('Déployer en Test') {
            steps {
                script {
                    sh 'docker run -d -p 3001:3000 -e MESSAGE="Environnement de Test" --name myapp-test myapp:${env.BUILD_ID}'

                }
            }
        }

        stage('Tests') {
            steps {
                sh 'ping -n 6 127.0.0.1 > nul'
                sh 'curl http://localhost:3001'
            }
        }

        stage('Déployer en Production') {
            steps {
                script {
                    def userInput = input(
                        message: "Voulez-vous déployer en production ?",
                        ok: "Déployer"
                    )
                    sh 'docker run -d -p 3000:3000 -e MESSAGE="Environnement de Production" --name myapp-prod myapp:${env.BUILD_ID}'
                }
            }
        }
    }

    post {
        always {
            script {
                sh 'docker rm -f myapp-test || exit 0'
            }
        }
    }
}
