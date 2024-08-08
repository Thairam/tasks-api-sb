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
    stage ('Quality Gate') {
      steps {
        sleep(5)
        timeout(time: 1, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
    stage ('Deploy Backend') {
      steps {
        deploy adapters: [tomcat8(credentialsId: 'Tomcat_Login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
      }
    }
    stage ('API Test') {
      steps {
        dir('api-test') {
          git credentialsId: 'Github_login', url: 'https://github.com/Thairam/tasks-api-test'
          bat 'mvn test'
        }
      }
    }
    stage ('Deploy Frontend') {
      steps {
        dir('frontend') {
          git credentialsId: 'Github_login', url: 'https://github.com/Thairam/tasks-site'
          bat 'mvn clean package'
          deploy adapters: [tomcat8(credentialsId: 'Tomcat_Login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
        }        
      }
    }
    stage ('Functional Test') {
      steps {
        dir('functional-test') {
          git credentialsId: 'Github_login', url: 'https://github.com/Thairam/tasks-functional-tests'
          bat 'mvn test'
        }
      }
    }
    stage ('Deploy Prod') {
      steps {
        bat 'docker-compose build'
        bat 'docker-compose up -d'
      }
    }
  }
}