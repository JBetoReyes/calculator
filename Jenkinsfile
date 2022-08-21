pipeline {
    agent {
        docker { image 'amazoncorretto:11-alpine-jdk' }
    }
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
    }
}