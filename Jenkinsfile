pipeline {
    agent any

    environment {
        RESULTS_DIR = "results"
    }

    stages {
        stage('Cleanup') {
            steps {
                sh "rm -rf ${RESULTS_DIR}"
                sh "mkdir -p ${RESULTS_DIR}"
            }
        }

        stage('Build Check') {
            steps {
                sh 'python3 --version'
                sh 'robot --version'
                sh 'chromium --version'
            }
        }

        stage('Run Robot Tests') {
            steps {
                sh '''
                robot --outputdir results tests/Lab8.robot
                '''
            }
        }
    }

    post {
        always {
            step([$class: 'RobotPublisher',
                outputPath: 'results',
                outputFileName: 'output.xml',
                reportFileName: 'report.html',
                logFileName: 'log.html'
            ])

            archiveArtifacts artifacts: 'results/*.*', allowEmptyArchive: true
        }
    }
}
