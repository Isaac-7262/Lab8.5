pipeline {
    agent any

    environment {
        RESULTS_DIR = "results"
        ROBOT_OPTIONS = "--outputdir ${RESULTS_DIR}"
        BROWSER = "headlesschrome"
    }

    options {
        timestamps()
        ansiColor('xterm')
    }

    stages {

        stage('Prepare Workspace') {
            steps {
                echo "Preparing workspace..."
                sh """
                    rm -rf ${RESULTS_DIR}
                    mkdir -p ${RESULTS_DIR}
                """
            }
        }

        stage('Verify Environment') {
            steps {
                echo "Checking tools inside container..."
                sh """
                    python3 --version
                    robot --version
                    chromium --version || echo "Chromium not found"
                """
            }
        }

        stage('Run Robot Framework Tests') {
            steps {
                echo "Executing Robot Framework tests..."
                sh """
                    robot ${ROBOT_OPTIONS} \
                          --variable BROWSER:${BROWSER} \
                          --settag docker_run \
                          tests/
                """
            }
        }
    }

    post {
        always {
            echo "Publishing Robot Framework results..."

            step([
                $class: 'RobotPublisher',
                outputPath: "${RESULTS_DIR}",
                outputFileName: 'output.xml',
                reportFileName: 'report.html',
                logFileName: 'log.html',
                disableReports: false,
                passThreshold: 100.0,
                unstableThreshold: 80.0
            ])

            archiveArtifacts artifacts: "${RESULTS_DIR}/*", allowEmptyArchive: true
        }

        success {
            echo "Pipeline finished SUCCESSFULLY ✅"
        }

        failure {
            echo "Pipeline FAILED ❌ — please check logs"
        }
    }
}
