pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'SonarQube'              // Nom du serveur SonarQube configuré dans Jenkins
        PROJECT_KEY = 'alpinehelloworld'         // Project Key dans SonarQube
        SONARQUBE_URL = 'http://sonarqube:9000' // URL de SonarQube
        SONAR_SCANNER_HOME = '/usr/local/bin'   // Chemin vers SonarScanner déjà installé
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo '🔄 Récupération du code source...'
                checkout scm
            }
        }

        stage('Install Python Dependencies') {
            steps {
                echo '📦 Installation des dépendances Python...'
                sh '''
                    if [ -f webapp/requirements.txt ]; then
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install --upgrade pip
                        pip install -r webapp/requirements.txt
                    else
                        echo "Pas de fichier requirements.txt trouvé dans webapp"
                    fi
                '''
            }
        }

        stage('Analyse SonarQube') {
            steps {
                echo '🔍 Lancement de l’analyse SonarQube...'
                script {
                    withCredentials([string(credentialsId: 'alpinehelloworld', variable: 'SONAR_TOKEN')]) {
                        sh """
                            ${SONAR_SCANNER_HOME}/sonar-scanner \
                              -Dsonar.projectKey=${PROJECT_KEY} \
                              -Dsonar.sources=. \
                              -Dsonar.login=${SONAR_TOKEN} \
                              -Dsonar.host.url=${SONARQUBE_URL}
                        """
                    }
                }
            }
        }

        stage('Build Python Project') {
            steps {
                echo '🔧 Compilation (optionnelle)...'
                sh '''
                    if [ -f setup.py ]; then
                        python3 setup.py install
                    else
                        echo "Pas de setup.py, étape ignorée"
                    fi
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline terminée avec succès.'
        }
        failure {
            echo '❌ Échec de la pipeline.'
        }
    }
}
