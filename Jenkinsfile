pipeline {
    agent any 
    tools {
        maven "maven3"
        jdk "OracleJDK8" 
    }

    environment {
         NEXUS_USER = "admin"
        NEXUS_PASS = "dino"
        NEXUS_IP = "172.31.80.247"
        NEXUS_PORT = "8081"
        RELEASE_REPO = "dino-release"
	    NEXUS_GRP_REPO   = "dino-grp-repo"
        NEXUS_LOGIN = "nexuslogin"
        CENTRAL_REPO = "dino-maven-central"
        SNAP_REPO = "dino-snapshot"
    }
    stages {
      stage( 'build'){
        steps {
            sh 'mvn -s settings.xml -Dskiptests'
        }
        post {
          sucess {
            echo "now archiving"
            archiveArtifacts artifacts: '**/*.war'
          }
        }
      }
      stage('test'){
        steps {
          sh 'mvn -s setting.xml test'
        }
      }
      stage('sonar Analysis') {
        environment {
          scannerHome =tool "${SONARSCANNER}"
        }
        steps {
          withSonarQubeEnv("${SONARSERVER}") {
            sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=blaisejenkins \
                   -Dsonar.projectName=blaisejenkins \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
          }
        }
      }
          stage("uploadArtifact") {
            steps{
              nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: "${RELEASE_REPO}",
                    credentialsId: "{NEXUS_LOGIN}",
                    artifacts: [
                        [artifactId: 'vproapp' ,
                        classifier: '' ,
                        file: 'target/vprofile-v2.war' ,
                        type: 'war']
                    ]
                )            
            }
          }    
        
    }
}