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
        SONAR_URL = "http://34.132.173.116:9000"
        SONAR_TOKEN = credentials('sonar_creds')
        POM_VERSION = readMavnePom().getVersion()
        POM_PACKAGING = readMavnePom().getPackaging()
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
                withSonarQubeEnv('SonarQube'){
                  sh """
                 mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=i27-eureka \
                    -Dsonar.host.url=${env.SONAR_URL} \
                    -Dsonar.login=${env.SONAR_TOKEN}
                """    
                }
                timeout (time: 2, unit: 'MINUTES'){
                    script {
                        waitForQualityGate abortPipeline: true
                    }
                }   
            }
        }
        stage ('DockerBuild') {
            steps {
                echo "Target jar Format: i27-${APPLICATION_NAME}-${BUILD_NUMBER}-${BRANCH_NAME}-${POM_PACKAGING}"
                echo "Building Docker Images"
            }
        }
    }
}
