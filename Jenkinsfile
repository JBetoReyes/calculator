pipeline {
    agent {
        docker { image 'amazoncorretto:11' }
    }
    options { timeout(time: 5) }
    stages {
        stage ("Compile") {
            steps {
                publishChecks name: 'Compile', status: 'in_progress'
                sh "./gradlew compileJava"
                publishChecks name: 'Compile', title: 'Compile', summary: 'gradlew compilation',
                    text: 'running ./gradlew compileJava',
                    detailsURL: '',
                    actions: [[label:'an-user', description:'actions ', identifier:'identifier']]
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
            sh 'curl "https://api.GitHub.com/repos/JBetoReyes/Calculator/statuses/b326c46?access_token=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" -H "Content-Type: application/json" -X POST -d "{\"state\": \"success\",\"context\": \"continuous-integration/jenkins\", \"description\": \"Jenkins\", \"target_url\": \"http://54.183.190.35:8080/job/Calculator/1/console\"}"'
        }
    }
}