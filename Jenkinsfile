pipeline {
    agent {
        label 'java-slave'
    }
    tools {
        maven 'Maven 3.6.3'
        jdk 'JDK-21'
    }
    environment {
        APPLICATION_NAME = "eureka"
    }
    stages {
        stage ('buildsatge'){
            steps {
                echo "Buiding the ${APPLICATION_NAME} Application "
                sh "mvn package -DskipTests=true"
                archiveArtifacts arrtifacts: 'target/*jar'
            }
        }
    }
}