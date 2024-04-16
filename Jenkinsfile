pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MAVEN3"
    }
    

    stages {

        stage('Build') {
            steps {

		    echo 'Running build ...'
                 // To run Maven on a Windows agent, use
                bat "mvn -Dmaven.test.failure.ignore=true clean package"

            }

	        post {
            // If Maven was able to run the tests, even if some of the test
            // failed, record the test results and archive the jar file.
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
                // Generate JaCoCo code coverage report
                bat 'mvn jacoco:report'

                // Assuming you want to archive the reports and add a post-build action
                script {
                    def jacocoReportPath = '**/target/site/jacoco/*.html'
                    // Archive the JaCoCo reports
                    archiveArtifacts artifacts: jacocoReportPath, fingerprint: true
                }
            }
        }

        
    }
}