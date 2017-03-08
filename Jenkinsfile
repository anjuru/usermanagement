pipeline {
  agent any
  stages {
    stage('Initiate Build') {
      steps {
        echo 'Initiate Build'
      }
    }
    stage('Source Code Checkout') {
      steps {
        git(url: 'https://github.com/anjuru/usermanagement.git', branch: 'master', changelog: true, poll: true)
      }
    }
    
    stage('Unit Test') {
      steps {
        sh './gradlew test'
      }
    }
    
    stage('Run Sonar Qube') {
      steps {
        sh './gradlew sonarqube -Dsonar.host.url=http://localhost:9000/sonar'
      }
    }
    
    stage('Build Artifacts') {
      steps {
        sh './gradlew clean build'
      }
    }
    stage('Archive Artifacts') {
      steps {
        parallel(
          "Archive WAR": {
            archiveArtifacts 'build/**/*.war'
            
          },
          "Archive Test Results": {
            junit(testResults: 'build/**/TEST-*.xml', keepLongStdio: true)
            
          }
        )
      }
    }
    stage('Artifactory Publish') {
      steps {
        sh './gradlew publish'
      }
    }
  }
}
