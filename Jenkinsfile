pipeline {
    agent {
        docker { image 'amazoncorretto:11-alpine-jdk' }
    }
    options { timeout(time: 5) }
    stages {
        stage ("Compile") {
            steps {
                  withCredentials([string(credentialsId: 'github-jenkins-integration-token', variable: 'TOKEN')]) {
                    sh "echo $TOKEN"
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