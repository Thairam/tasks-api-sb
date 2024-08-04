pipeline {
  agent any
  stages {
    stage ('Build Backend') {
      steps {
        bat 'mvn clean package -DskipTests=true'
      }
    }
    stage ('Unit tests') {
      steps {
        bat 'mvn test'
      }
    }
    stage ('Sonar Analysis') {
      environment {
        scannerHome = tool 'SONAR_SCANNER'
      }
      steps {
        withSonarQubeEnv('SONAR_LOCAL'){
          bat "${scannerHome}/bin/sonar-scanner -e -D sonar.projectKey=DeployBackend -D sonar.host.url=http://localhost:9000 -D sonar.login=81947f2400d09816b8129d565f763a8f275d32ad -D sonar.java.binaries=target -D sonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
        }
      }
    }
  }
}