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
        PATH = "/usr/bin:/snap/bin:/usr/local/bin:${env.PATH}"
    }
    stages {
        stage ('buildsatge'){
            steps {
                echo "Buiding the ${APPLICATION_NAME} Application "
                sh "mvn package -DskipTests=true"
                archiveArtifacts artifacts: 'target/*jar'
            }
        }
        // stage ('sonarqube'){
        //     steps {
        //         echo "starting the sonar scans"
        //         withSonarQubeEnv('SonarQube'){
        //           sh """
        //          mvn clean verify sonar:sonar \
        //             -Dsonar.projectKey=i27-eureka \
        //             -Dsonar.host.url=${env.SONAR_URL} \
        //             -Dsonar.login=${env.SONAR_TOKEN}
        //         """    
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
                sh """
                ls -la
                cp ${WORKSPACE}/target/i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} ./.cicd
                echo "Target jar Format: i27-${env.APPLICATION_NAME}-${BUILD_NUMBER}-${BRANCH_NAME}.${POM_PACKAGING}"
                echo "Exisiting jar Format: i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING}"
                echo "Building Docker Images"
                docker build --no-cache --build-arg JAR_SOURCE=i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} -t ${DOCKER_HUB}/${APPLICATION_NAME}:$GIT_COMMIT ./.cicd
                echo "******************DockerLogin************************"
                docker login -u ${DOCKER_CREDS_USR} -p ${DOCKER_CREDS_PSW}
                echo "**************DockerPush***************"
                docker push ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:$GIT_COMMIT
                """
            }
        }
    }
}
