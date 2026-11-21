
node {

    def imageName = "akshara0612/oltrs"
    def imageTag = "latest"
    def containerName = "oltrs-container"

    try {

        // STAGE 1: CHECKOUT
        stage('Checkout') {

            echo 'Fetching OLTRS source code from GitHub...'

            git branch: 'main',
                url: 'https://github.com/archanareddyse/OLTRS.git'
        }


        // STAGE 2: BUILD
        stage('Build') {

            echo 'Building Java application using Maven...'

            def mvnHome = tool 'Maven installation'

            withEnv(["PATH+MAVEN=${mvnHome}\\bin"]) {
                bat 'mvn clean compile'
            }
        }


        // STAGE 3: TEST
        stage('Test') {

            echo 'Running Maven test cases...'

            def mvnHome = tool 'Maven installation'

            withEnv(["PATH+MAVEN=${mvnHome}\\bin"]) {
                bat 'mvn test'
            }

            junit allowEmptyResults: true,
                testResults: 'target/surefire-reports/*.xml'
        }


        // STAGE 4: PACKAGE
        stage('Package') {

            echo 'Packaging application as WAR...'

            def mvnHome = tool 'Maven installation'

            withEnv(["PATH+MAVEN=${mvnHome}\\bin"]) {
                bat 'mvn clean package -DskipTests'
            }

            bat '''
                echo Checking generated WAR file...
                dir target
                if not exist target\\*.war (
                    echo ERROR: WAR file not generated!
                    exit /b 1
                )
            '''

            archiveArtifacts artifacts: 'target/*.war',
                allowEmptyArchive: false
        }


        // STAGE 5: CREATE DOCKERFILE
        stage('Create Dockerfile') {

            echo 'Creating Dockerfile for Tomcat deployment...'

            writeFile file: 'Dockerfile', text: '''
FROM tomcat:10.1-jdk21

WORKDIR /usr/local/tomcat/webapps/

COPY target/*.war ROOT.war

EXPOSE 8080

CMD ["catalina.sh", "run"]
'''

            echo 'Dockerfile created successfully!'
        }


        // STAGE 6: DOCKER BUILD
        stage('Docker Build') {

            echo 'Creating Docker image...'

            bat "docker build -t ${imageName}:${imageTag} ."

            echo 'Docker image created successfully!'
        }


        // STAGE 7: DOCKER HUB LOGIN AND PUSH
        stage('Docker Push') {

            echo 'Logging into Docker Hub and pushing image...'

            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )
            ]) {

                withEnv(["DOCKER_IMAGE=${imageName}:${imageTag}"]) {

                    try {

                        bat '''
                            @echo off
                            echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                            if errorlevel 1 exit /b 1
                        '''

                        bat '''
                            docker push %DOCKER_IMAGE%
                            if errorlevel 1 exit /b 1
                        '''

                    } finally {

                        bat 'docker logout'
                    }
                }
            }

            echo 'Docker image pushed successfully!'
        }


        // STAGE 8: DEPLOY CONTAINER
        stage('Deploy Container') {

            echo 'Deploying OLTRS Docker container...'

            bat '''
                docker rm -f oltrs-container 2>NUL
                exit /b 0
            '''

            bat "docker run -d --name ${containerName} -p 8082:8080 ${imageName}:${imageTag}"

            echo 'OLTRS container started successfully!'
        }


        // STAGE 9: VERIFY DEPLOYMENT
        stage('Verify Deployment') {

            echo 'Checking running Docker containers...'

            bat 'docker ps'

            echo 'OLTRS deployment completed successfully!'
        }


    } catch (err) {

        echo "Pipeline failed: ${err.getMessage()}"

        currentBuild.result = 'FAILURE'

        throw err

    } finally {

        if (currentBuild.currentResult == 'FAILURE') {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}


// node {

//     // ==========================================
//     // PROJECT CONFIGURATION
//     // ==========================================

//     // FILL: Docker Hub username and project name
//     def imageName = "YOUR_USERNAME/PROJECT_NAME"

//     // FILL: Docker image version
//     def imageTag = "latest"

//     // FILL: Container name
//     def containerName = "YOUR_CONTAINER_NAME"


//     try {

//         // ==========================================
//         // STAGE 1: CHECKOUT
//         // ==========================================

//         stage('Checkout') {

//             echo 'Fetching source code from GitHub...'

//             // FILL: GitHub branch and repository URL
//             git branch: 'YOUR_BRANCH',
//                 url: 'YOUR_GITHUB_REPOSITORY_URL'
//         }


//         // ==========================================
//         // STAGE 2: BUILD
//         // ==========================================

//         stage('Build') {

//             echo 'Building Java application using Maven...'

//             // FILL: Maven installation name configured in Jenkins
//             def mvnHome = tool 'YOUR_MAVEN_TOOL_NAME'

//             withEnv(["PATH+MAVEN=${mvnHome}\\bin"]) {

//                 // Maven compilation
//                 bat 'mvn clean compile'
//             }
//         }


//         // ==========================================
//         // STAGE 3: TEST
//         // ==========================================

//         stage('Test') {

//             echo 'Running Maven test cases...'

//             def mvnHome = tool 'YOUR_MAVEN_TOOL_NAME'

//             withEnv(["PATH+MAVEN=${mvnHome}\\bin"]) {

//                 bat 'mvn test'
//             }

//             // Publish test reports
//             junit allowEmptyResults: true,
//                 testResults: 'target/surefire-reports/*.xml'
//         }


//         // ==========================================
//         // STAGE 4: PACKAGE
//         // ==========================================

//         stage('Package') {

//             echo 'Packaging application...'

//             def mvnHome = tool 'YOUR_MAVEN_TOOL_NAME'

//             withEnv(["PATH+MAVEN=${mvnHome}\\bin"]) {

//                 // FILL: Choose required packaging command
//                 bat 'mvn clean package -DskipTests'
//             }

//             // FILL: Change extension to jar if required
//             archiveArtifacts artifacts: 'target/*.war',
//                 allowEmptyArchive: false
//         }


//         // ==========================================
//         // STAGE 5: CREATE DOCKERFILE
//         // ==========================================

//         stage('Create Dockerfile') {

//             echo 'Creating Dockerfile...'

//             // FILL: Base image, working directory,
//             // artifact path, destination and startup command
//             writeFile file: 'Dockerfile', text: '''
// FROM YOUR_BASE_IMAGE

// WORKDIR YOUR_WORKING_DIRECTORY

// COPY YOUR_ARTIFACT_PATH YOUR_DESTINATION

// EXPOSE YOUR_CONTAINER_PORT

// CMD ["YOUR_START_COMMAND"]
// '''

//             echo 'Dockerfile created successfully!'
//         }


//         // ==========================================
//         // STAGE 6: DOCKER BUILD
//         // ==========================================

//         stage('Docker Build') {

//             echo 'Building Docker image...'

//             bat "docker build -t ${imageName}:${imageTag} ."
//         }


//         // ==========================================
//         // STAGE 7: DOCKER HUB PUSH
//         // ==========================================

//         stage('Docker Push') {

//             echo 'Pushing image to Docker Hub...'

//             // FILL: Jenkins credentials ID
//             withCredentials([
//                 usernamePassword(
//                     credentialsId: 'YOUR_CREDENTIALS_ID',
//                     usernameVariable: 'DOCKER_USER',
//                     passwordVariable: 'DOCKER_PASS'
//                 )
//             ]) {

//                 withEnv(["DOCKER_IMAGE=${imageName}:${imageTag}"]) {

//                     try {

//                         bat '''
//                             @echo off
//                             echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
//                             if errorlevel 1 exit /b 1
//                         '''

//                         bat '''
//                             docker push %DOCKER_IMAGE%
//                             if errorlevel 1 exit /b 1
//                         '''

//                     } finally {

//                         bat 'docker logout'
//                     }
//                 }
//             }
//         }


//         // ==========================================
//         // STAGE 8: DEPLOY CONTAINER
//         // ==========================================

//         stage('Deploy Container') {

//             echo 'Deploying Docker container...'

//             // Remove old container
//             bat '''
//                 docker rm -f YOUR_CONTAINER_NAME 2>NUL
//                 exit /b 0
//             '''

//             // FILL: Host port and container port
//             bat "docker run -d --name ${containerName} -p HOST_PORT:CONTAINER_PORT ${imageName}:${imageTag}"
//         }


//         // ==========================================
//         // STAGE 9: VERIFY DEPLOYMENT
//         // ==========================================

//         stage('Verify Deployment') {

//             echo 'Checking running containers...'

//             bat 'docker ps'

//             echo 'Deployment completed successfully!'
//         }


//         // ==========================================
//         // STAGE 10: SUCCESS EMAIL
//         // ==========================================

//         stage('Email Notification') {

//             emailext(

//                 subject: "SUCCESS: ${JOB_NAME} - Build #${BUILD_NUMBER}",

//                 body: """
//                 Hello,

//                 Jenkins pipeline completed successfully!

//                 Project: ${JOB_NAME}
//                 Build Number: ${BUILD_NUMBER}
//                 Status: ${currentBuild.currentResult}

//                 Build URL:
//                 ${BUILD_URL}

//                 Regards,
//                 Jenkins
//                 """,

//                 // FILL: Receiver email address
//                 to: 'YOUR_EMAIL@gmail.com'
//             )
//         }


//     } catch (err) {

//         echo "Pipeline failed: ${err.getMessage()}"

//         currentBuild.result = 'FAILURE'


//         // ==========================================
//         // FAILURE EMAIL
//         // ==========================================

//         emailext(

//             subject: "FAILURE: ${JOB_NAME} - Build #${BUILD_NUMBER}",

//             body: """
//             Hello,

//             Jenkins pipeline failed!

//             Project: ${JOB_NAME}
//             Build Number: ${BUILD_NUMBER}
//             Error: ${err.getMessage()}

//             Check Console Output:
//             ${BUILD_URL}

//             Regards,
//             Jenkins
//             """,

//             // FILL: Receiver email address
//             to: 'YOUR_EMAIL@gmail.com'
//         )

//         throw err


//     } finally {

//         if (currentBuild.currentResult == 'FAILURE') {

//             echo 'Pipeline failed. Check Console Output.'
//         }
//     }
// }


node {

    try {

        // STAGE 1: CHECKOUT
        stage('Checkout') {

            echo 'Fetching Food Management System from GitHub...'

            git branch: 'main',
                url: 'https://github.com/Kumbhambhargavi75/FoodSystem.git'
        }

        // STAGE 2: MAVEN CLEAN
        stage('Maven Clean') {

            echo 'Cleaning previous build files...'

            def mvnHome = tool 'Maven installation'

            withEnv(["PATH+MAVEN=${mvnHome}\\bin"]) {
                bat 'mvn clean'
            }
        }

        // STAGE 3: MAVEN INSTALL
        stage('Maven Install') {

            echo 'Building Food Management System...'

            def mvnHome = tool 'Maven installation'

            withEnv(["PATH+MAVEN=${mvnHome}\\bin"]) {
                bat 'mvn install'
            }
        }

    } catch (err) {

        echo "Pipeline failed: ${err.getMessage()}"

        currentBuild.result = 'FAILURE'

        throw err

    } finally {

        if (currentBuild.currentResult == 'FAILURE') {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}

