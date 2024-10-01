pipeline {
  agent any

  stages {
    //---------------------------------------------- début stage -----------------------------------
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }  
    //----------------------------------------------

stage('Vulnerability Scan owasp - dependency-check') {
   steps {
	    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
     		sh "mvn dependency-check:check"
	    }
		}
}

 //-----------------------------------------------   
      stage('UNIT test & jacoco ') {
      steps {
        sh "mvn test"
      }
      }
      //----------------------------------------------
      stage('sonar maven ') {
      steps {
 withCredentials([string(credentialsId: 'TOKENSONAR', variable: 'TOKENSONAR')]) {
        sh "mvn clean verify sonar:sonar \
  -Dsonar.projectKey=maven-jenkins-pipeline \
  -Dsonar.projectName='maven-jenkins-pipeline' \
  -Dsonar.host.url=http://172.206.215.37:9999 \
  -Dsonar.token=${TOKENSONAR}"
 }
      }
      }
    
 

    //-------------------------------------------
      stage('Mutation Tests - PIT') {
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          sh "mvn org.pitest:pitest-maven:mutationCoverage"
        }
      }
    }
  //------------------------------
    }

        post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    jacoco execPattern: 'target/jacoco.exec'
                    pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
                   
                }
        
          }
}
