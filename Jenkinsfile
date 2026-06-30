pipeline {
    agent { label 'docker-agent' }

    // Poll GitHub for changes. Also configure "Poll SCM" in the job UI (lecture notes);
    // this block keeps the trigger versioned in the Jenkinsfile once the job has run once.
    // triggers {
    //     pollSCM('* * * * *')
    // }

    stages {
        // First check and make sure the pipeline should even run.
        // Only allow main and feature branches to run the pipeline. Any other branch name fails the pipeline immediately.
        stage('Validate branch name') {
            steps {
                script {
                    def b = env.BRANCH_NAME
                    if (!(b == 'main' || b.contains('feature'))) {
                        // Let's keep the rror messages positive, eh?
                        error("Branch '${b}' isn't main or a feature branch - operation failed successfully.")
                    }
                    echo "Branch '${b}' is allowed."
                }
            }
        }

        // If we know we're good, go ahead and start the announcement and run the follow-on stages
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

        // Checkstyle: runs on main and feature branches ( I hope)
        stage('Checkstyle') {
            // when {
            //     anyOf {
            //         branch 'main'
            //         expression { env.BRANCH_NAME.contains('feature') }
            //     }
            // }
            steps {
                dir('Chapter08/sample1') {
                    sh './gradlew checkstyleTest'
                }
            }
        }

        // Checkstyle + Code Coverage run ONLY when a *.java file was modified.
        // stage('Checkstyle & Code Coverage') {
        stage('Code Coverage') {
            when {
                // changeset '**/*.java'
                branch 'main'
            }
            steps {
                dir('Chapter08/sample1') {
                    // sh './gradlew checkstyleMain checkstyleTest jacocoTestReport jacocoTestCoverageVerification'
                    sh './gradlew jacocoTestReport jacocoTestCoverageVerification'
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
            echo 'Pipeline ran perfectly. Good job, Hackerman'
        }
        failure {
            echo 'pipeline failed successfully :( '
        }
    }
}
