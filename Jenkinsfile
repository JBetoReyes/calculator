pipeline {
    agent any
    options { timeout(time: 5) }
    stages {
        stage ("Compile") {
            steps {
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
        stage ("Package") {
            steps {
                sh "./gradlew build"
            }
        }
        stage ("Docker build") {
            steps {
                sh "docker build -t jbetoreyes/calculator:${env.build}"
            }
        }
        stage ("Docker push") {
            steps {
                sh "docker push jbetoreyes/calculator"
            }
        }
    }
}