pipeline {
    agent {
        docker { image 'amazoncorretto:11-alpine-jdk' }
    }
    options { timeout(time: 5) }
    stages {
        stage ("Compile") {
            steps {
                sh "./gradlew compileJava"
            }
        }
        stage("Unit Test") {
            steps {
                sh "./gradlew test"
            }
        }
        stage ("Code coverage") {
            steps {
                sh "./gradlew jacocoTestReport"
                publishHTML (target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFiles: 'index.html',
                    reportName: 'JaCOCO Report'

                ])
                sh "./gradlew jacocoTestCoverageVerification"
            }
        }
    }
    post {
        success {
            sh 'curl "https://api.GitHub.com/repos/JBetoReyes/Calculator/statuses/$GIT_COMMIT?access_token=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" -H "Content-Type: application/json" -X POST -d "{\"state\": \"success\",\"context\": \"continuous-integration/jenkins\", \"description\": \"Jenkins\", \"target_url\": \"http://54.183.190.35:8080/job/Calculator/$BUILD_NUMBER/console\"}"'
        }
    }
}