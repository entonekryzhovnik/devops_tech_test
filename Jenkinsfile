
pipeline {
    agent any
    stages {
        stage('Clone repository') {
            steps {
                // Шаг клонирования вашего репозитория
                git 'https://github.com/entonekryzhovnik/devops_tech_test'
            }
        }
        stage('Install Helm Chart') {
            steps {
                // Шаг установки Helm чарта
                sh 'helm install my-nginx ./my-nginx'
            }
        }
    }
}