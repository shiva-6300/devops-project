pipeline {
    agent any

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shiva-6300/devops-project.git'
            }
        }

        stage('Verify Project') {
            steps {
                sh '''
                    pwd
                    ls -la
                    find . -name pom.xml
                '''
            }
        }

        stage('Compile') {
            steps {
                dir('FullStack-Blogging-App') {
                    sh 'mvn clean compile'
                }
            }
        }

        stage('Trivy File System Scan') {
            steps {
                sh 'trivy fs --format table -o trivy-fs-report.html .'
            }
        }

    }
}
