pipeline {
    agent {
        docker { image 'amazoncorretto:11' }
    }
    options { timeout(time: 5) }
    stages {
        stage ("Compile") {
            steps {
                withChecks(name: 'injected name') {
                    // some other steps that will extract the name
                    publishChecks name: 'Compile', status: 'QUEUED'
                    publishChecks name: 'Unit Test', status: 'QUEUED'
                    publishChecks name: 'Code coverage', status: 'QUEUED'
                    publishChecks name: 'Compile', status: 'IN_PROGRESS'
                    sh "./gradlew compileJava"
                    publishChecks name: 'Compile', title: 'Compile', summary: 'gradlew compilation',
                        text: 'running ./gradlew compileJava',
                        detailsURL: '',
                        actions: [[label:'gradlew', description:'compileJava', identifier:'gradlew']]
                }
            }
        }
        stage("Unit Test") {
            steps {
                publishChecks name: 'Unit Test', status: 'IN_PROGRESS'
                sh "./gradlew test"
                publishChecks name: 'Unit Test'
            }
        }
        stage ("Code coverage") {
            steps {
                publishChecks name: 'Code coverage', status: 'IN_PROGRESS'
                sh "./gradlew jacocoTestReport"
                publishHTML (target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFiles: 'index.html',
                    reportName: 'JaCOCO Report'

                ])
                sh "./gradlew jacocoTestCoverageVerification"
                publishChecks name: 'Code coverage'
            }
        }
    }
}