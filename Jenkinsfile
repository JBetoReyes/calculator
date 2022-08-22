def failedStage = ""

pipeline {
    agent any
    options { timeout(time: 5) }
    stages {
        stage ("Compile") {
            steps {
                script { failedStage = env.STAGE_NAME }
                publishChecks name: 'Compile', status: 'QUEUED'
                publishChecks name: 'Unit Test', status: 'QUEUED'
                publishChecks name: 'Code coverage', status: 'QUEUED'
                publishChecks name: 'Package', status: 'QUEUED'
                publishChecks name: 'Docker build', status: 'QUEUED'
                publishChecks name: 'Docker push', status: 'QUEUED'
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
                script { failedStage = env.STAGE_NAME }
                publishChecks name: 'Unit Test', status: 'IN_PROGRESS'
                sh "./gradlew test"
                publishChecks name: 'Unit Test'
            }
        }
        stage ("Code coverage") {
            steps {
                script { failedStage = env.STAGE_NAME }
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
                script { failedStage = env.STAGE_NAME }
                publishChecks name: 'Package', status: 'IN_PROGRESS'
                sh "./gradlew build"
                publishChecks name: 'Package'
            }
        }
        stage ("Docker build") {
            steps {
                script { failedStage = env.STAGE_NAME }
                publishChecks name: 'Docker build', status: 'IN_PROGRESS'
                sh "docker build -t jbetoreyes/calculator:${currentBuild.number} ."
                publishChecks name: 'Docker build'
            }
        }
        stage ("Docker push") {
            steps {
                script { failedStage = env.STAGE_NAME }
                publishChecks name: 'Docker push', status: 'IN_PROGRESS'
                sh "docker push jbetoreyes/calculator"
                publishChecks name: 'Docker build'
            }
        }
    }
    post {
        failure {
            echo "failed stage: ${failedStage}"
            publishChecks name: 'Compile', conclusion: 'FAILURE'
            publishChecks name: 'Code coverage', conclusion: 'FAILURE'
            publishChecks name: 'Package', conclusion: 'FAILURE'
            publishChecks name: 'Docker build', conclusion: 'FAILURE'
            publishChecks name: 'Docker push', conclusion: 'FAILURE'
        }
    }
}