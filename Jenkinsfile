pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:${env.PATH}"
        KUBECTL_VERSION = sh(script: 'curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt', returnStdout: true).trim()
        KUBECTL_URL = "https://storage.googleapis.com/kubernetes-release/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl"
        HELM_VERSION = "3.7.0" // Версия Helm
        HELM_PATH = "${WORKSPACE}/helm" // Путь к исполняемому файлу Helm
    }

     stages {

        //  stage('Checkout') {
        //     steps {
        //         // Шаг для получения исходного кода (если нужно)
        //         git 'https://github.com/entonekryzhovnik/devops_tech_test.git'
        //     }
        // }

        stage('Install Tools') {
            steps {
                // Установка kubectl
                sh "curl -LO ${KUBECTL_URL}"
                sh "chmod +x ./kubectl"

                // Установка Helm
                sh "curl -fsSL -o helm.tar.gz https://get.helm.sh/helm-v${HELM_VERSION}-linux-amd64.tar.gz"
                sh "tar -zxvf helm.tar.gz"
                sh "mv linux-amd64/helm ./helm"
                sh "chmod +x ./helm"
            }
        }
        stage('Deploy Nginx') {
            steps {
                script {


                    // Добавляем путь к Helm в переменную среды PATH
                    sh "./helm install my-nginx ./my-nginx"
                }
            }
        }
    }
}
