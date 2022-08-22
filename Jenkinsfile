pipeline {
    agent {
        docker { image 'amazoncorretto:11' }
    }
    options { timeout(time: 5) }
    stages {
        stage ("Compile") {
            steps {
                publishChecks name: 'example', title: 'Pipeline Check', summary: 'check through pipeline',
                    text: 'you can publish checks in pipeline script',
                    detailsURL: 'https://github.com/jenkinsci/checks-api-plugin#pipeline-usage',
                    actions: [[label:'an-user-request-action', description:'actions allow users to request pre-defined behaviours', identifier:'an unique identifier']]
                  withCredentials([string(credentialsId: 'jenkins-github-app-integration-token', variable: 'TOKEN')]) {
                    sh '''
                        curl \
                          -X POST \
                          -H "Accept: application/vnd.github+json" \
                          -H "Authorization: token $TOKEN" \
                          https://api.github.com/repos/jbetoreyes/calculator/check-runs \
                          -d '{"name":"Compile","head_sha": "$GIT_COMMIT" ,"status":"in_progress","external_id":"42","started_at":"2018-05-04T01:14:52Z","output":{"title":"Mighty Readme report","summary":"","text":""}}'
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