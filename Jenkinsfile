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
                    // Lancer le conteneur en mode test
                    sh 'docker run -d -p 3001:3000 -e MESSAGE="Environnement de Test" --name myapp-test myapp:${env.BUILD_ID}'
                }
            }
        }

        stage('Tests') {
            steps {
                // Ping pour vérifier si le conteneur répond
                sh 'ping -c 6 127.0.0.1'

                // Tester l'application via curl
                sh 'curl http://localhost:3001'
            }
        }

        stage('Déployer en Production') {
            steps {
                script {
                    // Demander une confirmation manuelle avant le déploiement
                    def userInput = input(
                        message: "Voulez-vous déployer en production ?",
                        ok: "Déployer"
                    )

                    // Lancer le conteneur en mode production
                    sh 'docker run -d -p 3000:3000 -e MESSAGE="Environnement de Production" --name myapp-prod myapp:${env.BUILD_ID}'
                }
            }
        }
    }

    post {
        always {
            script {
                // Supprimer le conteneur de test
                sh 'docker rm -f myapp-test || true'
            }
        }
    }
}
