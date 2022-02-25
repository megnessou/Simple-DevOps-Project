pipeline{
  agent any 
  tools { maven 'maven-3.8.4'}
  stages{
    stage('git-clone'){
      steps{
         checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'mySecrets', url: 'https://github.com/megnessou/Simple-DevOps-Project.git']]])
      }
    }
    stage('etech-hello'){
      steps{
        sh 'git version'
        sh 'mvn -v'
      }
    }
   stage('Build Artifact - Maven') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar'
      }
   }
   stage('Unit Tests - JUnit and JaCoCo') {
      steps {
        sh 'mvn test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }
    }
   stage('Mutation Tests - PIT') {
      steps {
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
      }
      post {
        always {
          pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
        }
      }
    }
    stage('CodeQuality-SAST'){
      steps{
        sh 'mvn clean verify sonar:sonar \
  -Dsonar.projectKey=etechspringapp \
  -Dsonar.host.url=http://etechlabs.eastus.cloudapp.azure.com:9000 \
  -Dsonar.login=01e6afa1885429be4fc842badeb6b471e94176e1'
      }
    }
  }    
}
