pipeline {
    agent {
        label 'java-slave'
    }
    tools {
        maven 'Maven 3.6.3'
        jdk 'JDK-21'
    }
    stages {
        stage ('buildsatge'){
            steps {
                echo "Buiding the Eureka Application "
                sh "mvn package"
            }
        }
    }
}