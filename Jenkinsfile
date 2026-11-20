
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

