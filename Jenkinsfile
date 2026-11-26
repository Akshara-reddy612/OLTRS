pipeline{
  agent any
  tools{
    maven 'Maven installation'
  }
  stages{
    stage('git repo & clean'){
      steps{
        bat "git clone https://github.com/Akshara-reddy612/my-java-app.git"
        bat "mvn clean -f my-java-app"
      }
    }
    stage('install'){
      steps{
        bat "mvn install -f my-java-app"
      }
    }
    stage('test'){
      steps{
        bat "mvn test -f my-java-app"
      }
    }
    stage('package'){
      steps{
        bat "mvn package -f my-java-app"
      }
    }
  }
}
