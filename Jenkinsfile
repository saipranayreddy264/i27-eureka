pipeline {
    agent {
        label 'java-slave'
    }
    tools {
        maven 'Maven 3.8.9'
        jdk 'JDK-17'
    }
    environment {
        APPLICATION_NAME = "eureka"
    }
    stages {
        stage ('buildsatge'){
            steps {
                echo "Buiding the ${APPLICATION_NAME} Application "
                sh "mvn package -DskipTests=true"
                archiveArtifacts artifacts: 'target/*jar'
            }
        }
        stage ('sonarqube'){
            steps {
                echo "starting the sonar scans"
                sh """
                mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=i27-eureka \
                    -Dsonar.host.url=http://34.132.173.116:9000 \
                    -Dsonar.login=sqa_0662a5df8a06430f8d6ebcae43463ad6b2aa62b5
                """    
            }
        }
    }
}