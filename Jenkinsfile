def stagesMap = [
    'Compile',
    'Unit Test',
    'Code coverage',
    'Package',
    'Docker build',
    'Docker push',
    'Deploy to staging'
]

pipeline {
    agent any
    options { timeout(time: 5) }
    stages {
        stage ("Compile") {
            steps {
                script {
                    failedStage = env.STAGE_NAME
                    stagesMap.each { value ->
                        publishChecks name: "${value}", status: 'QUEUED'
                    }
                }
                publishChecks name: 'Compile', status: 'IN_PROGRESS'
                sh "./gradlew compileJava"
                publishChecks name: 'Compile', title: 'Compile', summary: 'gradlew compilation',
                    text: 'running ./gradlew compileJava',
                    detailsURL: '',
                    actions: [[label:'gradlew', description:'compileJava', identifier:'gradlew']]
                script { stagesMap.remove(0) }
            }
        }
        stage("Unit Test") {
            steps {
                script { failedStage = env.STAGE_NAME }
                publishChecks name: 'Unit Test', status: 'IN_PROGRESS'
                sh "./gradlew test"
                publishChecks name: 'Unit Test'
                script { stagesMap.remove(0) }
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
                script { stagesMap.remove(0) }
            }
        }
        stage ("Package") {
            steps {
                script { failedStage = env.STAGE_NAME }
                publishChecks name: 'Package', status: 'IN_PROGRESS'
                sh "./gradlew build"
                publishChecks name: 'Package'
                script { stagesMap.remove(0) }
            }
        }
        stage ("Docker build") {
            steps {
                script { failedStage = env.STAGE_NAME }
                publishChecks name: 'Docker build', status: 'IN_PROGRESS'
                sh "docker build -t jbetoreyes/calculator:${currentBuild.number} ."
                publishChecks name: 'Docker build'
                script { stagesMap.remove(0) }
            }
        }
        stage ("Docker push") {
            steps {
                script { failedStage = env.STAGE_NAME }
                publishChecks name: 'Docker push', status: 'IN_PROGRESS'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-integration', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
                  sh "docker login -u ${USER} -p ${PASSWORD}"
                }
                sh "docker push jbetoreyes/calculator:${currentBuild.number}"
                publishChecks name: 'Docker push'
                script { stagesMap.remove(0) }
            }
        }
        stage ("Deploy to staging") {
            steps {
                publishChecks name: 'Deploy to staging', status: 'IN_PROGRESS'
                sh "docker run -d --rm -p 8765:8080 --name calculator-build-${currentBuild.number} jbetoreyes/calculator:${currentBuild.number}"
                publishChecks name: 'Deploy to staging'
            }
        }
        stage ("Acceptance test") {
            steps {
                publishChecks name: 'Acceptance test', status: 'IN_PROGRESS'
                sleep 60
                sh "chmod +x acceptance_test.sh && ./acceptance_test.sh"
                publishChecks name: 'Acceptance test'
            }
        }
    }
    post {
        failure {
            script {
                stagesMap.each { value ->
                    publishChecks name: "${value}", conclusion: 'CANCELED', status: 'COMPLETED'
                }
            }
            publishChecks name: "${failedStage}", conclusion: 'FAILURE',  status: 'COMPLETED'
        }
        always {
            sh "docker stop calculator-build-${currentBuild.number}"
        }
    }
}