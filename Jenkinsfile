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
        POM_VERSION = readMavenPom().getVersion()
        POM_PACKAGING = readMavenPom().getPackaging()
        // Docker hub details
        DOCKER_HUB = "docker.io/saipranayreddy"
        DOCKER_CREDS = credentials("dockerhub_creds")
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
                echo "Target jar Format: i27-${env.APPLICATION_NAME}-${BUILD_NUMBER}-${BRANCH_NAME}.${POM_PACKAGING}"
                echo "Exisiting jar Format: i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING}"
                echo "Building Docker Images"
                sh "docker build --no-cache --build-arg JAR_SOURCE=i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} -t ${DOCKER_HUB}/${APPLICATION_NAME}:$GIT_COMMIT ./.cicd"
            }
        }
    }
}
