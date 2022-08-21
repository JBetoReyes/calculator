pipeline {
    agent {
        docker { image 'amazoncorretto:11-alpine-jdk' }
    }
    options { timeout(time: 5) }
    stages {
        stage ("Compile") {
            steps {
                  withCredentials([string(credentialsId: 'github-jenkins-integration-token', variable: 'TOKEN')]) {
                    sh '''
                        curl \
                          -X POST \
                          -H "Accept: application/vnd.github+json" \
                          -H "Authorization: token $TOKEN" \
                          https://api.github.com/repos/jbetoreyes/calculator/check-runs \
                          -d '{"name":"Compile","head_sha": $GIT_COMMIT ,"status":"in_progress","external_id":"42","started_at":"2018-05-04T01:14:52Z","output":{"title":"Mighty Readme report","summary":"","text":""}}'
                    '''
                    sh "echo ${TOKEN}"
                  }
                sh "./gradlew compileJava"
            }
        }
        stage("Unit Test") {
            steps {
                sh "./gradlew test"
            }
        }
        stage ("Code coverage test") {
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
}