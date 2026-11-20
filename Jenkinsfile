
node {

    try {

        stage('Checkout') {
            echo 'Checking out OLTRS project from GitHub...'

            git branch: 'main',
                url: 'https://github.com/Akshara-reddy612/OLTRS.git'
        }

        stage('Build') {
            echo 'Building the OLTRS application...'

            def mvnHome = tool 'Maven-3.10.1'

            withEnv(["PATH+MAVEN=${mvnHome}\\bin"]) {
                bat 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }

        stage('Test') {
            echo 'Executing test cases...'

            junit allowEmptyResults: true,
                testResults: 'target/surefire-reports/*.xml'
        }

        stage('Archive') {
            echo 'Archiving generated JAR files...'

            archiveArtifacts allowEmptyArchive: true,
                artifacts: 'target/*.jar'
        }

        echo 'OLTRS Pipeline completed successfully!'

    } catch (Exception e) {

        echo "Pipeline failed: ${e.getMessage()}"
        throw e

    }
}
