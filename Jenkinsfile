pipeline {
    agent any
    stages {
        stage('Announce') {
            steps {
                sh 'echo this is the main branch'
            }
        }
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Angry-Joe/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git',
                    branch: 'main'
            }
        }
        stage('JaCoCo Checkstyle') {
            steps {
                dir('Chapter08/sample1') {
                    sh './gradlew clean test jacocoTestReport jacocoTestCoverageVerification'
                }
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: true,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'Chapter08/sample1/build/reports/jacoco/test/html',
                        reportFiles: 'index.html',
                        reportName: 'jacoco checkstyle'
                    ])
                }
            }
        }
    }
}
