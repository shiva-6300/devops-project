pipeline {
    agent any

    tools {
        jdk 'jdk'
        maven 'maven'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

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
                    echo "Current Workspace:"
                    pwd
                    echo "Workspace Files:"
                    ls -la
                    echo "Searching for pom.xml..."
                    find . -name pom.xml
                '''
            }
        }

        stage('Check Java Version') {
            steps {
                sh '''
                    echo "JAVA_HOME=$JAVA_HOME"
                    java -version
                    javac -version
                    mvn -version
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

        stage('SonarQube Analysis') {
            steps {
                dir('FullStack-Blogging-App') {
                    withSonarQubeEnv('sonarqubeServer') {
                        sh '''
                            $SCANNER_HOME/bin/sonar-scanner \
                            -Dsonar.projectKey=Blogging-app \
                            -Dsonar.projectName=Blogging-app \
                            -Dsonar.sources=src \
                            -Dsonar.java.binaries=target/classes
                        '''
                    }
                }
            }
        }

        stage('Build') {
            steps {
                dir('FullStack-Blogging-App') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Publish Artifacts') {
            steps {
                dir('FullStack-Blogging-App') {
                    withMaven(
                        jdk: 'jdk',
                        maven: 'maven',
                        globalMavenSettingsConfig: 'maven-settings',
                        traceability: true
                    ) {
                        sh 'mvn deploy'
                    }
                }
            }
        }

        stage('Trivy File System Scan') {
            steps {
                sh '''
                    trivy fs \
                    --format table \
                    -o trivy-fs-report.html .
                '''
            }
        }

    }

    post {
        always {
            archiveArtifacts artifacts: 'trivy-fs-report.html', allowEmptyArchive: true
        }

        success {
            echo 'Pipeline executed successfully.'
        }

        failure {
            echo 'Pipeline execution failed.'
        }
    }
}
