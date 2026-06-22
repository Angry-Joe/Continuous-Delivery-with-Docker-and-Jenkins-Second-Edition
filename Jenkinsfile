pipeline {
    // Ex05: the build must run inside an agent container that Jenkins provisions.
    // 'docker-agent' is the label we attach to the Docker Cloud template.
    agent { label 'docker-agent' }

    // Poll GitHub for changes. Also configure "Poll SCM" in the job UI (lecture notes);
    // this block keeps the trigger versioned in the Jenkinsfile once the job has run once.
    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Announce') {
            steps {
                sh 'echo "Pipeline started on agent container: $(hostname)"'
            }
        }

        // Runs on EVERY build, regardless of whether a *.java file changed.
        stage('Unit Tests') {
            steps {
                dir('Chapter08/sample1') {
                    sh 'chmod +x ./gradlew'
                    sh './gradlew clean test'
                }
            }
        }

        // Checkstyle + Code Coverage run ONLY when a *.java file was modified.
        stage('Checkstyle & Code Coverage') {
            when {
                changeset '**/*.java'
            }
            steps {
                dir('Chapter08/sample1') {
                    sh './gradlew checkstyleMain checkstyleTest jacocoTestReport jacocoTestCoverageVerification'
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
                        reportName: 'JaCoCo Coverage'
                    ])
                }
            }
        }
    }

    post {
        success {
            echo 'pipeline ran perfectly'
        }
        failure {
            echo 'pipeline failure'
        }
    }
}
