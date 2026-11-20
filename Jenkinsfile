
node {
    def imageName = "akshara0612/oltrs"
    def imageTag = "latest"

    try {

        stage('Checkout') {
            git branch: 'main',
                url: 'https://github.com/Akshara-reddy612/OLTRS.git'
        }

        stage('Build') {
            if (isUnix()) {
                sh 'mvn clean compile'
            } else {
                bat 'mvn clean compile'
            }
        }

        stage('Test') {
            if (isUnix()) {
                sh 'mvn test'
            } else {
                bat 'mvn test'
            }
        }

        stage('Docker Build') {
            if (isUnix()) {
                sh "docker build -t ${imageName}:${imageTag} ."
            } else {
                bat "docker build -t ${imageName}:${imageTag} ."
            }
        }

        stage('Docker Push') {
            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )
            ]) {
                if (isUnix()) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login \
                        -u "$DOCKER_USER" --password-stdin
                        docker push YOUR_DOCKERHUB_USERNAME/oltrs:latest
                        docker logout
                    '''
                } else {
                    bat '''
                        echo %DOCKER_PASS% | docker login ^
                        -u %DOCKER_USER% --password-stdin
                        docker push YOUR_DOCKERHUB_USERNAME/oltrs:latest
                        docker logout
                    '''
                }
            }
        }

    } catch (err) {
        currentBuild.result = 'FAILURE'
        throw err

    } finally {
        if (currentBuild.currentResult == 'FAILURE') {
            echo 'Pipeline failed. Check the Console Output.'
        }
    }
}