pipeline {
    agent any

    tools {
        maven "MAVEN3"
         jdk 'JDK'
    }
    

    stages {

        stage('Build') {
            steps {

		    echo 'Running build ...'
                bat "mvn -Dmaven.test.failure.ignore=true clean package"

            }

	        post {
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
        
          stage("SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('SonarQubeServer') {
                bat 'mvn clean package sonar:sonar'
              }
            }
          }

          stage("Quality Gate") {
            steps {
              timeout(time: 2, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }

          }

          stage('Code Coverage') {
            steps {
                bat 'mvn jacoco:report'
                script {
                    def jacocoReportPath = '**/target/site/jacoco/*.html'
                    archiveArtifacts artifacts: jacocoReportPath, fingerprint: true
                }
            }
        }

         // New stages for deployment
        stage('Deploy to Dev Env') {
            steps {
                script {
                    echo 'Deploying to Development Environment...'
                    // Example deployment command, replace with actual deployment steps
                    bat 'scp target/*.jar user@dev-server:/path/to/deploy'
                    bat 'ssh user@dev-server "java -jar /path/to/deploy/your-app.jar"'
                }
            }
        }

        stage('Deploy to QAT Env') {
            steps {
                script {
                    echo 'Deploying to QAT Environment...'
                    bat 'scp target/*.jar user@qat-server:/path/to/deploy'
                }
            }
        }

        stage('Deploy to Staging Env') {
            steps {
                script {
                    echo 'Deploying to Staging Environment...'
                    bat 'scp target/*.jar user@staging-server:/path/to/deploy'
                }
            }
        }

        stage('Deploy to Production Env') {
            steps {
                script {
                    echo 'Deploying to Production Environment...'
                    bat 'scp target/*.jar user@production-server:/path/to/deploy'
                }
            }
        }

        
    }
}