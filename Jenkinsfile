pipeline {
  agent any

  options {
    buildDiscarder(logRotator(numToKeepStr: '3'))
 	disableConcurrentBuilds()
  }

  environment {
      CHAT_URL = credentials('URL_ID_SALA_CHAT')
  }
            
  stages{
    stage('Checkout') {
      steps{
        echo "------------>Checkout<------------"
        checkout scm
      }
    }
    
    stage('Compile & Unit Tests') {
      steps{
        echo "------------>Compile & Unit Tests<------------"
          sh 'chmod +x gradlew'
          sh './gradlew --b ./build.gradle clean test'
          junit 'build/test-results/test/*.xml'
      }
    }

    stage('Static Code Analysis') {
      steps{
        echo '------------>Análisis de código estático<------------'
        withSonarQubeEnv('SonarQube') {
          sh "${tool name: 'SonarScanner', type:'hudson.plugins.sonar.SonarRunnerInstallation'}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
        }
      }
    }

    stage('Build') {
      steps {
        echo "------------>Build<------------"
        sh './gradlew --b ./build.gradle build -x test'
      }
    } 
  }

  post {
    always {
      echo 'This will always run'
      googlechatnotification url: CHAT_URL, message: "Pipeline: ${currentBuild.projectName} \n Estado: ${currentBuild.currentResult} \n Detalles: ${env.BUILD_URL}"
    }
    success {
      echo 'This will run only if successful'
    }
    failure {
      echo 'This will run only if failed'
      //mail (to: 'yuliana.canas@ceiba.com.co',subject: "Failed Pipeline:${currentBuild.fullDisplayName}",body: "Something is wrong with ${env.BUILD_URL}")
      
    }
    unstable {
      echo 'This will run only if the run was marked as unstable'
      //mail (to: 'yuliana.canas@ceiba.com.co',subject: "Unstable Pipeline:${currentBuild.fullDisplayName}",body: "Something is wrong with ${env.BUILD_URL}")
    }
    changed {
      echo 'This will run only if the state of the Pipeline has changed'
      echo 'For example, if the Pipeline was previously failing but is now successful'
    }
  }
}
