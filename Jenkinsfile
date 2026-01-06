pipeline {
    agent {
        label 'k8s-slave'
    }
    tools {
        maven 'Maven-3.8.9'
        jdk 'JDK-17'
    }
    environment {
        APPLICATION_NAME = "eureka"
        SONAR_URL = "http://34.48.167.83:9000"
        SONAR_TOKEN = credentials('sonar_creds')
        POM_VERSION = readMavenPom().getVersion()
        POM_PACKAGING = readMavenPom().getPackaging()

        // Docker hub details 
        DOCKER_HUB = "docker.io/i27devopsb8"
        DOCKER_CREDS = credentials("dockerhub_creds")
        //JFROG_DOCKER_REPO = "i27.jfrog.io"

    }
    stages {
        stage ('build'){
            steps {
                echo "Building ${env.APPLICATION_NAME} Application"
                sh "mvn package -DskipTests=true"
                archiveArtifacts artifacts: 'target/*jar'
            }
        }
        // stage ('sonarqube') {
        //     steps {
        //         echo "Starting Sonar Scans"
        //         withSonarQubeEnv('SonarQube'){
        //             sh """
        //             mvn clean verify sonar:sonar \
        //                 -Dsonar.projectKey=i27-eureka \
        //                 -Dsonar.host.url=${env.SONAR_URL} \
        //                 -Dsonar.login=${env.SONAR_TOKEN}                  
        //             """
        //         }
        //         timeout (time: 2, unit: 'MINUTES'){
        //             script {
        //                 waitForQualityGate abortPipeline: true
        //             }
        //         }
 
        //     }
        // }
        stage ('DockerBuild') {
            steps {
                ///i27-eureka-0.0.1-SNAPSHOT.jar
                sh """
                ls -la
                cp ${WORKSPACE}/target/i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} ./.cicd
                echo "Existing Jar Format: i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING}"
                echo "Target Jar Format: i27-${env.APPLICATION_NAME}-${BUILD_NUMBER}-${BRANCH_NAME}.${env.POM_PACKAGING}"
                echo "**** Building Docker Images ******"
                docker build --no-cache --build-arg JAR_SOURCE=i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} -t ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:$GIT_COMMIT ./.cicd
                echo "******************************** Docker Login ********************************"
                docker login -u ${DOCKER_CREDS_USR} -p ${DOCKER_CREDS_PSW}
                echo "******************************** Docker Push ********************************"
                docker push ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:$GIT_COMMIT
                """
            }
        }
    }
}

// extra code 

//i27devopsb8/eureka:tagname 